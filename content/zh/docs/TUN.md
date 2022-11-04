---
title: "TUN 模式"
weight: 4
---

### Linux

用以下命令创建一个 tun 接口 `tun0`，并分配一个 IP 地址：

```bash
ip tuntap add mode tun dev tun0
ip addr add 198.18.0.1/15 dev tun0
ip link set dev tun0 up
```

修改路由表，让 `tun0` 接收流量。假设 `eth0` 是原本的网卡，网关地址为 `172.17.0.1`。


```bash
ip route del default
ip route add default via 198.18.0.1 dev tun0 metric 1
ip route add default via 172.17.0.1 dev eth0 metric 10
```

用以下配置启动 hysteria：

```json
  // ...
  "tun": {
    "name": "tun0",
    // ...
  },
  // ...
```

注意：有时需要禁用 `rp_filter` 才能让一个网卡接收来自其他网卡的流量。


```bash
sysctl net.ipv4.conf.all.rp_filter=0
sysctl net.ipv4.conf.eth0.rp_filter=0
```

### macOS

macOS 上可以通过直接启动 hysteria 让其自动创建一个 tun 接口。

```json
  // ...
  "tun": {
    "name": "utun123",
    // ...
  },
  // ...
```

用 `ifconfig` 启用这个接口并分配 IP。

```bash
sudo ifconfig utun123 198.18.0.1 198.18.0.1 up
```

添加路由，接收流量。

```bash
sudo route add -net 1.0.0.0/8 198.18.0.1
sudo route add -net 2.0.0.0/7 198.18.0.1
sudo route add -net 4.0.0.0/6 198.18.0.1
sudo route add -net 8.0.0.0/5 198.18.0.1
sudo route add -net 16.0.0.0/4 198.18.0.1
sudo route add -net 32.0.0.0/3 198.18.0.1
sudo route add -net 64.0.0.0/2 198.18.0.1
sudo route add -net 128.0.0.0/1 198.18.0.1
sudo route add -net 198.18.0.0/15 198.18.0.1
```

### Windows

Windows 上必须先下载 [wintun](https://www.wintun.net/)，把 `wintun.dll` 放到 hysteria 工作目录（通常是 exe 所在目录）。

```json
  // ...
  "tun": {
    "name": "wintun",
    // ...
  },
  // ...
```

和 macOS 一样，hysteria 会自动创建对应名字的接口。但依然需要手动分配 IP。

```bash
netsh interface ip set address name="wintun" source=static addr=192.168.123.1 mask=255.255.255.0 gateway=none
```

添加路由，接收流量（需要绕开 hysteria 服务器地址避免回环）

```bash
route add 0.0.0.0 mask 0.0.0.0 192.168.123.1 if <IF NUM> metric 5
route add <server ip> mask 255.255.255.255 <primary gateway ip>
```