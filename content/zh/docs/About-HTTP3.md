---
title: "关于 HTTP/3"
weight: 13
---

## 关于 HTTP/3

随着众多网站和 CDN 的支持，基于 QUIC 的 HTTP/3 正越来越流行。长远来看 UDP 将会替代 TCP 成为承载 Web 流量的主流协议。

### 新的挑战

Hysteria 目前是通过修改了拥塞控制的 QUIC 转发 TCP 流量，避免 Web 服务端与客户端通过各自系统的 TCP 栈直接通信，实现加速。 Hysteria 的设计目标是最大化数据吞吐量，而非通过冗余发包来降低应用层感知的丢包率本身（这两个目标往往是互斥的）。因此虽然 Hysteria 支持 UDP 转发，其并不会降低丢包率。 **换句话说，目前用 Hysteria 代理 HTTP/3 这样的 QUIC 流量时不会有额外加速效果，传输速度只能由 Web 服务器与浏览器内置的 QUIC 栈的拥塞控制自己决定（通常是 Cubic, Reno 或者 BBR）。** 由于 QUIC 把所有 TCP 里原本是明文的控制信息 (Seq, ACK, Window Size 这类) 都加密了，搞一个类似 TCP 透明代理的机制来替代两边原本的拥塞控制也是不可能的。

QUIC 流量对于代理这样的中间人来说，能做的只有原封不动转发这些加密的 UDP 包。

### 目前解决方案

目前所有的网站与 App 都只是把 QUIC 作为一个升级选项。如果网络环境不支持（比如 UDP 根本不通）则会回退到 TCP 的 HTTP/2 或者更低版本。**要保留 Hysteria 的加速效果，请确保你用的是 HTTP/2 或以下。**

如果你是在桌面环境用 Chrome, Firefox 这样的浏览器配合 HTTP/SOCKS5 代理，那么浏览器其实已经自动禁用 HTTP/3 了，因为 HTTP 代理无法支持 UDP 转发，而 SOCKS5 代理虽然理论上支持 UDP，但 Chrome 和 Firefox 并没有实现。

如果你在手机上用 Shadowrocket, SagerNet 这样的 VPN 客户端（或者桌面环境上用 tun 模式），建议通过以下方式之一手动禁用 HTTP/3：

- Chrome: 打开 `chrome://flags/` 找到 `Experimental QUIC protocol` 选项，切换到 `Disable`
- Firefox: 打开 `about:config` 找到 `network.http.http3.enabled` 选项，切换到 `false`
- 通过规则屏蔽 UDP 443 端口 (在 [ACL]({{< ref "ACL.md" >}}) 中添加规则 `block all udp/443`)

### 其他解决方案

目前能想到的办法只有添加 FEC (Forward Error Correction) 来降低感知的 UDP 丢包率来 "讨好" 应用层 QUIC 的拥塞控制。由于 Hysteria 目前用于转发 TCP 流量的拥塞控制已经足够好，添加 FEC 反而会因冗余数据减速，计划 FEC 将只用于 UDP 转发。

可能会开发基于丢包率动态调整 FEC 冗余比例的机制，避免在线路良好时因冗余数据降低吞吐。