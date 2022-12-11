---
title: "下载安装"
weight: 1
---

### Linux 服务端部署

- 从 [GitHub Releases](https://github.com/apernet/hysteria/releases) 下载编译好的版本 ([我该下载哪个文件？](#我该下载哪个文件))
- [Docker / Docker Compose]({{< ref "Docker.md" >}})
- [Arch Linux AUR](https://aur.archlinux.org/packages/hysteria/)
- [Linux 服务端部署脚本](https://raw.githubusercontent.com/apernet/hysteria/master/install_server.sh)
- 自己用 `build.sh` (Linux) / `build.ps1` (Windows) 从源码编译

### Windows, macOS, Linux 客户端

- 同上
- [v2rayN](https://github.com/2dust/v2rayN) (Windows GUI)
- [NekoRay](https://github.com/MatsuriDayo/nekoray) (Windows/Linux/macOS GUI)
- [Clash.Meta](https://github.com/MetaCubeX/Clash.Meta) (Clash kernel fork)

### OpenWrt LuCI app

- [openwrt-passwall](https://github.com/xiaorouji/openwrt-passwall)

### Android

- [SagerNet](https://github.com/SagerNet/SagerNet) 配合 [hysteria-plugin](https://github.com/SagerNet/SagerNet/releases?q=Hysteria)
- [Matsuri](https://github.com/MatsuriDayo/Matsuri) 配合 [hysteria-plugin](https://github.com/MatsuriDayo/plugins/releases?q=Hysteria)
- [Clash Meta for Android](https://github.com/MetaCubeX/ClashMetaForAndroid)

### iOS

- [Stash](https://apps.apple.com/app/stash/id1596063349)
- [Shadowrocket](https://apps.apple.com/us/app/shadowrocket/id932747118)

### 我该下载哪个文件？

#### 适用于大多数人的太长不看版

- Windows (64-bit): `hysteria-windows-amd64-avx.exe` 如果不能用，尝试 `hysteria-windows-amd64.exe`
- Linux (64-bit): `hysteria-linux-amd64-avx` 如果不能用，尝试 `hysteria-linux-amd64`
- Linux (32-bit): `hysteria-linux-386`
- macOS (M1 或更新): `hysteria-darwin-arm64`
- macOS (Intel): `hysteria-darwin-amd64-avx` 如果不能用，尝试 `hysteria-darwin-amd64`

#### 列表

| File | OS | Arch | Spec |
| --- | --- | --- | --- |
| hysteria-darwin-amd64 | macOS | x86_64 | |
| hysteria-darwin-amd64-avx | macOS | x86_64 | AVX |
| hysteria-darwin-arm64 | macOS | ARM64 | M1 or newer |
| hysteria-freebsd-386 | FreeBSD | x86 | |
| hysteria-freebsd-amd64 | FreeBSD | x86_64 | |
| hysteria-freebsd-amd64-avx | FreeBSD | x86_64 | AVX |
| hysteria-freebsd-arm | FreeBSD | ARMv7 | |
| hysteria-freebsd-arm64 | FreeBSD | ARM64 | |
| hysteria-linux-386 | Linux | x86 | |
| hysteria-linux-amd64 | Linux | x86_64 | |
| hysteria-linux-amd64-avx | Linux | x86_64 | AVX |
| hysteria-linux-arm | Linux | ARMv7 | |
| hysteria-linux-armv5 | Linux | ARMv5 | |
| hysteria-linux-arm64 | Linux | ARM64 | |
| hysteria-linux-mipsle | Linux | MIPS | Little Endian |
| hysteria-linux-mipsle-sf | Linux | MIPS | Little Endian, Soft Float |
| hysteria-linux-s390x | Linux | s390x | |
| hysteria-windows-386.exe | Windows | x86 | |
| hysteria-windows-amd64.exe | Windows | x86_64 | |
| hysteria-windows-amd64-avx.exe | Windows | x86_64 | AVX |
| hysteria-windows-arm64.exe | Windows | ARM64 | |

----------

如果你是某个支持 Hysteria 的项目的开发者，或者你作为用户发现了其他支持 Hysteria 的项目，请联系我们将其列在这里。