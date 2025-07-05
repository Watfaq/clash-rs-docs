# Remote Content Management

This document covers proxy providers and rule providers in ClashRS, which allow you to load and manage proxies and rules from external sources dynamically.

## Overview

ClashRS supports two types of remote content providers:
- **Proxy Providers**: Load proxy configurations from external sources
- **Rule Providers**: Load routing rules from external sources

Both providers support automatic updates, health checks, and local caching for reliability.

## Proxy Providers

Proxy providers allow you to load proxy configurations from external files or URLs, enabling dynamic proxy management and automatic updates.

### Basic Configuration

```yaml
proxy-providers:
  provider-name:
    type: http  # or file
    url: "https://example.com/proxies.yaml"
    interval: 3600  # Update interval in seconds
    path: ./proxies.yaml  # Local cache path
    health-check:
      enable: true
      url: "http://www.gstatic.com/generate_204"
      interval: 300
```

### HTTP Provider

Load proxies from a remote URL:

```yaml
proxy-providers:
  remote-proxies:
    type: http
    url: "https://example.com/subscription.yaml"
    interval: 86400  # 24 hours
    path: ./providers/remote-proxies.yaml
    health-check:
      enable: true
      url: "http://www.gstatic.com/generate_204"
      interval: 300
      timeout: 5000
      lazy: true
```

### File Provider

Load proxies from a local file:

```yaml
proxy-providers:
  local-proxies:
    type: file
    path: ./proxies/local.yaml
    interval: 3600
    health-check:
      enable: true
      url: "http://www.gstatic.com/generate_204"
      interval: 300
```

### Proxy Provider Format

The proxy provider file should contain a list of proxies:

```yaml
# proxies.yaml
proxies:
  - name: "server-1"
    type: ss
    server: 1.2.3.4
    port: 8388
    cipher: aes-256-gcm
    password: "password1"
    udp: true
    
  - name: "server-2"
    type: vmess
    server: 5.6.7.8
    port: 443
    uuid: 12345678-1234-1234-1234-123456789012
    alterId: 0
    cipher: auto
    tls: true
    
  - name: "server-3"
    type: trojan
    server: 9.10.11.12
    port: 443
    password: "password2"
    sni: example.com
```

### Using Proxy Providers

Reference proxy providers in proxy groups:

```yaml
proxy-groups:
  - name: "auto-select"
    type: url-test
    use:
      - remote-proxies
      - local-proxies
    url: "http://www.gstatic.com/generate_204"
    interval: 300
    
  - name: "manual-select"
    type: select
    use:
      - remote-proxies
    proxies:
      - DIRECT
      - REJECT
```

## Rule Providers

Rule providers allow you to load routing rules from external sources, enabling centralized rule management and automatic updates.

### Basic Configuration

```yaml
rule-providers:
  provider-name:
    type: http  # or file
    behavior: domain  # domain, ipcidr, or classical
    url: "https://example.com/rules.yaml"
    interval: 86400
    path: ./rules.yaml
```

### Rule Provider Types

#### Domain Behavior

For domain-based rules:

```yaml
rule-providers:
  ads-domains:
    type: http
    behavior: domain
    url: "https://example.com/ads-domains.yaml"
    interval: 86400
    path: ./rules/ads-domains.yaml
```

Domain rule format:
```yaml
# ads-domains.yaml
payload:
  - google-analytics.com
  - googletagmanager.com
  - doubleclick.net
  - facebook.com
  - twitter.com
```

#### IP CIDR Behavior

For IP-based rules:

```yaml
rule-providers:
  cn-ip:
    type: http
    behavior: ipcidr
    url: "https://example.com/cn-ip.yaml"
    interval: 86400
    path: ./rules/cn-ip.yaml
```

IP CIDR rule format:
```yaml
# cn-ip.yaml
payload:
  - 1.0.1.0/24
  - 1.0.2.0/23
  - 1.0.8.0/21
  - 1.0.32.0/19
  - 1.1.0.0/16
```

#### Classical Behavior

For mixed rule types:

```yaml
rule-providers:
  mixed-rules:
    type: http
    behavior: classical
    url: "https://example.com/mixed-rules.yaml"
    interval: 86400
    path: ./rules/mixed-rules.yaml
```

