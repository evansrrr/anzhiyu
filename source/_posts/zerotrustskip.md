---
author: Raziore
title: 全网首发：Zero Trust 最新跳过绑卡教程
date: 2026-07-02T03:50:00+08:00
description: 惊天大发现
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/124114873_p0.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/124114873_p0.webp
tags:
  - Net
  - Tech
  - Proxy
  - Cloudflare
categories: Proxy
---
不是 哥们，都2026年了，还能跳过绑卡，纯赛博大善人来的是吧？

自从 Cloudflare UI 上次改版，连同 Zero Trust 原来的跳过绑卡方式失效之后，过去了还不到一百来天，本来以为CF终于要转正规军了，没想到居然又找到了新的跳过绑卡方式。这个我绝对全网首发，网上不可能搜到的。

但是，这个方法未来可能会失效，建议有兴趣玩玩的趁早搞。下面进入正文：

**拥有一个Pages项目：** 如果已经有 Pages 项目，用已有的也行。没有的话可以随便创建一个，使用本地文件部署也可以，不需要能运行，只要 Cloudflare 给你部署就行。

**跳过绑卡：** 来到这个 Pages 项目的 **设置**，拉到最底下，找到 **预览访问**，点击 **限制预览**，然后 “限制预览” 就会变成 **管理**，点击它你就进到了正常的 Cloudflare One 界面内的子界面了（Cloudflare Zero Trust​ 是 Cloudflare One 平台的产品）

**设置登录邮箱：** 在这个界面左侧栏 **团队和资源** 里点击 **设备**，点击 **添加设备**，然后勾选 “已下载客户端”，下一步，在第二步这里设置允许登录的邮箱（默认是 Cloudflare 账户邮箱，之后可以再改），后面登录 Zero Trust 需要用这个邮箱接收验证码。然后3、4、5、6步保持默认设置直接一路继续，这样就设置好了。

在**概述**页可以看到团队名，默认应该是 “你的项目名-pages”。当然这个可以改。

这个大概率不是 Cloudflare 的本意，应该只是属于一个bug，可能随时被修掉，所以如果某一天这个方法失效了，那很正常。Warp 和 Zero Trust 各有优缺点，对一般人来说 Warp 实在是足够完美了，Zero Trust 跳过绑卡只是适用于那些想玩一下又懒得折腾绑卡的人。这里提个醒：如果你用了 Zero Trust，又想换回 Warp，不必卸载重装，试试 warp-cli help