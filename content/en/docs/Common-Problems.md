---
title: "Common Problems"
weight: 99
---

### [FATA] Out of retries, exiting...

When retry is enabled, Hysteria will display this log and exit once the maximum number of retries has been reached.

This is not an indication of what went wrong. You should check the logs before this one, which should be the real cause of the error.


### libhysteria exits too fast (exit code: 1)

When Hysteria fails to start, SagerNet shows this prompt on the screen.

This is not an indication of what went wrong.
You should click on the menu button (top left corner), click on "Logs" in the drawer and check the logs at the bottom starting with `I/libhysteria`.


### [ERRO] [error:timeout: no recent network activity]

The client is unable to establish a connection with the server. This might be caused by any of the following:

+ The `obfs` or `protocol` options do not match the server's config.
+ Incorrect `server` address or port.
+ There's some kind of firewall blocking the UDP connection between the client and the server.
+ The server process is not running, or is not listening on the correct address and port.

<details>
<summary>
Note: Being able to reach your server using curl does not prove that UDP traffic works as well.
You can use the following steps to verify that UDP works
</summary>

1. Stop the Hysteria server process.
2. Install socat on both the client and server.
3. Run `socat - UDP6-LISTEN:36712,reuseaddr,fork` on the server. Replace `36712` with the port you want to use.
4. Run `socat - UDP:example.com:36712` on the client. Replace `example.com:36712` with the server's address and port.
5. If socat starts successfully on both sides, try typing some random text on either side and press enter. If you see the text you typed on the other side, UDP is working.

</details>


### [ERRO] [error:Application error 0x1: protocol error]

This usually means that the protocol version is not compatible with the server you're trying to connect to.

Make sure both the client and the server are using the same version of Hysteria.


### [ERRO] [error:Application error 0x2: auth error]

This means that the password in the client config is incorrect, so the server rejects the request. Please check that the `auth_str` option is filled correctly.

You should use `auth_str` instead of `auth` in client config unless you know exactly what you're doing, or your proxy provider's guidelines explicitly require you to.


### [FATA] [file:./config.json] [error:illegal base64 data at input byte 8]

This means that you have misused `auth` in the client config.
Changing it to `auth_str` usually solves the problem.


### [FATA] [file:./config.json] [error:invalid speed]

This means that `up_mbps` and `down_mbps` are missing from the client config.

These two options are required for the client to work properly.


### [FATA] [file:./config.json] [error:json: cannot unmarshal object into Go value of type []uint8] Failed to parse client configuration

This is usually caused by accidentally using a server's config when starting the client. Please check if the config file is correct.

If you want to start the server, make sure you are not missing the `server` subcommand.


### [ERRO] [error:CRYPTO_ERROR (0x12a): x509: certificate signed by unknown authority]

This means that the client cannot verify the certificate provided by the server.

If you are using a self-signed certificate, use the `ca` option or enable `insecure`.

You can also get this error if the certificate chain of the `cert` configured on the server is not complete. If you use other ACME tools to provide the certificates for Hysteria, make sure to use the correct files.

A correct `.crt` or `.pem` file should contain multiple `-----BEGIN CERTIFICATE-----` blocks.


### [ERRO] [error:CRYPTO_ERROR (0x178): tls: no application protocol]

This means that the `alpn` option in the client config does not match the one set on the server.


### [ERRO] [error:CRYPTO_ERROR (0x12a): x509: certificate is valid for A, not B]

This means that the domain name of the `server` option in the client config does not match the certificate provided by the server.

If you use an IP address in the `server` option (e.g. to bypass DNS poisoning), you need to specify the correct domain name in the `server_name` option. The port you are using does not matter. A misconfigured `server_name` option will also cause this error.
