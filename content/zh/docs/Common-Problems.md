---
title: "常见问题"
weight: 98
---

### [FATA] Out of retries, exiting...

当「自动重试」启用时， 到达最大重试次数的情况下会显示这条日志并退出。

因此这条日志并不能指示出了什么问题， 你应该查阅这条日志之前的日志，
那才是真正的错误原因。


### libhysteria exits too fast (exit code: 1)

在 SagerNet 中， 当 Hysteria 启动失败时就会在屏幕上显示这个提示。

这个提示并不能指示出了什么问题。
你应该先点击 SagerNet 的左上角， 然后在弹出的抽屉中选中 「Logs」 选项，
检查最下方以 `I/libhysteria` 开头的日志， 那才是真正导致错误的原因。


### [ERRO] [error:timeout: no recent network activity]

这一项错误日志表示 Hysteria 客户端无法与服务端进行 QUIC 握手，
这可能是以下原因导致的：

+ Hysteria 客户端配置文件中的 `obfs` 或者 `protocol` 选项与服务端不匹配。
+ Hysteria 客户端配置文件中的 `server`
  选项填写了错误的服务器地址与端口，
  在使用域名的情况下也可能是该域名遭到了 DNS 劫持。
+ Hysteria 客户端与服务端之间的 UDP 通信遭到了防火墙的阻断。
+ Hysteria 服务端进程未启动或者异常退出。

<details>
<summary>
注意能使用 curl 正常请求服务器域名并不能证明能够正常进行 UDP 通信，
可以按照这里的方法进行排查。
</summary>

1. 停止服务器上的 Hysteria 服务端。
2. 在客户端和服务端安装 socat。
3. 在服务端一侧执行命令 `socat - UDP6-LISTEN:36712,reuseaddr,fork`，
   你需要把命令中的端口号换成你为 Hysteria 服务端设置的端口号。
4. 在客户端一侧执行命令 `socat - UDP:example.com:36712`，
   你需要把命令中的服务器和端口号换成 Hysteria 客户端配置中的
   `server` 项内容。
5. 如果两边的 socat 都能正常启动， 你可以尝试在两侧输入任意内容并按下回车，
   如果你输入的内容出现在了另一侧， 则客户端和服务端的 UDP 能正确连通。
   如果做不到这一点， 说明客户端到服务端的 UDP 连接可能被阻断。
</details>


### [ERRO] [error:Application error 0x1: protocol error]

这一错误日志表示 Hysteria 的客户端使用了服务端不支持的协议，
通常是客户端版本比服务端版本新导致的。

将服务器上的 Hysteria 升级到最新版本通常可以解决此问题。


### [ERRO] [error:Application error 0x2: auth error]

这一项错误日志表示 Hysteria 客户端配置中的密码是错误的，
服务端拒绝了客户端的连接请求。

请检查客户端配置中的 `auth_str` 选项是否填写正确。

**注意：** 除非你明确知道自己在做什么，
或者你使用的机场提供的连接指南明确要求你这么做，
否则你应该在客户端配置中使用 `auth_str` 而不是 `auth`。


### [FATA] [file:./config.json] [error:illegal base64 data at input byte 8]

这一项错误日志通常表示你在 Hysteria 客户端配置文件中错用了 `auth`，
将其修改为 `auth_str` 通常就能解决问题。

如果你使用的机场明确要求你使用 `auth`， 请检查你填写的 `auth` 选项是否正确。


### [FATA] [file:./config.json] [error:invalid speed]

这一项错误日志表示 Hysteria 客户端配置文件中缺少 `up_mbps` 和 `down_mbps`，
在客户端配置文件中， 这两个选项是必须的。


### [FATA] [file:./config.json] [error:json: cannot unmarshal object into Go value of type []uint8] Failed to parse client configuration

这个错误通常是在启动客户端时指定了服务端的配置文件导致的。

请检查配置文件是否正确。

如果你打算启动服务端， 请确认服务端启动命令中没有漏掉 `server` 参数。


### [ERRO] [error:CRYPTO_ERROR (0x12a): x509: certificate signed by unknown authority]

这一项错误日志表示 Hysteria 客户端无法验证服务端提供的证书。

如果你使用的是自签名证书， 请在 Hysteria 客户端配置文件中配置 `ca` 选项，
或者启用 `insecure`。

另外， 如果服务端配置的 `cert` 的证书链不完整， 客户端连接时也会产生相同的错误。
如果你使用其它 ACME 工具为 Hysteria 提供证书， 请确保使用了正确的证书文件
（正确的 .crt 或者 .pem 文件应该包含多个以
`-----BEGIN CERTIFICATE-----` 开始的证书块）。


### [ERRO] [error:CRYPTO_ERROR (0x178): tls: no application protocol]

这一项错误日志通常表示 Hysteria 客户端配置文件中的 `alpn` 选项与服务端不匹配。


### [ERRO] [error:CRYPTO_ERROR (0x12a): x509: certificate is valid for A, not B]

这一项错误日志表示在 Hysteria 客户端配置文件中
`server` 选项的域名与服务端提供的证书不匹配。

如果你希望在 `server` 选项中指定 IP 地址（例如用于对抗域名被 DNS 劫持的情况），
就需要在 `server_name` 选项中指定正确的域名。
端口对证书验证是没有影响的。

另外， 配置了错误的 `server_name` 选项也会导致这个问题。


### [FATA] [error:accept tcp [::]:1081: accept4: too many open files] Client shutdown

如果你在使用 REDIRECT、 TPROXY 或者 tun 模式。
这一项错误日志通常表明你的 iptables 规则或者路由表的配置有问题，
从 hysteria 发出的请求又被重新送回给了 hysteria 的 inbounds，
并引发了无限循环。

这个问题的解决方案取决于具体的 iptables 或者路由表配置。
通常来说， 在使用 iptables 或者路由表进行分流时，
应当尽可能绕过 hysteria 服务端和客户端自身的 IP 地址。


