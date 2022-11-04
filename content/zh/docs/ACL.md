---
title: "ACL"
weight: 8
---

ACL 文件描述如何处理传入请求。服务器和客户端都支持，并且遵循相同的语法（但客户端只有 SOCKS5/HTTP 模式支持 ACL，其余模式会全部走代理）。

### 语法

```
<处理方式> <条件类型> <条件表达式> <可选: 协议/端口> <可选: 处理方式参数>
```

4 种处理方式:

`direct` - 直接连接到目标服务器，不经过代理

`proxy` - 通过代理连接到目标服务器（仅在客户端上可用）

`block` - 拒绝连接建立

`hijack` - 把连接劫持到另一个目的地 （必须在参数中指定）

6 种条件类型:

`domain` - 匹配特定的域名（不匹配子域名！例如：`apple.com` 不匹配 `cdn.apple.com`）

`domain-suffix` - 匹配域名后缀（包含子域名，但 `apple.com` 仍不会匹配 `fakeapple.com`）

`cidr` - IPv4 / IPv6 CIDR

`ip` - IPv4 / IPv6 地址

`country` - 匹配国家 IP，ISO 两位字母国家代码

`all` - 匹配所有地址 （通常放在文件尾作为默认规则）

对于域名请求，Hysteria 将尝试解析域名并同时匹配域名规则和 IP 规则。换句话说，IP 规则能覆盖到所有连接，无论客户端是用 IP 还是域名请求。

协议/端口是可选的，可以用来匹配特定协议和端口的请求。比如要匹配 TCP 80 端口，写 `tcp/80`。要匹配 UDP 12450 端口，写 `udp/12450`。要同时匹配 TCP 和 UDP 的 443 端口，写 `*/443`。要匹配所有 UDP 端口，写 `udp/*`。要匹配所有连接，可直接省略这个字段，或者写 `*` 或者 `*/*`。

只有特定的处理方式才需要处理方式参数。目前只有 `hijack` 需要参数（来指定劫持到的目标地址）。

Hysteria 根据文件中第一个匹配到规则对每个请求进行操作。当没有匹配时默认的行为是代理连接。可以通过在文件的末尾添加一个规则加上条件 "all" 来设置默认行为。

### 样例

```
# 注释用 # 开头

# 直连 "evil.corp" 域名
direct domain evil.corp

# 代理 "google.com" 以及 "*.google.com" 域名
proxy domain-suffix google.com

# 屏蔽 IP 1.2.3.4
block ip 1.2.3.4

# 屏蔽所有中国 IP
block country cn

# 劫持日本 IP 的 UDP 53 端口到 8.8.8.8
hijack country jp udp/53 8.8.8.8

# 劫持局域网 IP 到 127.0.0.1
hijack cidr 192.168.1.1/24 127.0.0.1

# 代理所有 HTTP 协议
proxy all tcp/80

# 代理所有 HTTPS 协议
proxy all tcp/443

# 屏蔽一切没有匹配到上面规则的请求
block all
```
