---
title: "高级用法"
weight: 3
---

### 服务器

```json
{
  "listen": ":36712", // 监听地址
  "protocol": "faketcp", // "udp", "wechat-video", "faketcp" 留空默认 "udp"
  "acme": {
    "domains": [
      "your.domain.com",
      "another.domain.net"
    ], // ACME 证书域名
    "email": "your@email.com", // 注册邮箱，可选，推荐
    "disable_http": false, // 禁用 HTTP 验证方式
    "disable_tlsalpn": false, // 禁用 TLS-ALPN 验证方式
    "alt_http_port": 8080, // HTTP 验证方式替代端口
    "alt_tlsalpn_port": 4433 // TLS-ALPN 验证方式替代端口
  },
  "cert": "/home/ubuntu/my_cert.crt", // 证书
  "key": "/home/ubuntu/my_key.crt", // 证书密钥
  "up": "100 Mbps", // 单客户端最大上传速度，和 "up_mbps" 二选一
  "up_mbps": 100, // 单客户端最大上传速度 Mbps
  "down": "100 Mbps", // 单客户端最大下载速度，和 "down_mbps" 二选一
  "down_mbps": 100, // 单客户端最大下载速度 Mbps
  "disable_udp": false, // 禁用 UDP 转发
  "acl": "my_list.acl", // 见 ACL 页
  "mmdb": "GeoLite2-Country.mmdb", // MaxMind IP 库 (ACL)
  "obfs": "AMOGUS", // 混淆密码
  "auth": { // 验证
    "mode": "passwords", // 验证模式，目前支持 "none", "passwords", "external"。关于 external 见 外部验证接入 页面
    "config": ["yubiyubi", "random_password2", "Мать-Россия"]
  },
  "alpn": "ayaya", // QUIC TLS ALPN, 必须和客户端一致
  "prometheus_listen": ":8080", // Prometheus 统计接口监听地址 (在 /metrics)
  "recv_window_conn": 15728640, // QUIC stream receive window
  "recv_window_client": 67108864, // QUIC connection receive window
  "max_conn_client": 4096, // 单客户端最大活跃连接数
  "disable_mtu_discovery": false, // 禁用 MTU 探测 (RFC 8899)
  "resolver": "udp://1.1.1.1:53", // DNS 地址
  "resolve_preference": "64", // DNS IPv4/IPv6 优先级。可用选项 "64" (IPv6 优先，可回落到 IPv4) "46" (IPv4 优先，可回落到 IPv6) "6" (仅 IPv6) "4" (仅 IPv4)
  "socks5_outbound": {
    "server": "55.66.77.88:1080", // SOCKS5 服务器地址
    "user": "user", // SOCKS5 验证用户名
    "password": "password" // SOCKS5 验证密码
  },
  "bind_outbound": {
    "address": "5.6.7.8", // 绑定地址
    "device": "eth0" // 绑定设备 (通常需要 root)
  }
}
```

#### 速度

格式： `[整数] [单位]` 如 `100 Mbps`, `640 KBps`, `2 Gbps`

支持的单位 **(区分大小写, b 为比特 B 为字节 8b=1B)**:
- bps (bits per second)
- Bps (bytes per second)
- Kbps (kilobits per second)
- KBps (kilobytes per second)
- Mbps (megabits per second)
- MBps (megabytes per second)
- Gbps (gigabits per second)
- GBps (gigabytes per second)
- Tbps (terabits per second)
- TBps (terabytes per second)

#### ACME

目前仅支持 HTTP 与 TLS-ALPN 验证方式，不支持 DNS 验证。对于两种方式请分别确保 TCP 80/443 端口能够被访问。

#### Prometheus 流量统计

通过 `prometheus_listen` 选项可以让 Hysteria 暴露一个 Prometheus HTTP 客户端 endpoint 用来统计流量使用情况。
例如如果配置在 8080 端口，则 API 地址是 `http://example.com:8080/metrics`

```text
hysteria_active_conn{auth="55m95auW5oCq"} 32
hysteria_active_conn{auth="aGFja2VyISE="} 7

hysteria_traffic_downlink_bytes_total{auth="55m95auW5oCq"} 122639
hysteria_traffic_downlink_bytes_total{auth="aGFja2VyISE="} 3.225058e+06

hysteria_traffic_uplink_bytes_total{auth="55m95auW5oCq"} 40710
hysteria_traffic_uplink_bytes_total{auth="aGFja2VyISE="} 37452
```

`auth` 是客户端发来的验证密钥，经过 Base64 编码。

#### DNS

Hysteria 支持多种 DNS 协议。

- 标准 UDP/TCP: `udp://host`, `tcp://host`, `udp://host:port`, `tcp://host:port`
- DNS over HTTPS: `https://url`
- DNS over TLS: `tls://host`, `tls://host:port`
- DNS over QUIC: `quic://host:port`

当没有指定协议，且地址只是一个 IP 地址时，默认其为在 53 端口的标准 UDP DNS 服务器。

### 客户端

