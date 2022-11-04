---
title: "外部验证接入"
weight: 6
---


如果你是商业代理服务提供商，可以这样把 Hysteria 接入到自己的验证后端：

### HTTP

```json
{
  // ...
  "auth": {
    "mode": "external",
    "config": {
      "http": "https://api.example.com/auth" // 支持 HTTP 和 HTTPS
    }
  }
}
```

对于上述配置，Hysteria 会把验证请求通过 HTTP POST 发送到 `https://api.example.com/auth`

```json
{
  "addr": "111.222.111.222:52731",
  "payload": "[BASE64]", // 对应客户端配置的 auth 或 auth_str 字段
  "send": 12500000, // 协商后的服务端最大发送速率 (Bps)
  "recv": 12500000 // 协商后的服务端最大接收速率 (Bps)
}
```

后端必须用 HTTP 200 状态码返回验证结果（即使验证不通过）：

```json
{
  "ok": false,
  "msg": "No idea who you are"
}
```

`ok` 表示验证是否通过，`msg` 是成功/失败消息。

### 命令

```json
{
  // ...
  "auth": {
    "mode": "external",
    "config": {
      "cmd": "./auth.sh" // 可以是个程序或者脚本
    }
  }
}
```

`addr`, `payload`, `send`, `recv` 会作为参数传给命令。

如果返回值为 0，验证通过。否则验证失败。命令的 stdout (不包括 stderr) 会作为成功/失败消息。

示例：

```bash
#!/bin/bash

if [ $# -ne 4 ]; then
    echo "invalid number of arguments"
    exit 1
fi

ADDR=$1
AUTH=$2
SEND=$3
RECV=$4

if [[ $AUTH != *"LilNas"* ]]; then
    echo "no bitch"
    exit 1
fi

echo "ok idiot"
exit 0
```

对于上述代码，任何包含 `LilNas` (如 `FuckLilNasX`, `LoveLilNasY`) 的字符串会通过验证。