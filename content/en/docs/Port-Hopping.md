---
title: "Port Hopping"
weight: 5
---

Starting with 1.3.0, Hysteria has support for multi-port addresses on the client side. Format:

```
server:port1,port2-port3,...
```

Examples:

`example.com:1145,5144` means that the server is available on ports 1145 and 5144 (2 ports in total)

`example.com:20000-50000` means that the server is available on ports 20000-50000 (30001 ports in total)

`example.com:1145,5144-10240` means the server is available on ports 1145 and 5144-10240 (5098 ports in total)

There is no limit to the number of ports you can specify.

The client will randomly select a port to connect to, and randomly hop to a new port every once in a while (**default to 10 seconds**, controlled by `hop_interval` in the client config). The hopping process is transparent to the upper layers and should not cause any data loss/connection interruption under normal circumstances.

> **⚠ Be aware ⚠**
>
> This feature currently only supports UDP protocol.
> 
> Port hopping relies on QUIC connection migration added in 1.3.0. Both the server and the client must be running 1.3.0 or higher.

## Server configuration

**Hysteria server does not have built-in support for multi-port addresses (you cannot use the above format as a listening address).** It must be used with an external port forwarding mechanism.

Forwarding UDP ports 20000-50000 on eth0 to port 5666 on Linux:

First check the network interface controllers used by the server

```shell
ifconfig
```

After confirming that the network interface controller is `eth0`，set port forwarding:

```bash
# IPv4
iptables -t nat -A PREROUTING -i eth0 -p udp --dport 20000:50000 -j DNAT --to-destination :5666
# IPv6
ip6tables -t nat -A PREROUTING -i eth0 -p udp --dport 20000:50000 -j DNAT --to-destination :5666
```

The server can then listen on port 5666, while the client connects with `example.com:20000-50000`.

Naturally, just because the server is available on multiple ports does not mean that clients must use them all. If a client does not want to enable port hopping, it can still just pick a single port to connect to.

## Why is this useful?

Users in China often report that their ISPs block/restrict persistent UDP connections to a single port. Port hopping should invalidate this kind of mechanism.
