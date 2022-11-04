---
title: "Logging"
weight: 10
---

The program outputs `DEBUG` level, text format logs via stdout by default.

To change the logging level, use `LOGGING_LEVEL` environment variable. The available levels are `panic`, `fatal`
, `error`, `warn`, `info`, ` debug`, `trace`

To print JSON instead, set `LOGGING_FORMATTER` to `json`

To change the logging timestamp format, set `LOGGING_TIMESTAMP_FORMAT`

### IP Anonymization

Use `LOGGING_IPV4_MASK` and `LOGGING_IPV6_MASK` environment variables to assign CIDR masks to IP addresses in logs. For example, when `LOGGING_IPV4_MASK` is set to `24`, the last octet of any IPv4 address appearing in logs will be replaced with `0`. (`1.2.3.4` -> `1.2.3.0`)