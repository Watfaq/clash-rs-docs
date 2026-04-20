# Outbound Proxies

This document covers all supported outbound proxy protocols in ClashRS, including their configuration options and examples.

## Overview

ClashRS supports multiple outbound proxy protocols for different use cases:

- **AnyTLS**: TLS-multiplexed proxy protocol
- **Shadowsocks**: Lightweight SOCKS5 proxy with encryption
- **ShadowQuic**: QUIC-based Shadowsocks with JLS obfuscation
- **VMess**: V2Ray protocol with various transport options
- **VLess**: Lightweight V2Ray protocol
- **Trojan**: TLS-based proxy protocol
- **Hysteria2**: Modern UDP-based proxy with congestion control
- **Tuic**: UDP-based proxy with QUIC transport
- **Tailscale**: Embedded Tailscale node as an outbound
- **Wireguard**: VPN protocol implementation
- **SSH**: SSH tunnel proxy
- **Tor**: Tor network proxy
- **SOCKS5**: Standard SOCKS5 proxy
- **HTTP**: Standard HTTP proxy

## Basic Configuration Structure

All outbound proxies are defined in the `proxies` section:

```yaml
proxies:
  - name: "proxy-name"
    type: protocol-type
    server: proxy-server
    port: proxy-port
    # Protocol-specific options
```

## AnyTLS

AnyTLS is a TLS-multiplexed proxy protocol that uses a shared TLS session pool for efficient connection management.

```yaml
proxies:
  - name: "anytls"
    type: anytls
    server: example.com
    port: 443
    password: your-password
    udp: true
    sni: example.com
    skip-cert-verify: false
    alpn:
      - h2
      - http/1.1
    # Optional fields (parsed for compatibility, not yet applied by runtime)
    # fingerprint: chrome
    # client-fingerprint: chrome
    # idle-session-check-interval: 30
    # idle-session-timeout: 30
    # min-idle-session: 0
```

{% hint style="info" %}
`fingerprint`, `client-fingerprint`, and idle-session fields are accepted for config compatibility but are not yet applied by the runtime.
{% endhint %}

## Shadowsocks

### Basic Configuration

```yaml
proxies:
  - name: "ss-simple"
    type: ss
    server: 10.0.0.13
    port: 8388
    cipher: aes-256-gcm
    password: "your-password"
    udp: true
```

### Advanced Configuration

```yaml
proxies:
  - name: "ss-advanced"
    type: ss
    server: example.com
    port: 8388
    cipher: aes-256-gcm
    password: "your-password"
    udp: true
    # Simple obfuscation
    plugin: obfs
    plugin-opts:
      mode: tls
      host: example.com
```

### Supported Ciphers

- `aes-128-gcm`
- `aes-192-gcm`
- `aes-256-gcm`
- `chacha20-ietf-poly1305`
- `xchacha20-ietf-poly1305`
- `2022-blake3-aes-128-gcm`
- `2022-blake3-aes-256-gcm`
- `2022-blake3-chacha20-poly1305`

## VMess

### Basic VMess

```yaml
proxies:
  - name: "vmess-basic"
    type: vmess
    server: example.com
    port: 443
    uuid: 12345678-1234-1234-1234-123456789012
    alterId: 0
    cipher: auto
    udp: true
    skip-cert-verify: false
```

### VMess with WebSocket

```yaml
proxies:
  - name: "vmess-ws"
    type: vmess
    server: example.com
    port: 443
    uuid: 12345678-1234-1234-1234-123456789012
    alterId: 0
    cipher: auto
    udp: true
    tls: true
    skip-cert-verify: false
    servername: example.com
    network: ws
    ws-opts:
      path: /websocket
      headers:
        Host: example.com
        User-Agent: Mozilla/5.0
```

### VMess with gRPC

```yaml
proxies:
  - name: "vmess-grpc"
    type: vmess
    server: example.com
    port: 443
    uuid: 12345678-1234-1234-1234-123456789012
    alterId: 0
    cipher: auto
    udp: true
    tls: true
    skip-cert-verify: false
    network: grpc
    grpc-opts:
      grpc-service-name: GunService
```

### VMess with HTTP/2

```yaml
proxies:
  - name: "vmess-h2"
    type: vmess
    server: example.com
    port: 443
    uuid: 12345678-1234-1234-1234-123456789012
    alterId: 0
    cipher: auto
    udp: true
    tls: true
    skip-cert-verify: false
    network: h2
    h2-opts:
      host:
        - example.com
      path: /h2
```

### VMess Cipher Options

- `auto`: Automatic selection
- `aes-128-gcm`
- `chacha20-poly1305`
- `none`: No encryption (not recommended)

## VLess

VLess is a lightweight, stateless protocol from the V2Ray/Xray family. It supports Reality for TLS camouflage in addition to the standard WebSocket, gRPC, and H2 transports.

### Basic VLess

```yaml
proxies:
  - name: "vless-basic"
    type: vless
    server: example.com
    port: 443
    uuid: 12345678-1234-1234-1234-123456789012
    udp: true
    tls: true
    skip-cert-verify: false
    servername: example.com
```

