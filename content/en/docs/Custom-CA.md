---
title: "Custom CA"
weight: 11
---

Assuming the server address is `123.123.123.123`, UDP port `5678` is open, openssl is installed, and hysteria is in `/root/hysteria/`.

Run the script below under `/root/hysteria/`:

``` bash
#!/usr/bin/env bash

domain=$(openssl rand -hex 8)
password=$(openssl rand -hex 16)
obfs=$(openssl rand -hex 6)
path="/root/hysteria"

openssl genrsa -out hysteria.ca.key 2048

openssl req -new -x509 -days 3650 -key hysteria.ca.key -subj "/C=CN/ST=GD/L=SZ/O=Hysteria, Inc./CN=Hysteria Root CA" -out hysteria.ca.crt

openssl req -newkey rsa:2048 -nodes -keyout hysteria.server.key -subj "/C=CN/ST=GD/L=SZ/O=Hysteria, Inc./CN=*.${domain}.com" -out hysteria.server.csr

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

On the server side, copy `server.json`, `hysteria.server.crt`, `hysteria.server.key` to `/root/hysteria/`. Run `/root/hysteria/hysteria -c /root/hysteria/server.json server` to start the server.

On the client side, assuming that the client is also in `/root/hysteria`, copy `client.json`, `hysteria.ca.crt`
   to `/root/hysteria/`. Run `/root/hysteria/hysteria -c /root/hysteria/client.json` to start the client.

Make sure to change the server address, port, certificate path, etc. according to your needs.

If you are using a 3rd party client (e.g. Shadowrocket) on iOS, you can airdrop the file `hysteria.ca.crt` to your device and install it.