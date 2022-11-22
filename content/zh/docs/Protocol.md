---
title: "协议规范"
weight: 99
---

The Hysteria protocol is designed to provide a fast, secure, and reliable way to bypass firewalls and censorship, with both TCP and UDP relaying capabilities.

This document describes the v3 protocol used since Hysteria version 1.0.0, which is hereafter referred to as "The Hysteria protocol".

### Requirements Language

The keywords "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119).

### Underlying Protocol

The Hysteria protocol MUST be implemented on top of the standard QUIC transport protocol [RFC 9000](https://datatracker.ietf.org/doc/html/rfc9000) with the [Unreliable Datagram Extension](https://datatracker.ietf.org/doc/draft-ietf-quic-datagram/).

All multibyte numbers are in big endian.

### Establishment of a Connection

The client MUST start by making a QUIC connection to the server, opening a "control stream" and sending a "client hello" message.

```
[1-byte Version][8-byte SendBPS][8-byte RecvBPS][2-byte AuthLen][AuthLen bytes of AuthData]
```

Version: The version of the Hysteria protocol. `0x03` in this case.

SendBPS: The maximum send bandwidth in bytes per second. The client MAY use this value to limit the rate at which it sends data to the server.

RecvBPS: The maximum receive bandwidth in bytes per second. The client MAY use this value to limit the rate at which it receives data from the server.

AuthLen: The length of the authentication data.

AuthData: The authentication data.

The server MUST respond with a "server hello" message, whether or not the client is authorized to connect.

```
[1-byte OK][8-byte SendBPS][8-byte RecvBPS][2-byte MsgLen][MsgLen bytes of Message]
```

OK: A boolean indicating whether the client is authorized to connect.

SendBPS: The negotiated send bandwidth of the server in bytes per second. It SHOULD NOT be greater than the client's RecvBPS. The server MAY use this value to limit the rate at which it sends data to the client. The client MAY use this value to limit the rate at which it receives data from the server.

RecvBPS: The negotiated receive bandwidth of the server in bytes per second. It SHOULD NOT be greater than the client's SendBPS. The server MAY use this value to limit the rate at which it receives data from the client. The client MAY use this value to limit the rate at which it sends data to the server.

MsgLen: The length of the message.

Message: The welcome or authentication error message.

The client MUST only proceed with the connection if the server responds with OK=1, and SHOULD close the connection immediately otherwise.

### Handling Relay Requests

Once the client and server have established a valid connection, the client can send relay requests to the server. For each request, the client MUST open a dedicated "data stream" and send a "request" message.

```
[1-byte Type][2-byte HostLen][HostLen bytes of Host][2-byte Port]
```

Type: The type of the request. `0x00` for TCP, `0x01` for UDP. Other values are reserved for future use.

HostLen: The length of the hostname.

Host: The hostname of the target. MUST be either an IP address or a domain name.

Port: The port of the target.

The server MUST try to establish a connection to the target, and respond with a "response" message, whether or not the connection was successful.

```
[1-byte OK][4-byte UDPSessionID][2-byte MsgLen][MsgLen bytes of Message]
```

OK: A boolean indicating whether the target is reachable.

UDPSessionID: The UDP relay session ID. See below for more information.

MsgLen: The length of the message.

Message: The response success or failure message.

The client MUST only proceed with the request if the server responds with OK=1, and SHOULD close the stream immediately otherwise.

#### TCP

After responding with a successful "response" message, the server MUST relay the communication between the client and the target in both directions over the established "data stream" & TCP connection, until either the client closes the "data stream", or the target closes the TCP connection.

#### UDP

For UDP requests, the server MUST return a UDP session ID in the "response" message above. The ID MUST NOT be a duplicate of any existing UDP session ID. For each UDP session, the server MUST maintain a dedicated UDP socket bound to a unique port.

The client MAY indicate the target's address in the "request" message above, but the server MUST NOT assume that the client will only send UDP packets to the indicated target with this session and impose any restrictions solely based on this information.

The client and server then MUST communicate over the QUIC connection's unreliable datagram channel, encapsulating the UDP packets in the following "UDP message" format:

```
[4-byte UDPSessionID][2-byte HostLen][HostLen bytes of Host][2-byte Port][2-byte MsgID][1-byte FragID][1-byte FragCount][2-byte DataLen][DataLen bytes of Data]
```

UDPSessionID: The previously obtained UDP session ID.

HostLen: The length of the hostname.

Host: The hostname of the target. MUST be either an IP address or a domain name.

Port: The port of the target.

MsgID: The message ID for fragmented packets. See below for more information.

FragID: The fragment ID for fragmented packets. See below for more information.

FragCount: The total number of fragments for fragmented packets. See below for more information.

DataLen: The length of the data.

Data: The data.

The client and server MUST hold (keep open) the "data stream" as long as the UDP session is active. Closing the "data stream" signals the termination of the UDP session.

##### Fragmented Packets

Due to the datagram size limit of QUIC's unreliable datagram channel, if a UDP packet is larger than the maximum datagram size allowed by QUIC, it MUST be either fragmented or dropped.

Every fragment of a fragmented packet MUST have the same, unique, MsgID. FragID is the fragment ID starting from 0. FragCount is the total number of fragments. The server and client MUST wait for all fragments of a fragmented packet to arrive before processing it. If one or more fragments of a fragmented packet are lost, the entire packet MUST be dropped.

For non-fragmented packets, FragCount MUST be 1. The values of MsgID and FragID do not matter.

### "xplus" Obfuscation

Hysteria protocol implementations SHOULD provide a standard traffic obfuscation mechanism called "xplus".

"xplus" encapsulates all QUIC packets in the following format:

```
[16-byte salt][obfuscated payload]
```

For each QUIC packet, the obfuscator MUST calculate the SHA-256 hash of a randomly generated 16-byte salt appended to a fixed user-provided pre-shared key. The hash is then used to obfuscate the payload using the following XOR algorithm:

```
for i = 0 to len(payload) - 1
    obfuscated[i] = payload[i] XOR hash[i % 32]
```

The deobfuscator MUST use the same algorithms to calculate the salted hash and deobfuscate the payload.

Any invalid packets MUST be silently dropped.