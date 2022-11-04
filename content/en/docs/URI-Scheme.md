---
title: "URI Scheme"
weight: 9
---

Third party clients looking to implement a "share by link" feature are advised to follow the following URI scheme 
(initially introduced by Shadowrocket):

    hysteria://host:port?protocol=udp&auth=123456&peer=sni.domain&insecure=1&upmbps=100&downmbps=100&alpn=hysteria&obfs=xplus&obfsParam=123456#remarks

    - host: hostname or IP address of the server to connect to (required)
    - port: port of the server to connect to (required)
    - protocol: protocol to use ("udp", "wechat-video", "faketcp") (optional, default: "udp")
    - auth: authentication payload (string) (optional)
    - peer: SNI for TLS (optional)
    - insecure: ignore certificate errors (optional)
    - upmbps: upstream bandwidth in Mbps (required)
    - downmbps: downstream bandwidth in Mbps (required)
    - alpn: QUIC ALPN (optional)
    - obfs: Obfuscation mode (optional, empty or "xplus")
    - obfsParam: Obfuscation password (optional)
    - remarks: remarks (optional)
