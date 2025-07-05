# DNS Configuration

This document covers DNS configuration in ClashRS, including its built-in DNS server capabilities, resolution modes, and anti-spoofing features.

## Overview

ClashRS includes a comprehensive DNS subsystem that can act as both a DNS client and server. The DNS functionality is optional but provides enhanced routing capabilities, security features, and performance optimizations when enabled.

## Basic DNS Configuration

### Enable DNS Server

```yaml
dns:
  enable: true
  listen: 127.0.0.1:53553
```

### Complete Basic Setup

```yaml
dns:
  enable: true
  listen: 127.0.0.1:53553
  default-nameserver:
    - 114.114.114.114
    - 8.8.8.8
  nameserver:
    - 114.114.114.114
    - 1.1.1.1
```

## DNS Resolution Modes

ClashRS supports two primary DNS resolution modes that affect how domain names are resolved and routed.

### Fake-IP Mode

Fake-IP mode uses RFC 3089 fake IP addresses for enhanced routing capabilities:

```yaml
dns:
  enable: true
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.2/16
  nameserver:
    - 114.114.114.114
    - 1.1.1.1
```

**How Fake-IP Works:**
- Allocates fake IP addresses from a managed pool
- Maintains internal mapping between domain names and fake IPs
- Enables domain-based routing without real DNS resolution
- Improves performance and routing accuracy

### Redir-Host Mode

Traditional DNS resolution with real IP addresses:

```yaml
dns:
  enable: true
  enhanced-mode: redir-host
  nameserver:
    - 114.114.114.114
    - 1.1.1.1
```

**Redir-Host Behavior:**
- Resolves FQDNs using configured nameservers
- Returns actual IP addresses to clients
- Standard proxy behavior compatible with all applications

## DNS Protocols

ClashRS supports all major DNS protocols with multiple server endpoints.

### Protocol-Specific Listeners

```yaml
dns:
  enable: true
  listen: 127.0.0.1:53553    # Main DNS port
  # Optional specific protocol ports:
  # udp: 127.0.0.1:53553     # UDP DNS
  # tcp: 127.0.0.1:53553     # TCP DNS
  # dot: 127.0.0.1:53554     # DNS over TLS
  # doh: 127.0.0.1:53555     # DNS over HTTPS
```

### Nameserver Configuration

```yaml
dns:
  nameserver:
    - 114.114.114.114           # UDP DNS
    - 1.1.1.1                   # UDP DNS
    - tls://1.1.1.1:853         # DNS over TLS
    - https://1.1.1.1/dns-query # DNS over HTTPS
    - tcp://8.8.8.8:53          # TCP DNS
```

## DNS Server Types

### Default Nameservers

Bootstrap nameservers used to resolve other nameserver hostnames:

```yaml
dns:
  default-nameserver:
    - 114.114.114.114  # Must be IP addresses only
    - 8.8.8.8          # Used for bootstrap resolution
```

{% hint style="warning" %}
**Important**: Default nameservers must be IP addresses only, not domain names.
{% endhint %}

### Primary Nameservers

Main DNS servers for resolution:

```yaml
dns:
  nameserver:
    - 114.114.114.114
    - 1.1.1.1
    - tls://1.1.1.1:853
    - https://1.1.1.1/dns-query
```

### Fallback Nameservers

Secondary DNS servers for redundancy:

```yaml
dns:
  fallback:
    - 8.8.8.8
    - 1.1.1.1
    - tls://8.8.8.8:853
```

## DNS Filtering and Policies

### Host Override

Override DNS resolution for specific domains:

```yaml
hosts:
  'mtalk.google.com': 108.177.125.188
  'dl.google.com': 180.163.151.161
  'example.com': 192.168.1.100
  'internal.company.com': 10.0.0.100
```

### Fake-IP Filtering

Exclude specific domains from fake-IP resolution:

```yaml
dns:
  fake-ip-filter:
    - '*.lan'
    - '*.local'
    - localhost.ptlogin2.qq.com
    - '*.home'
    - 'router.asus.com'
```

**Common Exclusions:**
- Local network domains
- Router management interfaces
- Gaming platforms that require real IPs
- Applications sensitive to fake IPs

### IPv6 Control

Control IPv6 DNS responses:

```yaml
dns:
  ipv6: false  # When false, AAAA queries return empty responses
```

## Advanced DNS Features

### DNS Hijacking (TUN Mode)

When using TUN mode, ClashRS can hijack DNS queries at the network level:

```yaml
tun:
  enable: true
  dns-hijack:
    - 8.8.8.8:53
    - tcp://8.8.8.8:53
    - any:53  # Hijack all DNS queries

dns:
  enable: true
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.2/16
```

### Hosts File Integration

Enable system hosts file lookup:

```yaml
dns:
  use-hosts: true  # Enable hosts file lookup
```

### DNS Resolution Strategy

