# Traffic Routing Rules

This document covers all traffic routing rules available in ClashRS, including rule types, syntax, and practical examples.

## Overview

ClashRS uses a rule-based system to determine how traffic should be routed. Rules are processed in order from top to bottom, and the first matching rule determines the routing decision.

## Rule Structure

Each rule follows this basic format:

```yaml
rules:
  - RULE-TYPE,RULE-VALUE,PROXY-NAME
```

Where:
- `RULE-TYPE`: The type of rule (DOMAIN, IP-CIDR, etc.)
- `RULE-VALUE`: The value to match against
- `PROXY-NAME`: The proxy or proxy group to use

## Rule Types

### Domain Rules

#### DOMAIN

Exact domain matching:

```yaml
rules:
  - DOMAIN,google.com,proxy-us
  - DOMAIN,github.com,proxy-us
  - DOMAIN,example.com,DIRECT
```

#### DOMAIN-SUFFIX

Domain suffix matching (matches domain and all subdomains):

```yaml
rules:
  - DOMAIN-SUFFIX,google.com,proxy-us
  - DOMAIN-SUFFIX,github.com,proxy-us
  - DOMAIN-SUFFIX,apple.com,DIRECT
```

#### DOMAIN-KEYWORD

Domain keyword matching (matches any domain containing the keyword):

```yaml
rules:
  - DOMAIN-KEYWORD,google,proxy-us
  - DOMAIN-KEYWORD,youtube,proxy-us
  - DOMAIN-KEYWORD,facebook,proxy-us
```

#### DOMAIN-REGEX

Domain regex matching:

```yaml
rules:
  - DOMAIN-REGEX,^.*\.google\.com$,proxy-us
  - DOMAIN-REGEX,.*\.github\.io$,proxy-us
  - DOMAIN-REGEX,^[a-z0-9]+\.example\.com$,DIRECT
```

### IP Rules

#### IP-CIDR

IPv4 CIDR matching:

```yaml
rules:
  - IP-CIDR,192.168.0.0/16,DIRECT
  - IP-CIDR,10.0.0.0/8,DIRECT
  - IP-CIDR,172.16.0.0/12,DIRECT
  - IP-CIDR,127.0.0.0/8,DIRECT
```

#### IP-CIDR6

IPv6 CIDR matching:

```yaml
rules:
  - IP-CIDR6,::1/128,DIRECT
  - IP-CIDR6,fc00::/7,DIRECT
  - IP-CIDR6,fe80::/10,DIRECT
```

#### SRC-IP-CIDR

Source IP CIDR matching:

```yaml
rules:
  - SRC-IP-CIDR,192.168.1.0/24,proxy-home
  - SRC-IP-CIDR,10.0.0.0/8,DIRECT
  - SRC-IP-CIDR,172.16.0.0/12,DIRECT
```

#### GEOIP

Geographic IP matching:

```yaml
rules:
  - GEOIP,CN,DIRECT
  - GEOIP,US,proxy-us
  - GEOIP,JP,proxy-jp
  - GEOIP,KR,proxy-kr
```

### Port Rules

#### DST-PORT

Destination port matching:

```yaml
rules:
  - DST-PORT,80,DIRECT
  - DST-PORT,443,DIRECT
  - DST-PORT,22,proxy-ssh
  - DST-PORT,21,proxy-ftp
```

#### SRC-PORT

Source port matching:

```yaml
rules:
  - SRC-PORT,8080,DIRECT
  - SRC-PORT,1080,proxy-socks
  - SRC-PORT,7890,DIRECT
```


### Rule Sets

#### RULE-SET

External rule set matching:

```yaml
rules:
  - RULE-SET,ads-block,REJECT
  - RULE-SET,cn-domains,DIRECT
  - RULE-SET,us-domains,proxy-us
  - RULE-SET,streaming,proxy-media
```

### Network Rules

#### NETWORK

Network type matching:

```yaml
rules:
  - NETWORK,tcp,auto
  - NETWORK,udp,proxy-udp
```

### Final Rules

#### MATCH

Catch-all rule (must be last):

```yaml
rules:
  - MATCH,auto
```

#### FINAL

Alternative syntax for catch-all:

```yaml
rules:
  - FINAL,DIRECT
```

## Rule Sets

Rule sets allow you to organize rules into external files for better management.

### Rule Set Configuration

