---
title: "Advanced Usage"
weight: 3
---

### Server

```json
{
  "listen": ":36712", // Listen address
  "protocol": "faketcp", // Blank, "udp", "wechat-video", "faketcp"
  "acme": {
    "domains": [
      "your.domain.com",
      "another.domain.net"
    ], // Domains for the ACME cert
    "email": "your@email.com", // Registration email, optional but recommended
    "disable_http": false, // Disable HTTP challenges
    "disable_tlsalpn": false, // Disable TLS-ALPN challenges
    "alt_http_port": 8080, // Alternate port for HTTP challenges
    "alt_tlsalpn_port": 4433 // Alternate port for TLS-ALPN challenges
  },
  "cert": "/home/ubuntu/my_cert.crt", // Cert file, mutually exclusive with the ACME options above
  "key": "/home/ubuntu/my_key.crt", // Key file, mutually exclusive with the ACME options above
  "up": "100 Mbps", // Max upload speed per client, mutually exclusive with "up_mbps" below
  "up_mbps": 100, // Max upload Mbps per client
  "down": "100 Mbps", // Max download speed per client, mutually exclusive with "down_mbps" below
  "down_mbps": 100, // Max download Mbps per client
  "disable_udp": false, // Disable UDP support
  "acl": "my_list.acl", // See ACL page
  "mmdb": "GeoLite2-Country.mmdb", // MaxMind database for ACL country lookups
  "obfs": "AMOGUS", // Obfuscation password
  "auth": { // Authentication
    "mode": "passwords", // Mode, supports "none" "passwords" and "external". See external authentication page for details
    "config": ["yubiyubi", "random_password2", "Мать-Россия"]
  },
  "alpn": "ayaya", // QUIC TLS ALPN
  "prometheus_listen": ":8080", // Prometheus HTTP metrics server listen address (at /metrics)
  "recv_window_conn": 15728640, // QUIC stream receive window
  "recv_window_client": 67108864, // QUIC connection receive window
  "max_conn_client": 4096, // Max concurrent connections per client
  "disable_mtu_discovery": false, // Disable Path MTU Discovery (RFC 8899)
  "resolver": "udp://1.1.1.1:53", // DNS resolver address
  "resolve_preference": "64", // DNS IPv4/IPv6 preference. Available options: "64" (IPv6 first, fallback to IPv4), "46" (IPv4 first, fallback to IPv6), "6" (IPv6 only), "4" (IPv4 only)
  "socks5_outbound": {
    "server": "55.66.77.88:1080", // SOCKS5 proxy server
    "user": "user", // SOCKS5 proxy username
    "password": "password" // SOCKS5 proxy password
  },
  "bind_outbound": {
    "address": "5.6.7.8", // Bind address for outbound connections
    "device": "eth0" // Bind device for outbound connections (usually requires root)
  }
}
```

#### Speed

Format: `[Integer] [Unit]` e.g. `100 Mbps`, `640 KBps`, `2 Gbps`

Supported units **(case sensitive, b = bits, B = bytes, 8b=1B)**:
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

Only HTTP and TLS-ALPN challenges are currently supported (no DNS challenges). Make sure your TCP ports 80/443 are
accessible respectively.

#### Prometheus Metrics

You can make Hysteria expose a Prometheus HTTP client endpoint for monitoring traffic usage with `prometheus_listen`.
If configured on port 8080, the endpoint would be at `http://example.com:8080/metrics`.

```text
hysteria_active_conn{auth="55m95auW5oCq"} 32
hysteria_active_conn{auth="aGFja2VyISE="} 7

hysteria_traffic_downlink_bytes_total{auth="55m95auW5oCq"} 122639
hysteria_traffic_downlink_bytes_total{auth="aGFja2VyISE="} 3.225058e+06

hysteria_traffic_uplink_bytes_total{auth="55m95auW5oCq"} 40710
hysteria_traffic_uplink_bytes_total{auth="aGFja2VyISE="} 37452
```

`auth` is the auth payload sent by the clients, encoded in Base64.

#### DNS

Hysteria supports a variety of DNS protocols.

- Standard UDP/TCP: `udp://host`, `tcp://host`, `udp://host:port`, `tcp://host:port`
- DNS over HTTPS: `https://url`
- DNS over TLS: `tls://host`, `tls://host:port`
- DNS over QUIC: `quic://host:port`

When no scheme is provided and the address is just an IP address, it wil be assumed to be a UDP DNS server on port 53.

### Client

