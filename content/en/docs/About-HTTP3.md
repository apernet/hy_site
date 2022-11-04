---
title: "About HTTP/3"
weight: 13
---

## About HTTP/3

QUIC-based HTTP/3 is gaining popularity with the support of many websites and CDNs. In the long run, UDP may replace TCP as the main protocol for Web traffic.

### A New Challenge

Hysteria currently forwards TCP traffic through QUIC with modified congestion control. It is designed with the goal of maximizing data throughput, not reducing packet loss. So while Hysteria supports UDP forwarding, it doesn't help with packet loss directly. **In other words, there is currently no "acceleration effects" when using Hysteria to proxy QUIC traffic such as HTTP/3. The transmission speed will be solely determined by the web server and the browser's built-in congestion control of the QUIC stack (typically Cubic, Reno or BBR).** Since QUIC encrypts all transmission control information that would otherwise be in plaintext (Seq, ACK, Window Size, etc.), it is also not possible to have something like a TCP transparent proxy to replace the original congestion control on both sides.

For middlemen like proxy servers, all they can do is to forward the encrypted UDP packets unchanged.

### Current Solutions

Currently, all websites and apps use QUIC only as an "upgrade" option. If the network doesn't support it (e.g. UDP is blocked), it will fall back to HTTP/2 or lower (which uses TCP). **So we strongly recommend that you use HTTP/2 or below.**

If you are using Chrome or Firefox with HTTP/SOCKS5 proxy on a desktop PC, then the browser has actually disabled HTTP/3 automatically because HTTP proxy cannot support UDP forwarding. As for SOCKS5, while it theoretically supports UDP, it is not implemented in Chrome and Firefox.

If you are using a VPN client like Shadowrocket, SagerNet on your phone (or tun mode on your desktop), it is recommended to disable HTTP/3 manually by one of the following methods.

- Chrome: Open `chrome://flags/`, find `Experimental QUIC protocol` and switch to `Disable`.
- Firefox: Open `about:config`, find `network.http.http3.enabled` and toggle it to `false`.
- Block UDP port 443 by ACL (add `block all udp/443` to [ACL]({{< ref "ACL.md" >}})).

### Other Potential Solutions

The only solution I can think of right now is to add FEC (Forward Error Correction), to reduce the perceived UDP packet loss rate to "appease" the application layer QUIC congestion control. Since Hysteria's current congestion control for forwarding TCP traffic is good enough, adding FEC would actually slow it down. So FEC will be used for UDP only.

Mechanisms may be developed to dynamically adjust the FEC redundancy ratio based on packet loss rates, to avoid sending redundant data when the connection is good enough.
