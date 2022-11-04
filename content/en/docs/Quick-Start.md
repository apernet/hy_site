---
title: "Quick Start"
weight: 2
---

This is only a bare-bones example to get the server and client running. Go to [Advanced Usage]({{< ref "Advanced-Usage.md" >}}) for more details.

### Server

Create a `config.json` under the root directory of the program:

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

Hysteria requires a TLS certificate. You can either get a trusted TLS certificate from Let's Encrypt automatically using
the built-in ACME integration, or provide it yourself. It does not have to be valid and trusted, but in that case the
clients need additional configuration. To use your own existing TLS certificate, refer to this config:

```json
{
  "listen": ":36712",
  "cert": "/home/ubuntu/my.crt",
  "key": "/home/ubuntu/my.key",
  "obfs": "8ZuA2Zpqhuk8yakXvMjDqEXBwY"
}
```

The (optional) `obfs` option obfuscates the protocol using the provided password, so that it is not apparent that this
is Hysteria/QUIC, which could be useful for bypassing DPI blocking or QoS. If the passwords of the server and client do
not match, no connection can be established. Therefore, this can also serve as a simple password authentication. For
more advanced authentication schemes, see `auth` in [Advanced Usage]({{< ref "Advanced-Usage.md" >}}).

> **⚠ Warning ⚠**
> For users who use Hysteria to bypass censorship in China, Iran, etc., we strongly advise you NOT to disable obfs. And remember to use a strong password of your own.

To launch the server, simply run

```bash
./hysteria-linux-amd64 server
```

If your config file is not named `config.json` or is in a different path, specify it with `-c`:

```bash
./hysteria-linux-amd64 -c blah.json server
```

### Client

Same as the server side, create a `config.json` under the root directory of the program:

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

This config enables a SOCKS5 proxy (with both TCP & UDP support), and an HTTP proxy at the same time. There are many
other modes in Hysteria, be sure to check them out in [Advanced Usage]({{< ref "Advanced-Usage.md" >}}). To enable or
disable a mode, simply add or remove its entry in the config file.

If your server certificate is not issued by a trusted CA, you need to specify the CA used
with `"ca": "/path/to/file.ca"` on the client or use `"insecure": true` to ignore all certificate errors (not
recommended).

`up_mbps` and `down_mbps` are mandatory on the client side. Try to fill in these values as accurately as possible
according to your network conditions, as they are crucial for Hysteria to work optimally.

Some users may attempt to forward other encrypted proxy protocols such as Shadowsocks with relay. While this technically
works, it's not optimal from a performance standpoint - Hysteria itself uses TLS, considering that the proxy protocol
being forwarded is also encrypted, and the fact that almost all sites are now using HTTPS, it essentially becomes triple
encryption. If you need a proxy, just use our proxy modes.

To launch the client, simply run

```bash
./hysteria-linux-amd64
```

If your config file is not named `config.json` or is in a different path, specify it with `-c`:

```bash
./hysteria-linux-amd64 -c blah.json
```