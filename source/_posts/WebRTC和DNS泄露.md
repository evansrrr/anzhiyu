---
author: Raziore
title: WebRTC和DNS泄露
date: 2026-06-28T01:02:00+08:00
description: 说大不大，说小不小
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/146384614_p0.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/146384614_p0.webp
tags:
  - Net
  - Tech
  - Proxy
  - Privacy
categories:
  - Proxy
---
两种比较常见的真实ip泄露方式，和解决方法。好比你包饺子，不蘸一下水它有可能有一两个破皮，但蘸一下更不容易破。

WebRTC 是浏览器提供的实时音视频技术（Web Real-Time Communication），进行一些 P2P 通信可能使用。在建立 P2P 连接之前，浏览器会通过 STUN/TURN 服务器获取候选连接地址（ICE candidates），其中可能包含真实的网卡ip地址，造成泄露。Chrome 系浏览器可以通过安装插件解决，Firefox 需要改设置。注意如果使用[谷歌推出的插件](https://chromewebstore.google.com/detail/webrtc-network-limiter/npeicpdbkakmehahjeeohfdhnlpdklia)，必须在插件管理进入该插件详情页，点击插件选项，选择 **Use my proxy server (if present)**。这个还是推荐设置一下的。

DNS 泄露一般由于路由规则设置不当导致，泄露后运营商和网络管理员可以看到请求访问的域名。使用全局模式一般可以解决。

一些检测网站：

- [ippure.com webrtc](https://ippure.com/Browser-WebRTC-Leak-Detect.html)
- [ippure.com dns](https://ippure.com/DNS-Leak-Detect.html)
- [browserleaks.com/webrtc](https://browserleaks.com/webrtc)
- [browserleaks.com/dns](https://browserleaks.com/dns)
- [ipleak.net](https://ipleak.net/)
- [dnsleaktest.com](https://dnsleaktest.com/)
- [dnsleaktest.com/webrtc](https://dnsleaktest.com/webrtc.html)
- [whatismyip.io/webrtc-leak-test](https://whatismyip.io/webrtc-leak-test)
- [whatismyip.io/dns-leak-test](https://whatismyip.io/dns-leak-test)

如果用 Warp 的话可能显示泄露，但实际不会。其实像这种安全隐私性问题会越抠越细，我觉得不必太过纠结，关键的部分，通信内容加密是最重要的，一方面还没到这一步，另一方面做正经事无关痛痒。