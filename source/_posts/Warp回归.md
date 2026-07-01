---
author: Raziore
title: Warp回归
date: 2026-03-21T01:12:00+08:00
description: 依旧玄学
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/Warp回归.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/Warp回归.webp
tags:
  - Net
  - Tech
  - Proxy
  - Cloudflare
categories: Proxy
---
从19年cloudflare推出warp到现在，中国大陆的可用性一直在反复横跳。本质上warp是wireguard隧道，流量特征容易识别，所以21年到24年，warp被全面阻断，几乎不可用。从25年开始，得益于新的maskque协议，warp开始渐渐复活。到了26年，新协议的方案被进一步证明了稳定性，现在pc端速度和延迟都比较优秀。

不管使用warp还是zero trust，都需要手动切换协议。使用warp需要把这一段

```xml
<dict>
    <key>warp_tunnel_protocol</key>
    <string>masque</string>
</dict>
```

保存为 `mdm.xml` 文件，放到 `C:\ProgramData\Cloudflare` 下，重启warp即可。使用zero trust需要在设备管理那里切换。提一句，第一次开通zero trust服务到填写付款信息那里点取消，回到主页就可以绕过付款方式那一步。

为啥没写手机，因为我自己也没连上😅 （依旧玄学）

如果一次没连上，还是得多试几次。目前ip会落到香港，一些ai是用不了的（比如chatgpt），只能换其他节点使用。