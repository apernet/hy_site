---
title: "快速入门"
weight: 2
---

本节提供的配置只是为了快速上手，可能无法满足你的需求。请到 [高级用法]({{< ref "Advanced-Usage.md" >}}) 了解更多。

### 服务器

在目录下建立一个 `config.json`。

Hysteria 服务端 **必须要** 一个 TLS 证书。有两种方式：

1. 使用内置的 ACME 客户端从 Let's Encrypt 自动获取一个证书。这是最简单的方式，前提是你有解析到服务器的域名和一个有效的邮箱地址。

```json
{
  "listen": ":36712",
  "acme": {
    "domains": [
      "your.domain.com"
    ],
    "email": "your@email.com"
  },
  "obfs": "8ZuA2Zpqhuk8yakXvMjDqEXBwY"
}
```

2. 提供自己的证书文件。

```json
{
  "listen": ":36712",
  "cert": "/home/ubuntu/my.crt",
  "key": "/home/ubuntu/my.key",
  "obfs": "8ZuA2Zpqhuk8yakXvMjDqEXBwY"
}
```

可选的 `obfs` 选项使用提供的密码对协议进行混淆，这样协议会被识别为未知 UDP 流量而不是 Hysteria/QUIC，可以用来绕过针对性的 DPI 屏蔽或者 QoS。
如果服务端和客户端的密码不匹配就不能建立连接，因此这也可以作为一个简单的密码验证。对于更高级的验证方案请见 [高级用法]({{< ref "Advanced-Usage.md" >}}) 中的 `auth`。

> **⚠ 警告 ⚠**
> 对于用 Hysteria 绕过屏蔽的中国、伊朗等国家的用户 - 强烈不推荐关闭 obfs！并且记得更换一个自己的强密码。

要启动服务端，只需运行

```bash
./hysteria-linux-amd64 server
```

如果你的配置文件没有命名为 `config.json` 或在别的路径，请用 `-c` 指定路径：

```bash
./hysteria-linux-amd64 -c blah.json server
```

### 客户端

和服务器端一样，在程序根目录下建立一个`config.json`。

```json
{
  "server": "example.com:36712",
  "obfs": "8ZuA2Zpqhuk8yakXvMjDqEXBwY",
  "up_mbps": 10,
  "down_mbps": 50,
  "socks5": {
    "listen": "127.0.0.1:1080"
  },
  "http": {
    "listen": "127.0.0.1:8080"
  }
}
```

这个配置同时开了 SOCK5 (支持 TCP & UDP) 代理和 HTTP 代理。Hysteria 还有很多其他模式，请务必前往 [高级用法]({{< ref "Advanced-Usage.md" >}}) 了解！
要启用/禁用一个模式，在配置文件中添加/移除对应条目即可。

如果你的服务端证书不是由受信任的 CA 签发的，需要用 `"ca": "/path/to/file.ca"` 指定使用的 CA 或者用 `"insecure": true` 忽略所有
证书错误（不推荐）。

`up_mbps` 和 `down_mbps` 在客户端是 **必填** 选项， **请根据实际网络情况尽量准确地填写。** 如果你不知道你的网络当前带宽是多少，建议先用测速网站进行测量。不准确的数值会严重影响 Hysteria 的性能（过高、过低都是）。

与服务端不同， 要启动客户端， 命令行里不应该有 `server` 参数， 只需运行

```bash
./hysteria-linux-amd64
```

如果你的配置文件没有命名为 `config.json` 或在别的路径，请用 `-c` 指定路径：

```bash
./hysteria-linux-amd64 -c blah.json
```