### VLess with WebSocket

```yaml
proxies:
  - name: "vless-ws"
    type: vless
    server: example.com
    port: 443
    uuid: 12345678-1234-1234-1234-123456789012
    udp: true
    tls: true
    servername: example.com
    network: ws
    ws-opts:
      path: /ws
      headers:
        Host: example.com
```

### VLess with gRPC

```yaml
proxies:
  - name: "vless-grpc"
    type: vless
    server: example.com
    port: 443
    uuid: 12345678-1234-1234-1234-123456789012
    udp: true
    tls: true
    servername: example.com
    network: grpc
    grpc-opts:
      grpc-service-name: GunService
```

### VLess with Reality

Reality is an advanced TLS camouflage mechanism — the connection masquerades as real TLS traffic to a legitimate site.

```yaml
proxies:
  - name: "vless-reality"
    type: vless
    server: example.com
    port: 443
    uuid: 12345678-1234-1234-1234-123456789012
    udp: true
    tls: true
    servername: example.com         # SNI of the camouflage target
    client-fingerprint: chrome      # TLS fingerprint to mimic
    flow: xtls-rprx-vision          # optional XTLS flow
    reality-opts:
      public-key: your-reality-public-key
      short-id: your-short-id
```

## Trojan

### Basic Trojan

```yaml
proxies:
  - name: "trojan-basic"
    type: trojan
    server: example.com
    port: 443
    password: your-password
    udp: true
    sni: example.com
    skip-cert-verify: false
```

### Trojan with WebSocket

```yaml
proxies:
  - name: "trojan-ws"
    type: trojan
    server: example.com
    port: 443
    password: your-password
    udp: true
    sni: example.com
    skip-cert-verify: false
    network: ws
    ws-opts:
      path: /websocket
      headers:
        Host: example.com
```

### Trojan with gRPC

```yaml
proxies:
  - name: "trojan-grpc"
    type: trojan
    server: example.com
    port: 443
    password: your-password
    udp: true
    sni: example.com
    skip-cert-verify: false
    network: grpc
    grpc-opts:
      grpc-service-name: TrojanService
```

### Advanced Trojan Options

```yaml
proxies:
  - name: "trojan-advanced"
    type: trojan
    server: example.com
    port: 443
    password: your-password
    udp: true
    sni: example.com
    skip-cert-verify: false
    alpn:
      - h2
      - http/1.1
```

## Hysteria2

```yaml
proxies:
  - name: "hysteria2"
    type: hysteria2
    server: example.com
    port: 443
    password: your-password
    udp: true
    skip-cert-verify: false
    sni: example.com
    # Bandwidth settings
    up: 100
    down: 200
    # Congestion control
    cwnd: 32
    # Obfuscation
    obfs: salamander
    obfs-password: obfs-password
```

## Tuic

```yaml
proxies:
  - name: "tuic"
    type: tuic
    server: example.com
    port: 443
    uuid: 12345678-1234-1234-1234-123456789012
    password: your-password
    udp: true
    skip-cert-verify: false
    sni: example.com
    # QUIC settings
    alpn:
      - h3
    # UDP relay mode: native | quic
    udp-relay-mode: native
```

## ShadowQuic

ShadowQuic is a QUIC-based proxy that uses JLS obfuscation to blend in with legitimate QUIC/H3 traffic.

```yaml
proxies:
  - name: "shadowquic"
    type: shadowquic
    server: example.com
    port: 443
    password: your-jls-password
    username: your-jls-username
    server-name: example.com   # must match server's jls_upstream domain
    alpn:
      - h3
    # QUIC tuning (optional)
    initial-mtu: 1300          # must be >= 1200; 1400 for high-loss networks
    min-mtu: 1290
    congestion-control: bbr    # bbr | new-reno | cubic
    zero-rtt: true
    over-stream: false         # true = QUIC stream relay, false = QUIC datagram
    keep-alive-interval: 10000 # ms; 0 to disable
    gso: true
    mtu-discovery: true
```

## Wireguard

```yaml
proxies:
  - name: "wireguard"
    type: wireguard
    server: example.com
    port: 51820
    # WireGuard keys
    private-key: your-private-key
    public-key: server-public-key
    # Pre-shared key (optional)
    pre-shared-key: pre-shared-key
    # IP addresses
    ip: 10.0.0.2
    ipv6: fd00::2
    # Allowed IPs
    allowed-ips:
      - 0.0.0.0/0
      - ::/0
    # MTU
    mtu: 1420
    # UDP mode
    udp: true
```

## Tailscale

Tailscale embeds a Tailscale node directly inside ClashRS so you can route traffic through your Tailscale network (tsnet) without a separate Tailscale daemon.

{% hint style="info" %}
This outbound requires the binary to be built with the `tailscale` feature flag.
{% endhint %}

