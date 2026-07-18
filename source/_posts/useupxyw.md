---
author: Raziore
title: 榨干校园网の小实验
date: 2026-07-19T00:35:00+08:00
description: 莫怪莫怪
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/143764563_p0.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/143764563_p0.webp
tags:
  - Net
  - ipv6
  - 校园网
categories: Project
---
在过去的18天里，我用掉了13T的校园网流量。这是一个非常保守的数字，校园网v6计量方式是有问题的，实际上消耗了大约20T。

![Screenshot 2026-07-19 012226.webp](https://img.ich.cc.cd/file/ichblog/post_img/useupxyw/Screenshot_2026-07-19_012226.webp)
![Screenshot 2026-07-19 012245.webp](https://img.ich.cc.cd/file/ichblog/post_img/useupxyw/Screenshot_2026-07-19_012245.webp)
![Screenshot 2026-07-19 012259.webp](https://img.ich.cc.cd/file/ichblog/post_img/useupxyw/Screenshot_2026-07-19_012259.webp)

这样做的目的，完全是因为我想知道校园网v6流量有没有限额，或超额限速。之前听说一个月用6T会限速，所以我就做了这个小实验。目前为止，我没有遇到限额或限速。借此机会，我也得以充分测试了校园网v6的表现。由粗略的统计分析得出：

- 校园网单账户限速
- 会影响其他连接者延迟
- 凌晨网速较快
- 白天或晚高峰会出现波动
- 1:30左右登录状态可能失效

首先，单账户限速大概是500Mbps，我测得的峰值是473Mbps，与连接速度573Mbps相比差距明显。同账户多台设备测试，明显限速，总带宽200Mbps左右。尤其是深夜到凌晨，网络状况较好时，平均速度可达300Mbps以上；有波动时220Mbps左右；这18天测试平均245Mbps。

校园网v6计量方式存在一些莫名其妙的问题，由于v6流量不是实时更新，我也不太方便进一步排查，也可能与多设备连接有关。不过确实有一部分消耗的流量没有计入系统。

从另一个角度来说，这验证了warp免流的可行性。所以这个免流方式大可放心。