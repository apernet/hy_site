---
title: "优化"
weight: 7
---

### 针对超高传速度进行优化

如果要用 Hysteria 进行极高速度的传输 (如内网超过 10G 或高延迟跨国超过 1G)，请增加系统的 UDP receive buffer 大小。

```shell
sysctl -w net.core.rmem_max=4000000
```

这个命令会在 Linux 下将 buffer 大小提升到 4 MB 左右。

你可能还需要提高 `recv_window_conn` 和 `recv_window` (服务器端是 `recv_window_client`) 以确保它们至少不低于带宽-延迟的乘积。
比如如果想在一条 RTT 200ms 的线路上达到 500 MB/s 的速度，receive window 至少需要 100 MB (500*0.2)

### 路由器与其他嵌入式设备

对于运算性能和内存十分有限的嵌入式设备，如果不是必须的话建议关闭混淆，可以带来少许性能提升。

Hysteria 服务端与客户端默认的 receive window 大小是 64 MB。如果设备内存不够，请考虑通过配置降低。建议保持 stream receive window
和 connection receive window 之间 1:4 的比例关系。