```yaml
proxies:
  - name: "tailscale"
    type: tailscale
    auth-key: tskey-auth-xxxx   # Tailscale auth key
    state-dir: /var/lib/clash/tailscale  # persistent state directory
    hostname: clash-node        # node hostname in your tailnet (optional)
    control-url: https://controlplane.tailscale.com  # optional, defaults to Tailscale's servers
    client-name: clash-rs       # optional client identifier
    ephemeral: false            # if true, node is removed from tailnet on exit
```

## SSH

```yaml
proxies:
  - name: "ssh"
    type: ssh
    server: example.com
    port: 22
    username: your-username
    password: your-password
    # Or use private key
    # private-key: |
    #   -----BEGIN OPENSSH PRIVATE KEY-----
    #   your-private-key-content
    #   -----END OPENSSH PRIVATE KEY-----
    # private-key-passphrase: key-passphrase
    # Host key verification
    host-key:
      - server-host-key
    host-key-algorithms:
      - rsa-sha2-512
      - rsa-sha2-256
```

## Tor

```yaml
proxies:
  - name: "tor"
    type: tor
```

{% hint style="info" %}
Tor traffic is routed through the Tor network managed internally by ClashRS. No server address or credentials are configured here — the only required field is `name`.
{% endhint %}

## SOCKS5

```yaml
proxies:
  - name: "socks5"
    type: socks5
    server: example.com
    port: 1080
    username: your-username
    password: your-password
    udp: true
    skip-cert-verify: false
    tls: false
```

## HTTP

{% hint style="warning" %}
HTTP outbound proxy (`type: http`) is **not currently implemented** in ClashRS. Including it in your config will cause a parse error. Use SOCKS5 instead.
{% endhint %}

```yaml
proxies:
  - name: "http"
    type: http
    server: example.com
    port: 8080
    username: your-username
    password: your-password
    tls: false
    skip-cert-verify: false
    sni: example.com
```

## Common Options

### TLS Options

Most protocols support TLS configuration:

```yaml
proxies:
  - name: "proxy-with-tls"
    type: trojan
    server: example.com
    port: 443
    password: your-password
    # TLS settings
    tls: true
    skip-cert-verify: false
    sni: example.com
    alpn:
      - h2
      - http/1.1
```

### Client Fingerprint (`client-fingerprint`)

The `client-fingerprint` field is supported on **VLess** and **AnyTLS** for uTLS browser impersonation. It is accepted by the parser but **not yet applied** at runtime (a warning is logged).

Accepted values:
- `chrome`
- `firefox`
- `safari`
- `ios`
- `android`
- `edge`
- `random`

### Certificate Fingerprint (`fingerprint`)

On **Hysteria2**, `fingerprint` is a **SHA-256 hex digest** of the server certificate used for certificate pinning — not a browser profile name.

### UDP Support

Enable UDP relay for protocols that support it:

```yaml
proxies:
  - name: "proxy-with-udp"
    type: shadowsocks
    server: example.com
    port: 8388
    cipher: aes-256-gcm
    password: your-password
    udp: true
```

## Proxy Groups

Organize proxies into logical groups:

```yaml
proxy-groups:
  # Manual selection
  - name: "manual"
    type: select
    proxies:
      - "ss-simple"
      - "vmess-ws"
      - "trojan-basic"
      - DIRECT
  
  # Automatic selection (fastest)
  - name: "auto"
    type: url-test
    proxies:
      - "ss-simple"
      - "vmess-ws"
      - "trojan-basic"
    url: "http://www.gstatic.com/generate_204"
    interval: 300
    timeout: 5000
    tolerance: 50
  
  # Failover
  - name: "fallback"
    type: fallback
    proxies:
      - "ss-simple"
      - "vmess-ws"
      - "trojan-basic"
    url: "http://www.gstatic.com/generate_204"
    interval: 300
  
  # Load balancing
  - name: "load-balance"
    type: load-balance
    proxies:
      - "ss-simple"
      - "vmess-ws"
      - "trojan-basic"
    url: "http://www.gstatic.com/generate_204"
    interval: 300
    strategy: round-robin
  
  # Proxy chain
  - name: "relay"
    type: relay
    proxies:
      - "ss-simple"
      - "trojan-basic"
```

## Built-in Proxies

Special built-in proxy names:
- `DIRECT`: Direct connection without proxy
- `REJECT`: Reject the connection
- `PASS`: Pass through (for transparent proxy)

## Configuration Tips

{% hint style="info" %}
**Performance Tips:**
- Use `aes-256-gcm` or `chacha20-ietf-poly1305` for Shadowsocks
- Enable `udp: true` for better performance with UDP traffic
- Use `alterId: 0` for VMess (recommended)
- Consider Hysteria2 or Tuic for high-speed connections
{% endhint %}

{% hint style="warning" %}
**Security Notes:**
- Never use `skip-cert-verify: true` in production
- Use strong passwords and regularly rotate them
- Keep proxy software updated
- Monitor for unusual traffic patterns
{% endhint %}

{% hint style="success" %}
**Best Practices:**
- Use descriptive names for proxies
- Group related proxies together
- Test connectivity with health checks
- Use proxy providers for dynamic proxy lists
{% endhint %}