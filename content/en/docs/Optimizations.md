---
title: "Optimizations"
weight: 7
---

### Optimizing for extreme transfer speeds

If you want to use Hysteria for very high speed transfers (e.g. 10GE, 1G+ over inter-country long fat pipes), consider
increasing your system's UDP receive buffer size.

```bash
sysctl -w net.core.rmem_max=4000000
```

This would increase the buffer size to roughly 4 MB on Linux.

You may also need to increase `recv_window_conn` and `recv_window` (`recv_window_client` on server side) to make sure
they are at least no less than the bandwidth-delay product. For example, if you want to achieve a transfer speed of 500
MB/s on a line with an RTT of 200 ms, you need a minimum receive window size of 100 MB (500*0.2).

### Routers and other embedded devices

For devices with very limited computing power and RAM, turning off obfuscation can bring a slight performance boost.

The default receive window size for both Hysteria server and client is 64 MB. Consider lowering them if it's too large
for your device. Keeping a ratio of one to four between stream receive window and connection receive window is
recommended.