```yaml
rule-providers:
  ads-block:
    type: http
    url: "https://example.com/ads-block.yaml"
    interval: 86400
    path: ./rules/ads-block.yaml
    behavior: domain
  
  cn-domains:
    type: file
    path: ./rules/cn-domains.yaml
    behavior: domain
  
  us-ip:
    type: http
    url: "https://example.com/us-ip.yaml"
    interval: 86400
    path: ./rules/us-ip.yaml
    behavior: ipcidr
  
  classical-rules:
    type: file
    path: ./rules/classical.yaml
    behavior: classical
```

### Rule Set Types

#### Domain Behavior

```yaml
# rules/domain-set.yaml
payload:
  - google.com
  - github.com
  - stackoverflow.com
```

#### IP CIDR Behavior

```yaml
# rules/ipcidr-set.yaml
payload:
  - 192.168.0.0/16
  - 10.0.0.0/8
  - 172.16.0.0/12
```

#### Classical Behavior

```yaml
# rules/classical-set.yaml
payload:
  - DOMAIN-SUFFIX,google.com
  - DOMAIN-KEYWORD,youtube
  - IP-CIDR,192.168.0.0/16
```

### Using Rule Sets

```yaml
rules:
  - RULE-SET,ads-block,REJECT
  - RULE-SET,cn-domains,DIRECT
  - RULE-SET,us-ip,proxy-us
  - RULE-SET,classical-rules,auto
```

## Advanced Rule Examples

### Complete Rule Configuration

```yaml
rules:
  # Local network
  - DOMAIN,localhost,DIRECT
  - IP-CIDR,127.0.0.0/8,DIRECT
  - IP-CIDR,192.168.0.0/16,DIRECT
  - IP-CIDR,10.0.0.0/8,DIRECT
  - IP-CIDR,172.16.0.0/12,DIRECT
  - IP-CIDR,169.254.0.0/16,DIRECT
  - IP-CIDR,224.0.0.0/4,DIRECT
  
  # IPv6 local
  - IP-CIDR6,::1/128,DIRECT
  - IP-CIDR6,fc00::/7,DIRECT
  - IP-CIDR6,fe80::/10,DIRECT
  
  # Block ads
  - RULE-SET,ads-block,REJECT
  
  # Streaming services
  - DOMAIN-SUFFIX,netflix.com,proxy-streaming
  - DOMAIN-SUFFIX,hulu.com,proxy-streaming
  - DOMAIN-SUFFIX,disney.com,proxy-streaming
  - DOMAIN-KEYWORD,youtube,proxy-streaming
  
  # Social media
  - DOMAIN-SUFFIX,facebook.com,proxy-social
  - DOMAIN-SUFFIX,twitter.com,proxy-social
  - DOMAIN-SUFFIX,instagram.com,proxy-social
  
  # Development tools
  - DOMAIN-SUFFIX,github.com,proxy-dev
  - DOMAIN-SUFFIX,stackoverflow.com,proxy-dev
  - DOMAIN-SUFFIX,npmjs.com,proxy-dev
  
  # Process-specific rules
  - PROCESS-NAME,curl,DIRECT
  - PROCESS-NAME,wget,DIRECT
  - PROCESS-NAME,ssh,proxy-ssh
  
  # Port-specific rules
  - DST-PORT,53,DIRECT
  - DST-PORT,80,auto
  - DST-PORT,443,auto
  - DST-PORT,22,proxy-ssh
  
  # Geographic rules
  - GEOIP,CN,DIRECT
  - GEOIP,US,proxy-us
  - GEOIP,JP,proxy-jp
  
  # Source-based rules
  - SRC-IP-CIDR,192.168.1.0/24,proxy-home
  - SRC-IP-CIDR,192.168.2.0/24,proxy-work
  
  # Final rule
  - MATCH,auto
```

### Gaming Rules

```yaml
rules:
  # Gaming platforms
  - DOMAIN-SUFFIX,steam.com,proxy-gaming
  - DOMAIN-SUFFIX,epicgames.com,proxy-gaming
  - DOMAIN-SUFFIX,battle.net,proxy-gaming
  - DOMAIN-SUFFIX,riotgames.com,proxy-gaming
  
  # Gaming ports
  - DST-PORT,25565,proxy-gaming  # Minecraft
  - DST-PORT,7777,proxy-gaming   # Common game port
  - DST-PORT,27015,proxy-gaming  # Source engine games
```

### Work/Enterprise Rules

