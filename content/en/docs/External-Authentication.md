---
title: "External Authentication"
weight: 6
---

If you are a commercial proxy provider, you may want to connect Hysteria to your own authentication backend.

### HTTP

```json
{
  // ...
  "auth": {
    "mode": "external",
    "config": {
      "http": "https://api.example.com/auth" // Both HTTP and HTTPS are supported
    }
  }
}
```

For the above config, Hysteria sends a POST request to `https://api.example.com/auth` upon each client's connection:

```json
{
  "addr": "111.222.111.222:52731",
  "payload": "[BASE64]", // auth or auth_str of the client
  "send": 12500000, // Negotiated server send speed for this client (Bps)
  "recv": 12500000 // Negotiated server recv speed for this client (Bps)
}
```

The endpoint must return results with HTTP status code 200 (even if the authentication failed):

```json
{
  "ok": false,
  "msg": "No idea who you are"
}
```

`ok` indicates whether the authentication passed. `msg` is a success/failure message.

### Command

```json
{
  // ...
  "auth": {
    "mode": "external",
    "config": {
      "cmd": "./auth.sh" // Can be a program or a script
    }
  }
}
```

`addr`, `payload`, `send` and `recv` are passed to the command as arguments. 

If the return value of the command is 0, the authentication is considered successful. Otherwise it's considered failed. The stdout (but not stderr) of the command will be used as the success/failure message.

Shell script example:

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

For the above code, any string containing `LilNas` (e.g. `FuckLilNasX`, `LoveLilNasY`) will pass the authentication.
