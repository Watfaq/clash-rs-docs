---
description: Quick Start with ClashRS
---

# ⌨️ Getting set up

<details>

<summary>Step1: Download</summary>

Grab the release from [https://github.com/Watfaq/clash-rs/releases](https://github.com/Watfaq/clash-rs/releases)

</details>

<details>

<summary>Step 2: Make a config</summary>

Prepare a runtime working directory

```
-> % tree .
.
└── config.yaml
```

And make a simple config

```
-> % cat config.yaml
port: 7890
```

</details>

<details>

<summary>Step 3: Run it</summary>

```shell
./clash
```

</details>

<details>

<summary>Step 4: Check out the output</summary>

```
  2023-09-21T10:05:45.570668Z  INFO clash_lib::common::mmdb: downloading mmdb from https://github.com/Loyalsoldier/geoip/releases/download/202307271745/Country.mmdb
    at clash_lib/src/common/mmdb.rs:33

  2023-09-21T10:05:46.783362Z ERROR clash_lib::app::profile: failed to read cache file: No such file or directory (os error 2), initializing a new one
    at clash_lib/src/app/profile/mod.rs:116

  2023-09-21T10:05:46.785423Z  INFO clash_lib::app::inbound::network_listener: HTTP TCP listening at: 0.0.0.0:7890
    at clash_lib/src/app/inbound/network_listener.rs:118

```

</details>

<details>

<summary>Step 5: Use it</summary>

```bash
-> % curl  -x http://127.0.0.1:7890 https://httpbin.yba.dev/ip
{
  "origin": "YOUR IP"
}
```

</details>
