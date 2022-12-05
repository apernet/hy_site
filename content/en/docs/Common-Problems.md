---
title: "Common Problems"
weight: 98
---

### [FATA] Out of retries, exiting...

When retry is enabled, Hysteria will show this log and automatically terminate once the maximum number of retries is reached.

This log does not provide any information about the cause of the error. To identify the root of the problem, you should review the logs before this one.


### libhysteria exits too fast (exit code: 1)

When Hysteria fails to launch, SagerNet will display this prompt on the screen.

This prompt does not provide any information about the cause of the failure.
To troubleshoot the problem, click on the menu button in the top left corner of the screen, select "Logs" in the drawer, and review the logs at the bottom starting with `I/libhysteria`.


### [ERRO] [error:timeout: no recent network activity]

The client is unable to establish a connection with the server. This might be caused by any of the following:

+ The `obfs` or `protocol` settings do not match the server's configuration.
+ The `server` address or port is incorrect.
+ A firewall is blocking the UDP connection between the client and the server.
+ The server process is either not running or is not listening on the correct address and port.

<details>
<summary>
Note: Just because you can reach your server using curl does not guarantee that UDP traffic is functioning properly. To verify that UDP is working, you can follow these steps:
</summary>

1. Stop the Hysteria server process by using the `kill` or `pkill` command.
2. Install the `socat` utility on both the client and server by running the appropriate package manager command, such as `apt-get install socat` or `yum install socat`.
3. On the server, run the following command to start a UDP listener on the desired port: `socat - UDP6-LISTEN:36712,reuseaddr,fork`
4. On the client, run the following command to connect to the server's UDP listener: `socat - UDP:example.com:36712`
5. If `socat` starts successfully on both sides, try typing some random text on either side and press enter. If the text appears on the other side, it indicates that UDP communication is working properly.

</details>


### [ERRO] [error:Application error 0x1: protocol error]

This error typically indicates that the protocol version used by the client is not compatible with the server you are trying to connect to.

To resolve this issue, ensure that both the client and the server are using the same version of Hysteria.


### [ERRO] [error:Application error 0x2: auth error]

This error occurs when the password specified in the client config is incorrect, causing the server to reject the connection request.

To fix this issue, verify that the `auth_str` option in the client config is filled in correctly.

It is recommended to use `auth_str` instead of `auth` in the client config unless you are sure of what you are doing, or if your proxy provider's guidelines explicitly require you to use `auth`.


### [FATA] [file:./config.json] [error:illegal base64 data at input byte 8]

This means that you have misused `auth` in the client config.

Changing it to `auth_str` usually solves the problem.


### [FATA] [file:./config.json] [error:invalid speed]

This means that `up_mbps` and `down_mbps` are missing from the client config.

These two options are required for the client to work properly.


### [FATA] [file:./config.json] [error:json: cannot unmarshal object into Go value of type []uint8] Failed to parse client configuration

This error typically occurs when the client config file is incorrect, often because a server's config file was accidentally used when starting the client.

To fix this issue, verify that the config file used by the client is correct.

If you want to start the server, ensure that the `server` subcommand is included in the command.


### [ERRO] [error:CRYPTO_ERROR (0x12a): x509: certificate signed by unknown authority]

This error occurs when the client is unable to verify the certificate provided by the server.

If you are using a self-signed certificate, use the `ca` option or enable `insecure` in the client config to bypass certificate verification.

You may also encounter this error if the certificate chain of the `cert` configured on the server is incomplete. If you use other ACME tools to provide the certificates for Hysteria, make sure to use the correct files.

A correct `.crt` or `.pem` file should contain multiple `-----BEGIN CERTIFICATE-----` blocks. Check the file to ensure it is complete.


### [ERRO] [error:CRYPTO_ERROR (0x178): tls: no application protocol]

This error indicates that the alpn option in the client config does not match the alpn setting on the server.

To fix this issue, ensure that the alpn option in the client config is set to the same value as on the server.


### [ERRO] [error:CRYPTO_ERROR (0x12a): x509: certificate is valid for A, not B]

This error indicates that the domain name of the `server` option in the client config does not match the certificate provided by the server.

If you use an IP address in the `server` option (e.g. to bypass DNS poisoning), you need to specify the correct domain name in the `server_name` option. The port you are using does not affect this error. A misconfigured `server_name` option will also cause this error.

To fix this issue, verify that the `server_name` option in the client config is set to the correct domain name and that it matches the certificate provided by the server. This should resolve the error.