Classical rule format:
```yaml
# mixed-rules.yaml
payload:
  - DOMAIN-SUFFIX,google.com
  - DOMAIN-KEYWORD,youtube
  - IP-CIDR,192.168.0.0/16
  - DST-PORT,80
  - GEOIP,US
```

### File Rule Providers

Load rules from local files:

```yaml
rule-providers:
  local-ads:
    type: file
    behavior: domain
    path: ./rules/local-ads.yaml
    interval: 0  # No auto-refresh for file providers
  
  local-bypass:
    type: file
    behavior: classical
    path: ./rules/local-bypass.yaml
```

### Using Rule Providers

Reference rule providers in the rules section:

```yaml
rules:
  # Use rule providers
  - RULE-SET,ads-domains,REJECT
  - RULE-SET,cn-ip,DIRECT
  - RULE-SET,mixed-rules,proxy-auto
  
  # Regular rules
  - DOMAIN-SUFFIX,github.com,proxy-dev
  - GEOIP,CN,DIRECT
  - MATCH,proxy-auto
```

### Advanced Rule Provider Configuration

```yaml
rule-providers:
  comprehensive-rules:
    type: http
    behavior: classical
    url: "https://rules.example.com/comprehensive.yaml"
    interval: 21600  # 6 hours
    path: ./rules/comprehensive.yaml
    
    # Custom headers
    headers:
      Authorization: "Bearer your-token"
      User-Agent: "ClashRS/1.0"
      Accept: "application/yaml"
    
    # Format specification
    format: yaml  # yaml or text
```

## Complete Example Configuration

Here's a complete example showing both proxy and rule providers:

```yaml
# Proxy providers
proxy-providers:
  subscription-1:
    type: http
    url: "https://sub1.example.com/proxies.yaml"
    interval: 86400
    path: ./providers/sub1.yaml
    health-check:
      enable: true
      url: "http://www.gstatic.com/generate_204"
      interval: 300
      timeout: 5000
      lazy: true
  
  subscription-2:
    type: http
    url: "https://sub2.example.com/proxies.yaml"
    interval: 86400
    path: ./providers/sub2.yaml
    health-check:
      enable: true
      url: "http://www.gstatic.com/generate_204"
      interval: 300
  
  local-backup:
    type: file
    path: ./proxies/backup.yaml
    health-check:
      enable: true
      url: "http://www.gstatic.com/generate_204"
      interval: 600

# Rule providers
rule-providers:
  ads-block:
    type: http
    behavior: domain
    url: "https://rules.example.com/ads.yaml"
    interval: 86400
    path: ./rules/ads.yaml
  
  cn-domains:
    type: http
    behavior: domain
    url: "https://rules.example.com/cn-domains.yaml"
    interval: 86400
    path: ./rules/cn-domains.yaml
  
  cn-ip:
    type: http
    behavior: ipcidr
    url: "https://rules.example.com/cn-ip.yaml"
    interval: 86400
    path: ./rules/cn-ip.yaml
  
  streaming:
    type: http
    behavior: classical
    url: "https://rules.example.com/streaming.yaml"
    interval: 86400
    path: ./rules/streaming.yaml
  
  custom-rules:
    type: file
    behavior: classical
    path: ./rules/custom.yaml

# Proxy groups using providers
proxy-groups:
  - name: "auto"
    type: url-test
    use:
      - subscription-1
      - subscription-2
    url: "http://www.gstatic.com/generate_204"
    interval: 300
    tolerance: 50
  
  - name: "manual"
    type: select
    use:
      - subscription-1
      - subscription-2
      - local-backup
    proxies:
      - DIRECT
  
  - name: "fallback"
    type: fallback
    use:
      - subscription-1
      - subscription-2
    url: "http://www.gstatic.com/generate_204"
    interval: 300
  
  - name: "load-balance"
    type: load-balance
    use:
      - subscription-1
      - subscription-2
    url: "http://www.gstatic.com/generate_204"
    interval: 300
    strategy: round-robin

# Rules using providers
rules:
  # Block ads
  - RULE-SET,ads-block,REJECT
  
  # Chinese content direct
  - RULE-SET,cn-domains,DIRECT
  - RULE-SET,cn-ip,DIRECT
  
  # Streaming services
  - RULE-SET,streaming,manual
  
  # Custom rules
  - RULE-SET,custom-rules,auto
  
  # Fallback rules
  - GEOIP,CN,DIRECT
  - MATCH,auto
```

