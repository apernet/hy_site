---
title: "Installation"
weight: 1
---

### Linux server deployment

- Download executables from [GitHub Releases](https://github.com/apernet/hysteria/releases) ([Which build should I choose?](#which-build-should-i-choose))
- [Docker / Docker Compose]({{< ref "Docker.md" >}})
- [Arch Linux AUR](https://aur.archlinux.org/packages/hysteria/)
- [Linux server deployment script](https://raw.githubusercontent.com/apernet/hysteria/master/install_server.sh)
- Clone and build from source with `build.sh` (Linux) / `build.ps1` (Windows)

### Windows, Linux, macOS clients

- Same as above
- [v2rayN](https://github.com/2dust/v2rayN) (Windows GUI)
- [NekoRay](https://github.com/MatsuriDayo/nekoray) (Windows/Linux/macOS GUI)
- [Clash.Meta](https://github.com/MetaCubeX/Clash.Meta) (Clash kernel fork)

### OpenWrt LuCI app

- [openwrt-passwall](https://github.com/xiaorouji/openwrt-passwall)

### Android

- [SagerNet](https://github.com/SagerNet/SagerNet) with [hysteria-plugin](https://github.com/SagerNet/SagerNet/releases?q=Hysteria)
- [Matsuri](https://github.com/MatsuriDayo/Matsuri) with [hysteria-plugin](https://github.com/MatsuriDayo/plugins/releases?q=Hysteria)
- [Clash Meta for Android](https://github.com/MetaCubeX/ClashMetaForAndroid)

### iOS

- [Stash](https://apps.apple.com/app/stash/id1596063349)
- [Shadowrocket](https://apps.apple.com/us/app/shadowrocket/id932747118)
- [Singbox for iOS](https://testflight.apple.com/join/c6ylui2j) with [Singbox config](https://sing-box.sagernet.org/configuration/inbound/hysteria/)
- [Pharos Pro](https://apps.apple.com/app/pharos-pro/id1456610173) with [Clash.Meta config](https://docs.metacubex.one/function/proxy/hysteria)

### Which build should I choose?

#### TL;DR for most users

- Windows (64-bit): `hysteria-windows-amd64-avx.exe`. If that doesn't work, try `hysteria-windows-amd64.exe`.
- Linux (64-bit): `hysteria-linux-amd64-avx`. If that doesn't work, try `hysteria-linux-amd64`.
- Linux (32-bit): `hysteria-linux-386`.
- macOS (M1 or newer): `hysteria-darwin-arm64`.
- macOS (Intel): `hysteria-darwin-amd64-avx`. If that doesn't work, try `hysteria-darwin-amd64`.

#### Details

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

If you are a developer of a project that supports Hysteria, or you have found a project that supports Hysteria as a user, contact us to have it listed here.