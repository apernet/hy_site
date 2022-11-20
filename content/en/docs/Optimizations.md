---
title: "Optimizations"
weight: 7
---

### Optimizing for "long fat pipes"

Generally, these are the common bottlenecks that could limit your transfer speed (apart from the network itself):

- Processing power of your CPU, NIC, etc. (not much to do other than upgrading your hardware)
- System UDP buffer sizes
- Hysteria's flow control receive window size

If you want to use Hysteria for high speed transfers, you should increase your system's UDP receive & send buffer sizes.

#### Linux

```bash
# Set both buffers to 16 MB
sysctl -w net.core.rmem_max=16777216
sysctl -w net.core.wmem_max=16777216
```

#### BSD/macOS

```bash
sysctl -w kern.ipc.maxsockbuf=20971520
sysctl -w net.inet.udp.recvspace=16777216
# UDP is not buffered in the kernel on BSD, so there's no "sendspace" to set
```

You may also need to increase `recv_window_conn` and `recv_window` (`recv_window_client` on server side) to make sure
they are at least no less than the bandwidth-delay product. For example, if you want to achieve a transfer speed of 500 MB/s over a connection with an average RTT of 200 ms, you need a minimum receive window size of 100 MB (500*0.2).

### Routers and other embedded devices

For devices with very limited computing power, turning off obfuscation can bring a slight (~10%) CPU performance boost.

The default receive window size for both Hysteria server and client is 64 MB. If this is too large for your device, you can turn them down, but bear in mind that this could negatively affect your transfer speed. We recommend keeping the ratio of stream receive window and connection receive window at 2:5.