## Provider Management

### Manual Updates

Force update providers using the REST API:

```bash
# Update proxy provider
curl -X PUT "http://127.0.0.1:9090/providers/proxies/subscription-1"

# Update rule provider
curl -X PUT "http://127.0.0.1:9090/providers/rules/ads-block"

# Update all providers
curl -X PUT "http://127.0.0.1:9090/providers/proxies"
curl -X PUT "http://127.0.0.1:9090/providers/rules"
```

### Health Check Management

```bash
# Check proxy health
curl "http://127.0.0.1:9090/providers/proxies/subscription-1/healthcheck"

# Force health check
curl -X GET "http://127.0.0.1:9090/providers/proxies/subscription-1/healthcheck"
```

### Provider Status

```bash
# Get provider status
curl "http://127.0.0.1:9090/providers/proxies"
curl "http://127.0.0.1:9090/providers/rules"
```

## Best Practices

### Provider Organization

```yaml
# Organize providers by purpose
proxy-providers:
  # Regional providers
  us-servers:
    type: http
    url: "https://us.example.com/proxies.yaml"
    interval: 86400
    path: ./providers/us.yaml
  
  eu-servers:
    type: http
    url: "https://eu.example.com/proxies.yaml"
    interval: 86400
    path: ./providers/eu.yaml
  
  asia-servers:
    type: http
    url: "https://asia.example.com/proxies.yaml"
    interval: 86400
    path: ./providers/asia.yaml

rule-providers:
  # Rule organization
  security-rules:
    type: http
    behavior: domain
    url: "https://security.example.com/rules.yaml"
    interval: 21600
    path: ./rules/security.yaml
  
  privacy-rules:
    type: http
    behavior: domain
    url: "https://privacy.example.com/rules.yaml"
    interval: 21600
    path: ./rules/privacy.yaml
  
  bypass-rules:
    type: file
    behavior: classical
    path: ./rules/bypass.yaml
```

### Error Handling

```yaml
proxy-providers:
  reliable-provider:
    type: http
    url: "https://reliable.example.com/proxies.yaml"
    interval: 86400
    path: ./providers/reliable.yaml
    
    # Fallback configuration
    health-check:
      enable: true
      url: "http://www.gstatic.com/generate_204"
      interval: 300
      timeout: 5000
      lazy: false
      
    # Retry configuration
    expected-status: 200
```

## Troubleshooting

### Common Issues

1. **Provider Update Failures**
   - Check network connectivity
   - Verify URL accessibility
   - Check authentication headers
   - Review local file permissions

2. **Health Check Failures**
   - Verify health check URL
   - Check proxy connectivity
   - Review timeout settings
   - Monitor proxy server status

3. **Rule Provider Issues**
   - Verify rule format
   - Check behavior type
   - Review rule syntax
   - Test rule matching

### Debug Configuration

```yaml
log-level: debug

proxy-providers:
  debug-provider:
    type: http
    url: "https://debug.example.com/proxies.yaml"
    interval: 300  # Shorter interval for debugging
    path: ./debug/proxies.yaml
    health-check:
      enable: true
      url: "http://www.gstatic.com/generate_204"
      interval: 60
      timeout: 3000
      lazy: false
```

## Security Considerations

{% hint style="warning" %}
**Security Notes:**
- Use HTTPS URLs for remote providers
- Implement proper authentication for sensitive providers
- Regularly update provider URLs and credentials
- Monitor provider access logs
- Use local backups for critical configurations
{% endhint %}

{% hint style="info" %}
**Performance Tips:**
- Set appropriate update intervals
- Use lazy health checks for large provider lists
- Cache providers locally for reliability
- Monitor provider update frequency
- Use efficient rule provider behaviors
{% endhint %}

{% hint style="success" %}
**Best Practices:**
- Organize providers by purpose and region
- Implement proper error handling
- Use version control for local provider files
- Document provider sources and purposes
- Monitor provider health and performance
{% endhint %}