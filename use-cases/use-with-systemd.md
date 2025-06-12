# 📰 Use with systemd

A sample systemd config can be like

```sh
[Unit]
Description=Clash
After=network.target

[Service]
Type=simple
User=%i
Restart=always
ExecStart=/usr/bin/clash -d /home/user/clash
AmbientCapabilities=CAP_NET_BIND_SERVICE CAP_NET_ADMIN
CapabilityBoundingSet=CAP_NET_BIND_SERVICE CAP_NET_ADMIN

[Install]
WantedBy=multi-user.target
```

