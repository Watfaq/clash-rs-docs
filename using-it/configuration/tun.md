# TUN Configuration

ClashRS can create a virtual TUN (layer-3) network interface to transparently intercept all traffic on the machine — no per-application proxy settings required. This is the foundation for gateway and transparent-proxy setups.

## Basic TUN Setup

```yaml
tun:
  enable: true
  gateway: 198.18.0.1/24   # TUN interface address (CIDR)
  dns-hijack: true          # redirect all DNS queries to ClashRS DNS
```

## Full Configuration Reference

```yaml
tun:
  enable: true

  # TUN interface device ID
  # Accepts aliases: "device-url", "device"
  # macOS: "utun1989" or "dev://utun1989"  (must start with "utun")
  # Linux: "tun0" or "dev://tun0"
  # file descriptor: "fd://3"
  device-id: "utun1989"

  # TUN interface IPv4 address/prefix
  gateway: 198.18.0.1/24

  # TUN interface IPv6 address/prefix (optional)
  # gateway-v6: "2001:fac::1/64"

  # MTU for the TUN interface (optional, defaults to system default)
  mtu: 9000

  # Static routes to push into the TUN interface (optional).
  # route-all: true is simpler and routes everything.
  routes:
    - 0.0.0.0/1
    - 128.0.0.0/1

  # Route ALL traffic through TUN (simpler alternative to routes list)
  route-all: false

  # Linux only: fwmark to set on outbound packets
  so-mark: 666

  # Linux only: policy routing table to use
  route-table: 2468

  # DNS hijack: redirect DNS queries to the ClashRS DNS server.
  # true  - hijack all UDP/TCP port 53 traffic
  # false - no hijacking (default)
  # list  - same effect as true (hijacks all DNS, not restricted to listed addresses)
  dns-hijack: true
  # dns-hijack:
  #   - 8.8.8.8:53
  #   - tcp://8.8.8.8:53
```

## Platform Notes

{% hint style="info" %}
**macOS**: The device name must start with `utun` (e.g. `utun1989`). ClashRS will create the interface automatically.
{% endhint %}

{% hint style="info" %}
**Linux**: The device name is typically `tun0`. You may need `CAP_NET_ADMIN` capability or root privileges.
{% endhint %}

{% hint style="warning" %}
**Windows**: You must place `wintun.dll` (matching your architecture) in the same directory as the ClashRS binary and run as Administrator.
{% endhint %}

## DNS Hijacking

When TUN mode is active, pair it with the ClashRS DNS server to ensure domain-based rules work correctly:

```yaml
dns:
  enable: true
  listen: 127.0.0.1:53553
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.2/16
  nameserver:
    - 1.1.1.1
    - 8.8.8.8

tun:
  enable: true
  gateway: 198.19.0.1/16
  dns-hijack: true
```

{% hint style="info" %}
Use a different subnet for `gateway` and `fake-ip-range` to avoid conflicts (e.g. `198.19.0.1/16` for TUN and `198.18.0.2/16` for fake-ip).
{% endhint %}

## Transparent Proxy (Gateway Mode)

For a full gateway setup where other devices route traffic through this machine, see the [Gateway use case](../../use-cases/as-a-gateway/README.md).

Quick example for a single machine:

```yaml
tun:
  enable: true
  gateway: 198.19.0.1/16
  route-all: true
  dns-hijack: true

dns:
  enable: true
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.2/16
  nameserver:
    - tls://1.1.1.1:853
    - tls://8.8.8.8:853

rules:
  - GEOIP,CN,DIRECT
  - MATCH,proxy
```
