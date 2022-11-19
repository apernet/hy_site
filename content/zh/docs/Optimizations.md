---
title: "优化"
weight: 7
---

### 针对超高传速度进行优化

通常，除了网络本身以外，这些因素可能限制你的传输速度：

- CPU、网卡等硬件的处理能力（除了升级硬件没有什么好办法）
- 系统 UDP buffer 大小
- Hysteria 的接收窗口大小

如果要用 Hysteria 进行高速传输 ，请增加系统 UDP receive buffer 大小。

```shell
sysctl -w net.core.rmem_max=4194304
```

这个命令会在 Linux 下将 buffer 大小提升到 4 MB 左右。

你可能还需要提高 `recv_window_conn` 和 `recv_window` (服务器端是 `recv_window_client`) 以确保它们至少不低于带宽-延迟的乘积。比如如果想在一条 RTT 200ms 的线路上达到 500 MB/s 的速度，receive window 至少需要 100 MB (500*0.2)

### 路由器与其他嵌入式设备

对于运算性能十分有限的嵌入式设备，关闭混淆 (obfs) 可以带来约 10% 的 CPU 性能提升。

Hysteria 服务端与客户端默认的 receive window 大小是 64 MB。如果设备内存不够，可以通过配置降低它们的大小。但注意这可能会影响传输速度。建议保持 stream receive window 与 connection receive window 的比例为 2:5。
