---
author: Raziore
title: ip6.arpa域名：200条dns不够？
date: 2026-04-22T00:03:00+08:00
description: 羊毛薅炸
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/143019582_p0.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/143019582_p0.webp
tags:
  - Net
  - Cloudflare
  - 域名
  - ipv6
---
### [TG 群](https://t.me/ernst_loosen_bot)

来点人啊。。。

### [Discord服务器](https://discord.gg/vNfM9ezTdE)

加discord群也行，新建的。。。

## 正文

前几天在做 proxyip 的时候忽然发现 Cloudflare 给的 200 条 dns 不够用了，于是想起来 ip6.arpa。免费版正常域名的子域名是不能再托管到 cf 的，但 ip6.arpa 域名可以。

`.arpa` 顶级域名的官方注册管理机构是IANA（互联网数字分配机构），它负责维护 `ip6.arpa` 的根部解析，将 ipv6 地址的大段分配给全球五个 RIR（区域互联网注册机构）管理。IAB（互联网架构委员会）负责确保该域名仅用于网络基础设施相关的技术功能。

那么怎么获得 ip6.arpa 域名？有两家著名的隧道服务商 [he.net](https://tunnelbroker.net) 和 [netassist.ua](https://tb.netassist.ua/index.php)，它们原本的宗旨是让只拥有 ipv4 环境的人通过它们提供的隧道获得的 ipv6 地址也能访问 ipv6 资源，但由于 ipv6 地址十分庞大，所以目前注册的账户可以分配到一个 /64 地址段（$2^{64}$）和一个 /48 地址段（$2^{80}$）。这对于个人几乎不可能用得完。还有个好消息是注册都比较宽松。关于如何注册和配置请自行 google 或 youtube。

当你向 he.net 申请了 ipv6 隧道，he.net 就会将该段地址的 dns 管理权开放给你。填上 Cloudflare 的 NS 地址后就能用 Cloudflare 来管理反向解析记录。所以具体的域名是什么？例如，我得到一个 `2001:470:d:6e9::/64` 地址段，接下来把前缀补全并倒置，得到 `2001:0470:000d:06e9` -> `9.e.6.0.d.0.0.0.0.7.4.0.1.0.0.2.ip6.arpa`，这就是你可以托管在 cloudflare 的主域名。

有了这个主域名后就能裂变出来巨量的子域名，每一个都能通过在其上级域名添加 ns 记录从而单独托管在 cloudflare，获得 200 条 dns 记录容量。但注意如果要托管其他服务需要申请一个 ssl 证书，可以通过 [IP6.ARPA域名自动添加SSL证书](https://ssl.eooce.xx.kg/) 这个工具快捷申请并安装。最后可以通过设置链式解析逻辑来实现成百上千条 dns 记录，例如 example.com 设置 cname 解析到 9.e.6.0.d.0.0.0.0.7.4.0.1.0.0.2.ip6.arpa，9.e.6.0.d.0.0.0.0.7.4.0.1.0.0.2.ip6.arpa 设置 150 条 dns 记录和 50 条 ns 记录，这 50 条 ns 记录指向其子域名，子域名可以重复相同的设置继续向下委托，这样 example.com 就拥有所有的这些 dns 记录。但要注意 dns 记录解析可能会有深度上限，这个未知。