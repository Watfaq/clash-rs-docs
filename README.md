# 👋 Welcome to ClashRS User Manual

{% hint style="info" %}
This software is still under it's prerelease. Please report any missing/broken features if you'd like to :thumbsup:
{% endhint %}

{% hint style="info" %}
**Disclaimer:**
- This manual contains AI generated content and may not be 100% accurate.
{% endhint %}


## Overview

ClashRS is a network proxy software that you can use to control how you want to divert your internet traffic through different proxy servers with flexible rules and multiple different supported outbound protocols.

### [✨ Features](https://github.com/Watfaq/clash-rs#-features) <a href="#user-content--features" id="user-content--features"></a>

* 🌈 Flexible traffic routing rules based off source/destination IP/Domain/GeoIP/GeoSite/Process etc.
* 📦 Local anti-spoofing DNS with support of UDP/TCP/DoH/DoT remote, exposed as a local UDP/TCP/DoH/DoT server.
* 🛡 Multiple inbound modes: HTTP, SOCKS5, Mixed, Shadowsocks, Redir, TProxy, and TUN (utun) for transparent proxying.
* ⚙️ AnyTLS/Hysteria2/ShadowQuic/Shadowsocks/Socks5/SSH/Tailscale/Tor/Trojan/Tuic/VLess/VMess/WireGuard outbound support with transports including gRPC, TLS, H2, and WebSocket.
* 🌍 Dynamic remote rule/proxy loader.
* 🎵 Tracing with Jaeger (build with `--features telemetry`)



## &#x20;Links

* Repository - [https://github.com/Watfaq/clash-rs](https://github.com/Watfaq/clash-rs)
* Download - [https://github.com/Watfaq/clash-rs/releases](https://github.com/Watfaq/clash-rs/releases)

## Get Started

We've put together some helpful guides for you to get setup with our product quickly and easily.

{% content-ref url="using-it/getting-set-up.md" %}
[getting-set-up.md](using-it/getting-set-up.md)
{% endcontent-ref %}

{% content-ref url="using-it/configuration/" %}
[configuration](using-it/configuration/)
{% endcontent-ref %}

{% content-ref url="using-it/config-references.md" %}
[config-references.md](using-it/config-references.md)
{% endcontent-ref %}
