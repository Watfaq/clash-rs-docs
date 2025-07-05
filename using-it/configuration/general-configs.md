# General Configs

## Inbound Config

### HTTP Inbound

The HTTP proxy listening port

{% code title="Example:" %}
```yaml
port: 8888
```
{% endcode %}

### **SOCKS5 Inbound**

The SOCKS5 proxy listening port

```yaml
socks-port: 8889
```

### **TPROXY Inbound**

```yaml
tproxy-port: 8900
```

{% hint style="warning" %}
Linux Only
{% endhint %}

### Mixed HTTP/SOCKS5 Inbound

```yaml
mixed-port: 8899
```

This port accepts both HTTP and SOCKS5 proxy inbound connections.

### **Authentication**

HTTP and SOCKS5(and Mixed Inbound) user authentication

```yaml
authentication:
  - user1:pass1
  - user2:pass2
```

### **Allow LAN**

Whether to allow LAN(non local) incoming inbound connections

```yaml
allow-lan: true
```

{% hint style="info" %}
When `allow-lan` is enabled, `bind-address` can't be `localhost` or `127.0.0.1`
{% endhint %}

### Bind Address

The listening address for the inbound listeners

```yaml
bind-address: '0.0.0.0'
```

### Mode

Traffic route mode, can be:

* `rule`: divert traffic by rules, **default**
* `global`: all traffic will go though a global selected proxy
* `direct`: all traffic will go to the internet directly without any proxy

```yaml
mode: 'rule'
```

***

## Logging Configuration

### Log Level

Controls the verbosity of log output:

```yaml
log-level: debug
```

Available options:
- `debug`: Most verbose, shows all log messages
- `info`: Normal operation logs (default)
- `warning`: Warning and error messages only
- `error`: Error messages only
- `off`: No logging

## External Controller

### API Interface

Enable external control via REST API:

```yaml
external-controller: 127.0.0.1:9090
```

### External UI Dashboard

Path to the web dashboard interface:

```yaml
external-ui: "public"
```

### Authentication Secret

Secure the external controller with a secret:

```yaml
secret: "clash-rs"
```

### CORS Configuration

Allow cross-origin requests from specific domains:

```yaml
cors-allow-origins:
  - "https://example.com"
  - "https://dashboard.example.com"
```

## Network Configuration

### IPv6 Support

Enable or disable IPv6 support:

```yaml
ipv6: false
```

### Outbound Interface

Specify the network interface for outbound connections:

```yaml
interface: "en0"
```

### Routing Mark

Set FW Mark on Linux for advanced routing:

```yaml
routing-mark: 0
```

## Database Configuration

### GeoIP Database

Configure IP geolocation database:

```yaml
mmdb: ./Country.mmdb
mmdb-download-url: "https://github.com/Loyalsoldier/geoip/releases/download/202307271745/Country.mmdb"
```

### ASN Database

Configure ASN (Autonomous System Number) database:

```yaml
asn-mmdb: ./ASN.mmdb
asn-mmdb-download-url: "https://github.com/Loyalsoldier/geoip/releases/download/202307271745/ASN.mmdb"
```

### Geosite Database

Configure domain-based geolocation database:

```yaml
geosite: ./geosite.db
geosite-download-url: "https://github.com/Loyalsoldier/v2ray-rules-dat/releases/download/202307271745/geosite.dat"
```

## Profile Settings

### Cache Configuration

Control what data is cached between restarts:

```yaml
profile:
  store-selected: true    # Remember selected proxy in groups
  store-fake-ip: false    # Cache fake-ip mappings
  tracing: true          # Enable tracing support
```

## Host Mappings

Override DNS resolution for specific domains:

```yaml
hosts:
  'mtalk.google.com': 108.177.125.188
  'dl.google.com': 180.163.151.161
  'example.com': 192.168.1.100
```

## Advanced Listeners

Configure additional listening interfaces:

```yaml
listeners:
  - name: "socks5-local"
    type: socks
    port: 1080
    listen: 127.0.0.1
    
  - name: "http-local"
    type: http
    port: 8080
    listen: 127.0.0.1
```

## Experimental Features

Enable experimental functionality:

```yaml
experimental:
  ignore-resolve-fail: true   # Continue if DNS resolution fails
  sniff-tls-sni: true        # Sniff TLS SNI for routing
  force-fake-ip: true        # Force fake-ip mode
```

## Provider Configuration

### Proxy Providers

Load proxies from external sources:

```yaml
proxy-providers:
  file-provider:
    type: file
    path: ./ss.yaml
    interval: 300
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 300
      timeout: 5000
      lazy: true
  
  http-provider:
    type: http
    url: "https://example.com/proxy.yaml"
    interval: 3600
    path: ./proxy.yaml
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 300
```

### Rule Providers

Load rules from external sources:

```yaml
rule-providers:
  file-provider:
    type: file
    path: ./rule-set.yaml
    interval: 300
    behavior: domain  # or ipcidr, classical
  
  http-provider:
    type: http
    url: "https://example.com/rules.yaml"
    interval: 3600
    path: ./rules.yaml
    behavior: domain
```

## Configuration Tips

{% hint style="info" %}
**Best Practices:**
- Always set `log-level` to `info` or `warning` in production
- Use `store-selected: true` to remember proxy selections
- Keep database files updated for accurate geolocation
- Use strong secrets for external controller access
{% endhint %}

{% hint style="warning" %}
**Security Notes:**
- Don't expose the external controller to public networks without proper authentication
- Use HTTPS for external UI access when possible
- Regularly update GeoIP and geosite databases
{% endhint %}
