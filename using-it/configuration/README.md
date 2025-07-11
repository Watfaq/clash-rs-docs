# 🔨 Configuration

Full sample configuration reference

````yaml
---
port: 8888
socks-port: 8889
mixed-port: 8899

tun:
  enable: false
  device-id: "dev://utun1989"

dns:
  enable: true
  listen: 127.0.0.1:53553
  #   udp: 127.0.0.1:53553
  #   tcp: 127.0.0.1:53553
  #   dot: 127.0.0.1:53554
  #   doh: 127.0.0.1:53555

  # ipv6: false # when the false, response to AAAA questions will be empty

  # These nameservers are used to resolve the DNS nameserver hostnames below.
  # Specify IP addresses only
  default-nameserver:
    - 114.114.114.114
    - 8.8.8.8
  enhanced-mode: fake-ip 
  fake-ip-range: 198.18.0.2/16 # Fake IP addresses pool CIDR
  # use-hosts: true # lookup hosts and return IP record
  
  # Hostnames in this list will not be resolved with fake IPs
  # i.e. questions to these domain names will always be answered with their
  # real IP addresses
  # fake-ip-filter:
  #   - '*.lan'
  #   - localhost.ptlogin2.qq.com
  
  # Supports UDP, TCP, DoT, DoH. You can specify the port to connect to.
  # All DNS questions are sent directly to the nameserver, without proxies
  # involved. Clash answers the DNS question with the first result gathered.
  nameserver:
    - 114.114.114.114 # default value
    - 1.1.1.1 # default value
    - tls://1.1.1.1:853 # DNS over TLS
    - https://1.1.1.1/dns-query # DNS over HTTPS
#    - dhcp://en0 # dns from dhcp

allow-lan: true
mode: rule
log-level: debug
external-controller: 127.0.0.1:9090
external-ui: "public"
# secret: "clash-rs"
experimental:
  ignore-resolve-fail: true

profile:
  store-selected: true
  store-fake-ip: false


proxy-groups:
  - name: "relay"
    type: relay
    proxies:
      - "plain-vmess"
      - "ws-vmess"
      - "auto"
      - "fallback-auto"
      - "load-balance"
      - "select"
      - DIRECT
  
  - name: "relay-one"
    type: relay
    use:
      - "file-provider"

  - name: "auto"
    type: url-test
    use:
      - "file-provider"
    proxies:
      - DIRECT
    url: "http://www.gstatic.com/generate_204"
    interval: 300

  - name: "fallback-auto"
    type: fallback
    use:
      - "file-provider"
    proxies:
      - DIRECT
    url: "http://www.gstatic.com/generate_204"
    interval: 300

  - name: "load-balance"
    type: load-balance
    use:
      - "file-provider"
    proxies:
      - DIRECT
    strategy: round-robin
    url: "http://www.gstatic.com/generate_204"
    interval: 300

  - name: select
    type: select
    use:
      - "file-provider"

  - name: test 🌏
    type: select
    use:
      - "file-provider"
    proxies:
      - DIRECT

proxies:
  - name: plain-vmess
    type: vmess
    server: 10.0.0.13
    port: 16823
    uuid: b831381d-6324-4d53-ad4f-8cda48b30811
    alterId: 0
    cipher: auto
    udp: true
    skip-cert-verify: true
  - name: ws-vmess
    type: vmess
    server: 10.0.0.13
    port: 16824
    uuid: b831381d-6324-4d53-ad4f-8cda48b30811
    alterId: 0
    cipher: auto
    udp: true
    skip-cert-verify: true
    network: ws
    ws-opts:
      path: /api/v3/download.getFile
      headers:
        Host: www.amazon.com

  - name: tls-vmess
    type: vmess
    server: 10.0.0.13
    port: 8443
    uuid: 23ad6b10-8d1a-40f7-8ad0-e3e35cd38297
    alterId: 0
    cipher: auto
    udp: true
    skip-cert-verify: true
    tls: true

  - name: h2-vmess
    type: vmess
    server: 10.0.0.13
    port: 8444
    uuid: b831381d-6324-4d53-ad4f-8cda48b30811
    alterId: 0
    cipher: auto
    udp: true
    skip-cert-verify: true
    tls: true
    network: h2
    h2-opts:
      path: /ray

  - name: vmess-altid
    type: vmess
    server: tw-1.ac.laowanxiang.com
    port: 153
    uuid: 46dd0dd3-2cc0-3f55-907c-d94e54877687
    alterId: 64
    cipher: auto
    udp: true
    network: ws
    ws-opts:
      path: /api/v3/download.getFile
      headers:
        Host: 5607b9d187e655736f563fee87d7283994721.laowanxiang.com
  - name: "ss-simple"
    type: ss
    server: 10.0.0.13
    port: 8388
    cipher: aes-256-gcm
    password: "password"
    udp: true
  - name: "trojan"
    type: trojan
    server: 10.0.0.13
    port: 9443
    password: password1
    udp: true
    # sni: example.com # aka server name
    alpn:
      - h2
      - http/1.1
    skip-cert-verify: true

proxy-providers:
  file-provider:
    type: file
    path: ./ss.yaml
    interval: 300
    health-check:
      enable: true
      url: http://www.gstatic.com/generate_204
      interval: 300

rule-providers:
  file-provider:
    type: file
    path: ./rule-set.yaml
    interval: 300
    behavior: domain

rules:
  - DOMAIN,ipinfo.io,relay
  - RULE-SET,file-provider,trojan
  - GEOIP,CN,relay
  - DOMAIN-SUFFIX,facebook.com,REJECT
  - DOMAIN-KEYWORD,google,select
  - DOMAIN,google.com,select
  - SRC-IP-CIDR,192.168.1.1/24,DIRECT
  - GEOIP,CN,DIRECT
  - DST-PORT,53,trojan
  - SRC-PORT,7777,DIRECT
  - MATCH, DIRECT
...

```
````
