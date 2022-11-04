---
title: "TUN"
weight: 4
---

### Linux

Create a TUN interface `tun0` and assign an IP address for it.

```bash
ip tuntap add mode tun dev tun0
ip addr add 198.18.0.1/15 dev tun0
ip link set dev tun0 up
```

Configure the default route table with different metrics. Let's say the primary interface is `eth0` and gateway is `172.17.0.1`.

```bash
ip route del default
ip route add default via 198.18.0.1 dev tun0 metric 1
ip route add default via 172.17.0.1 dev eth0 metric 10
```

Start hysteria with the following configuration:

```json
  // ...
  "tun": {
    "name": "tun0",
    // ...
  },
  // ...
```

Note: sometimes we need to disable `rp_filter` for the corresponding interface so that it can receive packets from other interfaces.

```bash
sysctl net.ipv4.conf.all.rp_filter=0
sysctl net.ipv4.conf.eth0.rp_filter=0
```

### macOS

On macOS, we need to start hysteria first so that it will create a TUN interface for us.

```json
  // ...
  "tun": {
    "name": "utun123",
    // ...
  },
  // ...
```

Then use `ifconfig` to bring the TUN interface up and assign addresses for it.

```bash
sudo ifconfig utun123 198.18.0.1 198.18.0.1 up
```

Add these specific routes so that hysteria can handle primary connections.

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

To use it on Windows, you must download [wintun](https://www.wintun.net/) and put `wintun.dll` in the working directory. (Usually the same directory where the hysteria executable is located.)

```json
  // ...
  "tun": {
    "name": "wintun",
    // ...
  },
  // ...
```

Same as macOS version, but we don't need to bring up the interface by hand, the only thing we need is to assign an IP address to it.

```bash
netsh interface ip set address name="wintun" source=static addr=192.168.123.1 mask=255.255.255.0 gateway=none
```

Then route default traffic to TUN interface and make proxy server ip as an exception.

```bash
route add 0.0.0.0 mask 0.0.0.0 192.168.123.1 if <IF NUM> metric 5
route add <server ip> mask 255.255.255.255 <primary gateway ip>
```