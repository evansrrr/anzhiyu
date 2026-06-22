---
author: Raziore
title: 校园网一键免流软件
date: 2026-05-11T15:22:00+08:00
description: 早就想做了
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/4B4CD090214E55622913B893B68AFE69.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/4B4CD090214E55622913B893B68AFE69.webp
tags:
  - Net
  - Tech
  - 校园网
  - Soft
categories:
  - Project
---
前阵子借着比赛时机顺便做了这个小软件，已经全部开源在Github了，您看着要不给个免费的star...？

由于仓库Readme写的比较详细，博客里就不介绍了。务必仔细读读readme的**使用说明**和**常见问题**。现在Gitee也有镜像版本，方便下载

[Github：evansrrr/BKNetwork](https://github.com/evansrrr/BKNetwork)

[Gitee：evansrrr/BKNetwork](https://gitee.com/evansrrr/BKNetwork)

这里有几个重点：

- [TG Group](https://t.me/ernst_loosen_bot) [Discord Group](https://discord.gg/vNfM9ezTdE) 想加可以加
- **当前版本（v0.9.7）重大bug：由软件唤起浏览器的操作可能导致cookie全部被清除，强烈建议开启静默启动，并手动在浏览器访问控制台（`http://localhost:13335`）**，该问题将于下个版本修复，在此之前先让我平安度过期末周
- 建议在关机前关闭免流模式，否则下次开机后可能因为校园网认证失败无法上网，还得关上免流再登录认证
- 免流前提是连接`USTB-Student` 或 `USTB-V6`，已登录校园网账号，并且电脑可以正常获取 ipv6 地址（[testipv6](https://www.testipv6.cn/)）
- Warp 免流偶尔连不上，正常现象稍后再试
- 免流模式不能访问校园网内网（例如校园网登录页和本研一体）

免流一定是功能大于体验，有无法使用的软件很正常。

一般来说 Warp 免流是兼容性最好的，如果一个软件在 Warp 连接下不能用，可以尝试寻找这个软件设置中有没有**使用代理**的选项，搭配代理软件和免流节点使用，例如网易云就可以这样解决。关于代理免流和节点，参见之前几篇博客。之后的版本也将考虑加入免流节点。

anyway，虽然开发到现在v0.9.7版本只花了一周时间，难免仓促，我依然照着让自己可以日常使用的目标在设计，很高兴现在我每天也在用它。bug reports and advice are welcomed.