```json
{
  "server": "example.com:36712", // 服务器地址
  "protocol": "faketcp", // "udp", "wechat-video", "faketcp" 留空默认 "udp"
  "up": "10 Mbps", // 最大上传速度，和 "up_mbps" 二选一
  "up_mbps": 10, // 最大上传速度 Mbps
  "down": "50 Mbps", // 最大下载速度，和 "down_mbps" 二选一
  "down_mbps": 50, // 最大下载速度 Mbps
  "retry": 3, // 启动时连接服务器的重试次数。默认为 0 不重试，负数为无限重试
  "retry_interval": 5, // 重试间隔，单位为秒
  "quit_on_disconnect": false, // 连接断开时退出程序
  "handshake_timeout": 10, // 握手超时，单位为秒
  "idle_timeout": 60, // 空闲超时，单位为秒。客户端会以这个值的 2/5 作为发送心跳包的间隔
  "hop_interval": 120, // 端口跳跃间隔，单位为秒。见 端口跳跃 页面
  "socks5": {
    "listen": "127.0.0.1:1080", // SOCKS5 监听地址
    "timeout": 300, // TCP 超时秒数
    "disable_udp": false, // 禁用 UDP 转发
    "user": "me", // SOCKS5 验证用户名
    "password": "lmaolmao" // SOCKS5 验证密码
  },
  "http": {
    "listen": "127.0.0.1:8080", // HTTP 监听地址
    "timeout": 300, // TCP 超时秒数
    "user": "me", // HTTP 验证用户名
    "password": "lmaolmao", // HTTP 验证密码
    "cert": "/home/ubuntu/my_cert.crt", // 证书 (变为 HTTPS 代理)
    "key": "/home/ubuntu/my_key.crt" // 证书密钥 (变为 HTTPS 代理)
  },
  "tun": {
    "name": "tun-hy", // TUN 接口名称
    "timeout": 300, // 超时秒数
    "mtu": 1500, // MTU 值
    "tcp_sndbuf": "4m", // TCP 发送 buffer 大小 (字符串，如 4096, 4k, 2m)
    "tcp_rcvbuf": "4m", // TCP 接收 buffer 大小 (字符串，如 4096, 4k, 2m)
    "tcp_autotuning": true, // 启用 TCP 接收 buffer 自动调整
  },
  "relay_tcps": [
    {
      "listen": "127.0.0.1:2222", // TCP 转发监听地址
      "remote": "123.123.123.123:22", // TCP 转发目标地址
      "timeout": 300 // TCP 超时秒数
    },
    {
      "listen": "127.0.0.1:13389", // TCP 转发监听地址
      "remote": "124.124.124.124:3389", // TCP 转发目标地址
      "timeout": 300 // TCP 超时秒数
    }
  ],
  "relay_udps": [
    {
      "listen": "127.0.0.1:5333", // UDP 转发监听地址
      "remote": "8.8.8.8:53", // UDP 转发目标地址
      "timeout": 60 // UDP 超时秒数
    },
    {
      "listen": "127.0.0.1:11080", // UDP 转发监听地址
      "remote": "9.9.9.9.9:1080", // UDP 转发目标地址
      "timeout": 60 // UDP 超时秒数
    }
  ],
  "tproxy_tcp": {
    "listen": ":9000", // TCP 透明代理监听地址
    "timeout": 300 // TCP 超时秒数
  },
  "tproxy_udp": {
    "listen": ":9000", // UDP 透明代理监听地址
    "timeout": 60 // UDP 超时秒数
  },
  "redirect_tcp": {
    "listen": "127.0.0.1:9500", // TCP 重定向监听地址
    "timeout": 300 // TCP 超时秒数
  },
  "acl": "my_list.acl", // 见 ACL 页
  "mmdb": "GeoLite2-Country.mmdb", // MaxMind IP 库 (ACL)
  "obfs": "AMOGUS", // 混淆密码
  "auth": "[BASE64]", // Base64 验证密钥
  "auth_str": "yubiyubi", // 字符串验证密钥，和上面的选项二选一
  "alpn": "ayaya", // QUIC TLS ALPN, 必须和服务端一致
  "server_name": "real.name.com", // 用于验证服务端证书的 hostname
  "insecure": false, // 忽略一切证书错误 
  "ca": "my.ca", // 自定义 CA
  "recv_window_conn": 15728640, // QUIC stream receive window
  "recv_window": 67108864, // QUIC connection receive window
  "disable_mtu_discovery": false, // 禁用 MTU 探测 (RFC 8899)
  "resolver": "udp://1.1.1.1:53", // DNS 地址
  "resolve_preference": "64" // DNS IPv4/IPv6 优先级。可用选项 "64" (IPv6 优先，可回落到 IPv4) "46" (IPv4 优先，可回落到 IPv6) "6" (仅 IPv6) "4" (仅 IPv4)
}
```

#### 伪装 TCP (faketcp 模式)

某些网络可能限制或者完全屏蔽 UDP 流量。Hysteria 提供了一个 "faketcp" 模式，让服务端与客户端之间用看起来是 TCP 但实际不走
系统 TCP 栈的方式通信。通过这种方式可以让防火墙、QoS 设备认为这是真的 TCP 连接，绕过对 UDP 的限制。

目前只在 Linux 上支持（客户端和服务器都是），并且需要 root 权限。

如果你的服务器有防火墙，请放行相应的 TCP 端口而不是 UDP。

#### 透明代理

TPROXY 和 REDIRECT 模式 (`tproxy_tcp`, `tproxy_udp`, `redirect_tcp`) 只在 Linux 下可用。

参考阅读：
- https://www.kernel.org/doc/Documentation/networking/tproxy.txt
- https://powerdns.org/tproxydoc/tproxy.md.html
- https://v2.gost.run/redirect/
- https://www.linuxtopia.org/Linux_Firewall_iptables/x4508.html