```yaml
rules:
  # Corporate domains
  - DOMAIN-SUFFIX,company.com,DIRECT
  - DOMAIN-SUFFIX,corp.local,DIRECT
  - DOMAIN-KEYWORD,intranet,DIRECT
  
  # VPN detection bypass
  - DOMAIN-SUFFIX,whatismyipaddress.com,DIRECT
  - DOMAIN-SUFFIX,ipinfo.io,DIRECT
  
  # Work applications
  - DOMAIN-SUFFIX,slack.com,proxy-work
  - DOMAIN-SUFFIX,zoom.us,proxy-work
  - DOMAIN-SUFFIX,teams.microsoft.com,proxy-work
  
  # Source IP based on office networks
  - SRC-IP-CIDR,10.0.0.0/8,proxy-work
  - SRC-IP-CIDR,172.16.0.0/12,proxy-work
```

## Rule Optimization

### Rule Order Optimization

```yaml
rules:
  # 1. Most specific rules first
  - DOMAIN,specific.example.com,proxy-special
  - DOMAIN-SUFFIX,example.com,proxy-general
  
  # 2. Frequently matched rules early
  - GEOIP,CN,DIRECT  # Common in China
  - DOMAIN-KEYWORD,google,proxy-us
  
  # 3. Expensive rules last
  - DOMAIN-REGEX,.*\.complex\.pattern\.com$,proxy-complex
  
  # 4. Catch-all rule must be last
  - MATCH,auto
```

### Performance Tips

{% hint style="info" %}
**Rule Performance:**
- Use DOMAIN-SUFFIX instead of DOMAIN-KEYWORD when possible
- Place frequently matched rules at the top
- Use GEOIP rules for geographic routing
- Avoid complex regex patterns in high-traffic scenarios
{% endhint %}

### Rule Debugging

Enable rule logging to debug routing decisions:

```yaml
log-level: debug
```

Look for logs like:
```
[Router] match rule: DOMAIN-SUFFIX,google.com -> proxy-us
```

## Common Rule Patterns

### Bypass Rules

```yaml
rules:
  # Bypass local traffic
  - DOMAIN,localhost,DIRECT
  - IP-CIDR,127.0.0.0/8,DIRECT
  - IP-CIDR,192.168.0.0/16,DIRECT
  - IP-CIDR,10.0.0.0/8,DIRECT
  - IP-CIDR,172.16.0.0/12,DIRECT
  
  # Bypass Chinese websites
  - GEOIP,CN,DIRECT
  - DOMAIN-SUFFIX,baidu.com,DIRECT
  - DOMAIN-SUFFIX,qq.com,DIRECT
  - DOMAIN-SUFFIX,taobao.com,DIRECT
```

### Blocking Rules

```yaml
rules:
  # Block ads
  - DOMAIN-SUFFIX,doubleclick.net,REJECT
  - DOMAIN-SUFFIX,googleadservices.com,REJECT
  - DOMAIN-KEYWORD,analytics,REJECT
  - DOMAIN-KEYWORD,tracker,REJECT
  
  # Block malware
  - DOMAIN-SUFFIX,malware.com,REJECT
  - DOMAIN-SUFFIX,phishing.com,REJECT
```

### Streaming Rules

```yaml
rules:
  # Netflix
  - DOMAIN-SUFFIX,netflix.com,proxy-streaming
  - DOMAIN-SUFFIX,nflximg.net,proxy-streaming
  - DOMAIN-SUFFIX,nflxext.com,proxy-streaming
  - DOMAIN-SUFFIX,nflxso.net,proxy-streaming
  
  # YouTube
  - DOMAIN-SUFFIX,youtube.com,proxy-streaming
  - DOMAIN-SUFFIX,googlevideo.com,proxy-streaming
  - DOMAIN-SUFFIX,ytimg.com,proxy-streaming
  
  # Disney+
  - DOMAIN-SUFFIX,disneyplus.com,proxy-streaming
  - DOMAIN-SUFFIX,disney-plus.net,proxy-streaming
  - DOMAIN-SUFFIX,disneystreaming.com,proxy-streaming
```

## Best Practices

{% hint style="success" %}
**Rule Best Practices:**
- Keep rules organized and commented
- Use rule sets for better management
- Test rules with different traffic types
- Monitor rule performance and adjust order
- Use specific rules before general ones
- Always include a MATCH rule at the end
{% endhint %}

{% hint style="warning" %}
**Common Pitfalls:**
- Forgetting the final MATCH rule
- Using overly broad DOMAIN-KEYWORD rules
- Not considering rule order for performance
- Complex regex patterns affecting performance
- Not testing rules with real traffic
{% endhint %}

{% hint style="info" %}
**Rule Maintenance:**
- Regularly update rule sets
- Remove unused rules
- Monitor rule hit rates
- Update geographic rules as needed
- Keep rule documentation current
{% endhint %}