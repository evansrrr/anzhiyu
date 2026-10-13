---
author: Raziore
title: 教育网通用IPv6免流客户端
date: 2026-10-13T13:36:00+08:00
description: 后来者总有机会
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/4B4CD090214E55622913B893B68AFE69.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/4B4CD090214E55622913B893B68AFE69.webp
tags:
  - Net
  - Tech
  - 校园网
  - Soft
  - CERNET2
categories:
  - Project
---
这篇博文偏向技术性的解决方案对比，使用说明和软件下载请移步[官网](https://freev6.cc.cd)

---
## What's New

主要的解决方案和 BKNetwork 还是差别很大的。简单来说，FreeV6 摆脱了 Warp 官方工具链，转而使用更可控、稳定的 mihomo 核心，并独立实现设备注册。总体架构上延续了 Go 后端 + Tauri 前端的设计，界面设计参考 FlClash（本来也差不多）并使用 material-web 统一风格。

---
## Note

感谢 [usque](https://github.com/Diniboy1123/usque) 和 [warp-masque-actions](https://github.com/byJoey/warp-masque-actions) 提供的思路，这让解决独立注册 Warp 设备成为可能。在一开始我就排除了附带 usque 的方案，这会让安装包和占用空间直接起飞，因此我尝试使用 Cloudflare Worker 或 Github Action 执行注册并下发凭据，但显然这样做不能保证稳定性和不被滥用。就在做到 Github Action Python 注册设备的时候，我突然意识到既然 usque 是开源的而且能用 Go 实现，为什么不直接让客户端去注册。最后两百行左右的 Go 就解决了。

mihomo 核心版本目前固定在正式版 v1.19.31，这个版本实测是支持 masque 协议的，不必使用 alpha。默认路由模式为 tun + rule，确保接管系统流量，而且可以实现黑白名单。白名单自然可以排除内网资源，黑名单则默认阻断了一些本不应该通过免流访问的域名，当然通过打开开发者模式可以取消。开发者模式会一并跳过 IP 检测（ https://api-ipv6.ip.sb/ip ）。

规则集使用了 Github 上公认质量较高的 cmliu、ACL4SSR 等维护的。没有必要做过多的分流，接入点只是入口泛播 ip 的变更，分流实际上没有任何意义，只需保留节点选择和 DIRECT 分组。接入点只保留 IPv6 地址，共 4 个：

- 2606:4700:103::1
- 2606:4700:103::2
- 2606:4700:104::1
- 2606:4700:104::2

端口共 7 个：

- 443
- 500
- 1701
- 4500
- 4443
- 8443
- 8095

合计共 28 个接入点。IPv6 DNS 使用阿里和 Cloudflare，支持 DoH。mihomo 共占用 3 个端口：

- 7890, TCP, mixed proxy
- 9090, TCP, controller
- 1053, TCP/UDP, DNS

对于软件内大部分目标 Github 的请求，都使用了 gh-proxy.com 代理加速，包括更新、mihomo、GeoData 等。

热点共享免流使用 ICS 拓扑切换，但直接开会导致出现问题，见 [clash-verge-rev #7739](https://github.com/clash-verge-rev/clash-verge-rev/issues/7739) 与 [mihomo #3186](https://github.com/MetaCubeX/mihomo/issues/3186)。另外针对 Win 11 新版 WDI 驱动，解决方案参考 [clash-verge-rev Discussion #7194](https://github.com/clash-verge-rev/clash-verge-rev/discussions/7194)。

Github Action CI 脚本在每一次 push 后执行，Release 则在 tag push 后执行，若版本号一致则触发编译，创建 Attestation 和 Draft Release，手动确认发布。Deploy Docs 在正式版本发布 Release 后触发 Vercel Hook，使托管在 Vercel 的文档站构建更新。为了解决 Github API 配额问题，检测更新采用随 Release 附 version.json 的方法，这样就可以直接通过代理站请求 latest。

这次加上了用户政策和隐私协议，到底还是比较灰色地带。但是这个分离核心和注册设备的方案确实很大程度降低了风险，最后依然选择了 MIT，反正思路就是那么个思路，我只是比较早吃螃蟹的人，也管不了那么多。不过读这个真是头疼。

未来除了已在计划中的几个小更新和修复项，还在考虑对于 macOS 和 iOS 的支持。本来像我这种穷学生，实在没有闲钱买苹果，身边也没有朋友用，暂时就没打算做。但是至少理论上是可以的。安卓就歇菜吧，我实在没有查到谷歌要支持 DHCPv6 的消息，而校园网由于要方便认证一般只使用 DHCPv6 而不支持 SLAAC，所以安卓还是洗洗睡吧。