```json
{
  "server": "example.com:36712", // Server address
  "protocol": "faketcp", // Blank, "udp", "wechat-video", "faketcp"
  "up": "10 Mbps", // Max upload speed, mutually exclusive with "up_mbps" below
  "up_mbps": 10, // Max upload Mbps
  "down": "50 Mbps", // Max download speed, mutually exclusive with "down_mbps" below
  "down_mbps": 50, // Max download Mbps
  "retry": 3, // Number of retries when unable to connect to the server at startup. 0 (default) = no retry. Negative value = infinite retries.
  "retry_interval": 5, // Retry interval in seconds
  "quit_on_disconnect": false, // Quit the client when disconnected from the server
  "handshake_timeout": 10, // Handshake timeout in seconds
  "idle_timeout": 60, // Idle timeout in seconds. The client will send a ping to the server every 2/5 of this value.
  "socks5": {
    "listen": "127.0.0.1:1080", // SOCKS5 listen address
    "timeout": 300, // TCP timeout in seconds
    "disable_udp": false, // Disable UDP support
    "user": "me", // SOCKS5 authentication username
    "password": "lmaolmao" // SOCKS5 authentication password
  },
  "http": {
    "listen": "127.0.0.1:8080", // HTTP listen address
    "timeout": 300, // TCP timeout in seconds
    "user": "me", // HTTP authentication username
    "password": "lmaolmao", // HTTP authentication password
    "cert": "/home/ubuntu/my_cert.crt", // Cert file (HTTPS proxy)
    "key": "/home/ubuntu/my_key.crt" // Key file (HTTPS proxy)
  },
  "tun": {
    "name": "tun-hy", // TUN interface name
    "timeout": 300, // Timeout in seconds
    "mtu": 1500, // MTU size
    "tcp_sndbuf": "4m", // TCP send buffer size, in a human-readable string representation (e.g. 4096, 4k, 2m)
    "tcp_rcvbuf": "4m", // TCP receive buffer size, in a human-readable string representation (e.g. 4096, 4k, 2m)
    "tcp_autotuning": true, // Enable TCP receive buffer auto-tuning
  },
  "relay_tcps": [
    {
      "listen": "127.0.0.1:2222", // TCP relay listen address
      "remote": "123.123.123.123:22", // TCP relay remote address
      "timeout": 300 // TCP timeout in seconds
    },
    {
      "listen": "127.0.0.1:13389", // TCP relay listen address
      "remote": "124.124.124.124:3389", // TCP relay remote address
      "timeout": 300 // TCP timeout in seconds
    }
  ],
  "relay_udps": [
    {
      "listen": "127.0.0.1:5333", // UDP relay listen address
      "remote": "8.8.8.8:53", // UDP relay remote address
      "timeout": 60 // UDP session timeout in seconds
    },
    {
      "listen": "127.0.0.1:11080", // UDP relay listen address
      "remote": "9.9.9.9.9:1080", // UDP relay remote address
      "timeout": 60 // UDP session timeout in seconds
    }
  ],
  "tproxy_tcp": {
    "listen": ":9000", // TCP TProxy listen address
    "timeout": 300 // TCP timeout in seconds
  },
  "tproxy_udp": {
    "listen": ":9000", // UDP TProxy listen address
    "timeout": 60 // UDP session timeout in seconds
  },
  "redirect_tcp": {
    "listen": "127.0.0.1:9500", // TCP Redirect listen address
    "timeout": 300 // TCP timeout in seconds
  },
  "acl": "my_list.acl", // See ACL page
  "mmdb": "GeoLite2-Country.mmdb", // MaxMind database for ACL country lookups
  "obfs": "AMOGUS", // Obfuscation password
  "auth": "[BASE64]", // Authentication payload in Base64
  "auth_str": "yubiyubi", // Authentication payload in string, mutually exclusive with the option above
  "alpn": "ayaya", // QUIC TLS ALPN
  "server_name": "real.name.com", // TLS hostname used to verify the server certificate
  "insecure": false, // Ignore all certificate errors 
  "ca": "my.ca", // Custom CA file
  "recv_window_conn": 15728640, // QUIC stream receive window
  "recv_window": 67108864, // QUIC connection receive window
  "disable_mtu_discovery": false, // Disable Path MTU Discovery (RFC 8899)
  "resolver": "udp://1.1.1.1:53", // DNS resolver address
  "resolve_preference": "64" // DNS IPv4/IPv6 preference. Available options: "64" (IPv6 first, fallback to IPv4), "46" (IPv4 first, fallback to IPv6), "6" (IPv6 only), "4" (IPv4 only)
}
```

#### Fake TCP / TCP masquerade

Certain networks may impose various restrictions on UDP traffic or block it altogether. Hysteria offers a "faketcp" mode
that allows servers and clients to communicate using a protocol that looks like TCP but does not actually go through the
system TCP stack. This tricks whatever middleboxes into thinking it's actually TCP traffic, rendering UDP-specific
restrictions useless.

This mode is currently only supported on Linux (both client and server) and requires root privileges.

If your server is behind a firewall, open the corresponding TCP port instead of UDP.

#### Transparent proxy

TPROXY and REDIRECT modes (`tproxy_tcp`, `tproxy_udp`, `redirect_tcp`) are only available on Linux.

References:
- https://www.kernel.org/doc/Documentation/networking/tproxy.txt
- https://powerdns.org/tproxydoc/tproxy.md.html
- https://v2.gost.run/en/redirect/
- https://www.linuxtopia.org/Linux_Firewall_iptables/x4508.html