ClashRS uses a "first result" strategy:
- Queries are sent to all configured nameservers simultaneously
- The first response received is returned to the client
- Provides both speed and redundancy

## Complete DNS Configuration Example

```yaml
dns:
  enable: true
  listen: 127.0.0.1:53553
  ipv6: false
  use-hosts: true
  
  # DNS resolution mode
  enhanced-mode: fake-ip
  fake-ip-range: 198.18.0.2/16
  
  # Bootstrap nameservers (IP addresses only)
  default-nameserver:
    - 114.114.114.114
    - 8.8.8.8
  
  # Primary nameservers
  nameserver:
    - 114.114.114.114
    - 1.1.1.1
    - tls://1.1.1.1:853
    - https://1.1.1.1/dns-query
  
  # Fallback nameservers
  fallback:
    - 8.8.8.8
    - 1.1.1.1
  
  # Fallback filter (optional)
  fallback-filter:
    geoip: true
    geoip-code: CN
    ipcidr:
      - 240.0.0.0/4
  
  # Exclude domains from fake-IP
  fake-ip-filter:
    - '*.lan'
    - '*.local'
    - localhost.ptlogin2.qq.com

# Host overrides
hosts:
  'localhost': 127.0.0.1
  'router.local': 192.168.1.1
  'internal.company.com': 10.0.0.100

# TUN DNS hijacking (if using TUN mode)
tun:
  enable: true
  dns-hijack:
    - 8.8.8.8:53
    - tcp://8.8.8.8:53
```

## DNS and Proxy Routing Integration

### Route DNS Queries Through Proxies

```yaml
rules:
  # Route DNS queries through specific proxy
  - DST-PORT,53,proxy-dns
  - IP-CIDR,8.8.8.8/32,proxy-dns
  - IP-CIDR,1.1.1.1/32,proxy-dns
```

### Domain-Based Routing

```yaml
rules:
  # Route specific domains through proxy groups
  - DOMAIN,google.com,proxy-us
  - DOMAIN-SUFFIX,youtube.com,proxy-us
  - DOMAIN-KEYWORD,github,proxy-dev
```

## DNS Security Features

### Anti-Spoofing Protection

ClashRS includes built-in anti-spoofing features:
- Validates DNS responses against expected queries
- Maintains state to prevent cache poisoning attacks
- Integrates with proxy routing for secure resolution

### Encrypted DNS Support

Use encrypted DNS to prevent eavesdropping:

```yaml
dns:
  nameserver:
    - tls://1.1.1.1:853         # DNS over TLS
    - https://1.1.1.1/dns-query # DNS over HTTPS
    - tls://8.8.8.8:853
    - https://8.8.8.8/dns-query
```

## Performance Optimization

### DNS Caching

ClashRS automatically caches DNS responses for performance:
- Respects TTL values from DNS responses
- Maintains separate cache for fake-IP mappings
- Provides fast resolution for frequently accessed domains

### Concurrent Resolution

Multiple nameservers are queried simultaneously:
- First response is used
- Provides redundancy and speed
- Balances load across nameservers

## Platform-Specific Features

### Linux DNS Hijacking

```yaml
tun:
  enable: true
  dns-hijack:
    - any:53  # Hijack all DNS traffic
```

### macOS DNS Configuration

```yaml
dns:
  enable: true
  listen: 127.0.0.1:53553
  # Configure system DNS to use 127.0.0.1:53553
```

## Troubleshooting DNS Issues

### Common Problems

1. **DNS Resolution Failures**
   - Check nameserver connectivity
   - Verify default-nameserver configuration
   - Test with different DNS protocols

2. **Fake-IP Issues**
   - Add problematic domains to fake-ip-filter
   - Consider using redir-host mode
   - Check fake-ip-range conflicts

3. **Performance Issues**
   - Use geographically closer nameservers
   - Enable DNS caching
   - Reduce number of concurrent nameservers

### Debug Configuration

```yaml
log-level: debug
dns:
  enable: true
  listen: 127.0.0.1:53553
  # Enable detailed DNS logging
```

## Best Practices

{% hint style="success" %}
**DNS Best Practices:**
- Use fake-ip mode for better routing performance
- Configure both primary and fallback nameservers
- Use encrypted DNS protocols when possible
- Properly configure fake-ip-filter for compatibility
- Test DNS resolution with different applications
- Monitor DNS performance and adjust nameservers
{% endhint %}

{% hint style="info" %}
**Performance Tips:**
- Use geographically close nameservers
- Enable fake-ip mode for domain-based routing
- Configure appropriate fake-ip-range size
- Use concurrent nameserver resolution
- Cache DNS responses effectively
{% endhint %}

{% hint style="warning" %}
**Security Considerations:**
- Use encrypted DNS protocols (DoH/DoT)
- Validate nameserver certificates
- Monitor for DNS hijacking attempts
- Regularly update DNS configurations
- Use trusted DNS providers
{% endhint %}