---
title: "日志"
weight: 10
---

程序默认在 stdout 输出 DEBUG 级别，文字格式的日志。

如果需要修改日志级别可以使用 `LOGGING_LEVEL` 环境变量，支持 `panic`, `fatal`, `error`, `warn`, `info`, `debug`, `trace`

如果需要输出 JSON 可以把 `LOGGING_FORMATTER` 设置为 `json`

如果需要修改日志时间戳格式可以使用 `LOGGING_TIMESTAMP_FORMAT`

### IP 匿名化

使用 `LOGGING_IPV4_MASK` 和 `LOGGING_IPV6_MASK` 环境变量可以指定用来匿名化 IP 地址的 CIDR 掩码。

例如当 `LOGGING_IPV4_MASK` 为 `24` 时，日志里出现的所有 IPv4 地址的最后一个八位组 (Octet) 会被替换为 0。(`1.2.3.4` -> `1.2.3.0`)