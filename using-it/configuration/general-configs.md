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

## WIP BELOW

* **log\_level**: Log level, options are `debug`, `info`, `warning`, `error`, or `off`.
* **dns**: DNS client/server settings.
* **profile**: Profile settings.
* **proxy**: Proxy settings.
* **proxy\_group**: Proxy group settings.
* **rule**: Rule settings.
* **hosts**: Host mappings.
* **mmdb**: Country database path.
* **mmdb\_download\_url**: URL to download the country database.
* **asn\_mmdb**: Optional ASN database path.
* **asn\_mmdb\_download\_url**: URL to download the ASN database.
* **geosite**: Geosite database path.
* **geosite\_download\_url**: URL to download the geosite database.
* **ipv6**: Whether IPv6 is supported.
* **external\_controller**: External controller address.
* **external\_ui**: Path to external UI dashboard.
* **secret**: External controller secret.
* **cors\_allow\_origins**: List of CORS allowed origins.
* **interface**: Outbound interface name.
* **routing\_mask**: FW Mark on Linux.
* **proxy\_provider**: Proxy provider settings.
* **rule\_provider**: Rule provider settings.
* **experimental**: Experimental settings.
* **tun**: TUN configuration settings.
* **listeners**: List of additional listeners.
