---
author: Raziore
title: Cloudflare代理速成
date: 2026-03-31T16:56:00+08:00
description: 依旧玄学
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/Cloudflare代理速成.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/Cloudflare代理速成.webp
tags:
  - Net
  - Tech
  - Proxy
  - Cloudflare
categories: Proxy
---
>免责声明：《中华人民共和国计算机信息网络国际联网管理暂行规定》第六条规定：“计算机信息网络直接进行国际联网，必须使用邮电部国家公用电信网提供的国际出入口信道。任何组织和个人不得自行建立或者使用其他信道进行国际联网。”，第十四条：“违反本规定第六条、第八条和第十条的规定的，由公安机关责令停止联网，给予警告，可以并处15000元以下的罚款；有违法所得的，没收违法所得。”本文既不提倡“翻墙”行为，也不提供工具和源码，仅供讨论、学习网络技术，严禁用于任何形式的非法网络访问，因技术滥用导致的一切后果由使用者自行承担。另Cloudflare服务存在明确的[使用条款限制](https://www.cloudflare.com/zh-cn/terms/)，使用者应自行斟酌。

**阅读本文可能需要对Github、代理等有一小点了解**

经过几年的技术沉淀，Cloudflare代理已经变得相对成熟，甚至一定程度上比普通机场和自建代理表现更好。本文选取几个常见的项目逐一介绍，速成并不能面面俱到，原理及进阶使用方式另开一篇。每个项目的README也有详尽的介绍。另外由于Cloudflare网站界面经常更新变动，本文倾向使用文字描述而不提供图片指示，我使用英文版懒得改了，有什么翻译问题不要在意。

## 准备

如果你懒得做任何准备，请仅确保有一个[Cloudflare](https://dash.cloudflare.com/login)账户和Clash或v2rayN类的客户端。

对于更全面的配置，需要[Cloudflare](https://dash.cloudflare.com/login)账户、[GitHub](https://github.com/)账户、托管在Cloudflare的域名和客户端。

客户端推荐：

- [FlClash](https://github.com/chen08209/FlClash/releases/latest)：Windows、Android、macOS、Linux
- [Clash Verge](https://github.com/clash-verge-rev/clash-verge-rev/releases/latest)：Windows、macOS、Linux
- [v2rayN](https://github.com/2dust/v2rayN/releases/latest)：Windows、macOS、Linux
- [v2rayNG](https://github.com/2dust/v2rayNG/releases/latest)：Android
- [SingBox](https://github.com/GUI-for-Cores/GUI.for.SingBox/releases/latest)：Windows、Linux
- [Hiddify](https://github.com/hiddify/hiddify-app/releases/latest)：Windows、Android、[iOS](https://apps.apple.com/us/app/hiddify-proxy-vpn/id6596777532)、macOS、Linux

特别说明：不支持Wireguard客户端及L2TP类。

## 一、甬哥侃侃侃

>YouTube：[@ygkkk](https://www.youtube.com/@ygkkk)
>Github：[@yonggekkk](https://github.com/yonggekkk)

要用到的项目：[yonggekkk/Cloudflare-vless-trojan](https://github.com/yonggekkk/Cloudflare-vless-trojan)

登陆后进入Cloudflare的仪表板（Dashboard），在左侧找到**计算 -> Workers & Pages -> 创建应用**。本文介绍的所有项目均支持Workers和Pages两种部署方式。以Pages部署方式为例，此方式适用于没有托管在Cloudflare域名，以下几个项目同理，不再一一叙述。在页面下方找到`Looking to deploy Pages? Get started`这行小字，点开链接点开[Vless_workers_pages](https://github.com/yonggekkk/Cloudflare-vless-trojan/tree/main/Vless_workers_pages)文件夹，点击[worker混淆.js](https://github.com/yonggekkk/Cloudflare-vless-trojan/blob/main/Vless_workers_pages/_worker%E6%B7%B7%E6%B7%86.js)，