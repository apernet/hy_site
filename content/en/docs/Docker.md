---
title: "Docker"
weight: 12
---

## About Dockerfile

Be aware that our official Docker image is based on **alpine**. This means that
**some glibc calls may not work if you run programs that depend on glibc**.

By default, **bash** is installed for debugging, **tzdata** is used to
provide time zone configuration, and **ca-certificates** is used to ensure the 
trust of the ssl certificate chain. It does not contain any other tools that are
not part of the standard alpine system.

The hysteria executable is at `/usr/local/bin/hysteria`. **ENTRYPOINT** is set 
to **execute the `hysteria` command** - this means that when the container starts,
the `hysteria` command will be executed by default.

## Usage

### For average Docker users

You can mount the configuration file to any location and use `-c` to specify the
location.

In the following command, we assume that the **`/root/hysteria.json`** configuration
file is mounted to **`/etc/hysteria.json`**:

⚠️ Note: **If you don't want to use the host network (`--network=host`), please make sure that
the hysteria UDP port is correctly mapped (`-p 1234:1234/udp`)**

```sh
# Please replace `/root/hysteria.json` with your actual configuration file path
docker run -dt --network=host --name hysteria \
    -v /root/hysteria.json:/etc/hysteria.json \
    tobyxdd/hysteria -c /etc/hysteria.json server
```

### For docker-compose users

Create a directory and copy [docker-compose.yaml](https://raw.githubusercontent.com/HyNetwork/hysteria/master/docker-compose.yaml) to it. Create your own configuration file and mount it to the container.

```sh
# Create dir
mkdir hysteria && cd hysteria

# Download the docker-compose example config
wget https://raw.githubusercontent.com/HyNetwork/hysteria/master/docker-compose.yaml

# Create your config
cat <<EOF > hysteria.json
{
  "listen": ":36712",
  "acme": {
    "domains": [
      "your.domain.com"
    ],
    "email": "your@email.com"
  },
  "obfs": "8ZuA2Zpqhuk8yakXvMjDqEXBwY",
  "up_mbps": 100,
  "down_mbps": 100
}
EOF

# Start
docker-compose up -d
```
