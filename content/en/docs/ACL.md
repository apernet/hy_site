---
title: "ACL"
weight: 8
---

ACL allows you to customize how incoming connections are handled. It's available for both servers and clients.

For clients, ACL only works in HTTP and SOCKS5 modes. In other modes, all connections will go through the proxy regardless of the rules.

### Syntax

```
<action> <condition type> <condition expression> <optional: protocol/port> <optional: action argument>
```

There are 4 types of actions:

`direct` - connect directly to the target server without going through the proxy

`proxy` - connect to the target server through the proxy (only available on the client)

`block` - block the connection from establishing

`hijack` - hijack the connection to another target address (must be specified in the action argument)

And 6 types of conditions:

`domain` - match a specific domain (does NOT match subdomains! e.g. `apple.com` will not match `cdn.apple.com`)

`domain-suffix` - match a domain suffix (match subdomains, but `apple.com` will still not match `fakeapple.com`)

`cidr` - IPv4 or IPv6 CIDR

`ip` - IPv4 or IPv6 address

`country` - match IP by ISO 3166-1 alpha-2 country code

`all` - match anything, doesn't need a condition expression (usually placed at the end of the file as a default rule)

For domain requests, Hysteria will try to resolve the domains and match both domain & IP rules. In other words, an IP
rule covers all connections that would end up connecting to this IP, regardless of whether the client requests with IP
or domain.

`protocol/port` is optional and can be used to match only specific protocols and/or ports. To match connections to TCP port 80, use `tcp/80`. To match connections to UDP port 12450, use `udp/12450`. To match connections to both TCP and UDP port 443, use `*/443`. To match all UDP connections, use `udp/*`. To match everything, you can simply omit this field, use `*` or `*/*`.

`action argument` is optional and only needed for certain actions. Currently, only `hijack` needs an argument (for the target address that you want to hijack the connections to).

Hysteria always handles a connection according to the first matching rule. When there is no match, the default
behavior is to proxy it. You can override this by adding a rule at the end of the file with the condition
`all`.

### Example

```
# Comments start with a `#`

# Connect to "evil.corp" directly
direct domain evil.corp

# Proxy "google.com" and "*.google.com"
proxy domain-suffix google.com

# Block IP 1.2.3.4
block ip 1.2.3.4

# Block all China IPs
block country cn

# Hijack all Japan IPs' UDP 53 port to 8.8.8.8
hijack country jp udp/53 8.8.8.8

# Hijack all LAN connections to loopback
hijack cidr 192.168.1.1/24 127.0.0.1

# Proxy all HTTP connections
proxy all tcp/80

# Proxy all HTTPS connections
proxy all tcp/443

# Block everything else
block all
```
