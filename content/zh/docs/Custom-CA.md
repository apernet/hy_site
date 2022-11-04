---
title: "自定义 CA"
weight: 11
---

假设服务器地址是 `123.123.123.123`, 端口 `5678` UDP 未被防火墙拦截，已经安装了 openssl，并且 hysteria 在 `/root/hysteria/` 目录下。

在 `/root/hysteria/` 目录下，将以下脚本保存为 `generate.sh` 赋予执行权限: `chmod +x ./generate.sh` 后，运行 `./generate.sh` 命令生成自定义 CA 证书

``` shell
#!/usr/bin/env bash

domain=$(openssl rand -hex 8)
password=$(openssl rand -hex 16)
obfs=$(openssl rand -hex 6)
path="/root/hysteria"
# 生成CAkey
openssl genrsa -out hysteria.ca.key 2048
# 生成CA证书
openssl req -new -x509 -days 3650 -key hysteria.ca.key -subj "/C=CN/ST=GD/L=SZ/O=Hysteria, Inc./CN=Hysteria Root CA" -out hysteria.ca.crt

openssl req -newkey rsa:2048 -nodes -keyout hysteria.server.key -subj "/C=CN/ST=GD/L=SZ/O=Hysteria, Inc./CN=*.${domain}.com" -out hysteria.server.csr
# 签发服务端用的证书
openssl x509 -req -extfile <(printf "subjectAltName=DNS:${domain}.com,DNS:www.${domain}.com") -days 3650 -in hysteria.server.csr -CA hysteria.ca.crt -CAkey hysteria.ca.key -CAcreateserial -out hysteria.server.crt

cat > ./client.json <<EOF
{
    "server": "123.123.123.123:5678",
    "alpn": "h3",
    "obfs": "${obfs}",
    "auth_str": "${password}",
    "up_mbps": 30,
    "down_mbps": 30,
    "socks5": {
        "listen": "0.0.0.0:1080"
    },
    "http": {
        "listen": "0.0.0.0:8080"
    },
    "server_name": "www.${domain}.com",
    "ca": "${path}/hysteria.ca.crt"
}
EOF


cat > ./server.json <<EOF
{
    "listen": ":5678",
    "alpn": "h3",
    "obfs": "${obfs}",
    "cert": "${path}/hysteria.server.crt",
    "key": "${path}/hysteria.server.key" ,
    "auth": {
        "mode": "password",
        "config": {
            "password": "${password}"
        }
    }
}
EOF
```
</details>

服务端：复制 `server.json`、 `hysteria.server.crt`、 `hysteria.server.key` 到 `/root/hysteria/` 目录下，运行 `/root/hysteria/hysteria -c /root/hysteria/server.json server` 命令启动服务端。

客户端：假设客户端运行目录也为 `/root/hysteria`, 复制 `client.json`、`hysteria.ca.crt` 到 `/root/hysteria/` 目录下，运行 `/root/hysteria/hysteria -c /root/hysteria/client.json` 命令启动客户端。

生成 CA 证书后，根据自身情况修改服务器地址、端口和证书文件路径，加上 `obfs` 和 `alpn` 是防止首次在某些环境下被墙，第一次在全参数情况下测试通过后，可以自身网络环境删除不必须要参数，比如 `obfs` 和 `alpn`

iOS 端如果使用的是小火箭 Shadowrocket，可以把文件 `hysteria.ca.crt` airdrop 到手机，然后在手机上安装并信任后, 就可以使用自定义 CA 证书了。
