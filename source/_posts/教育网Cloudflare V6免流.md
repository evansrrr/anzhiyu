---
author: Raziore
title: 教育网Cloudflare V6免流
date: 2026-03-30T23:22:00+08:00
description: 给赛博菩萨上柱香
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/教育网Cloudflare_V6免流.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/教育网Cloudflare_V6免流.webp
tags:
  - Net
  - Tech
  - ipv6
  - 校园网
  - CERNET2
  - Proxy
  - Cloudflare
categories:
  - Proxy
---
---

之前原生V6免流的方法有一定局限，延迟和速度不太理想。且极少服务器可能屏蔽来自DNS64的ip的请求。用CF的这个方法比较繁琐，并且涉及到代理的一些成分，但速度、延迟和兼容性确实更好。另外玩游戏还是老老实实用流量。

>免责声明：《中华人民共和国计算机信息网络国际联网管理暂行规定》第六条规定：“计算机信息网络直接进行国际联网，必须使用邮电部国家公用电信网提供的国际出入口信道。任何组织和个人不得自行建立或者使用其他信道进行国际联网。”，第十四条：“违反本规定第六条、第八条和第十条的规定的，由公安机关责令停止联网，给予警告，可以并处15000元以下的罚款；有违法所得的，没收违法所得。”根据法律规定，请勿向他人售卖、传播vpn，否则可能构成违法行为，受到行政处罚并记录。本文既不提倡“翻墙”行为，也不提供工具和源码，仅供讨论、学习网络技术，严禁用于任何形式的非法网络访问，因技术滥用导致的一切后果由使用者自行承担。另Cloudflare服务存在明确的[使用条款限制](https://www.cloudflare.com/zh-cn/terms/)，使用者应自行斟酌。

## 一、WARP/Zero Trust

Cloudflare推出的“转发加密流量和DNS”的软件，说白了就是vpn，对个人用户免费。19年刚出来的时候用的人很多，但那时候只支持wireguard协议，比较容易识别，后来被GFW封锁了四五年，这两年又开始能用了，而且多了个masque协议可选，这个隐蔽性比较高。zero trust本来是给企业设计的，个人用户有50个免费席位（就是能同时登录50个设备），因为使用企业线路，ip纯净度比较高（尤其是ipv6），更容易被服务商接受。国内网络情况复杂，根据你的情况选择表现好的方案。zero trust国内落地ip基本都是天津、杭州、香港、北京等，warp基本落地香港。一般来说推荐用zero trust，比较稳一点，落地国内ip无所谓，正好速度比较快。**其实非常快，Wegame和Steam也能下载游戏。并且有时可以访问谷歌系网站，包括gemini和youtube等**（gemini有时候打不开，youtube和谷歌都能打开）；如果偏科学上网需求可以看第二种方案。

手机用不了，手机用不了，手机用不了。

**Warp**：[Windows官方最新版下载](https://downloads.cloudflareclient.com/v1/download/windows/ga)，安装后第一次打开选择 Warp 模式，连接模式选择 Traffic and DNS (HTTPS) 或 Traffic and DNS (UDP)，中文翻译可能不太一样。

**Zero Trust**：你只需要有一个[Cloudflare账户](https://dash.cloudflare.com/login)，登录以后在左侧找到**Zero Trust**，注册一个独立的团队名，选择**Free Plan**，~~到添加付款信息这里直接点右上角`取消并退出`，确定退出；再点Zero Trust就成功跳过绑卡步骤了~~ 现在不能跳过绑卡了，需要真实的、有余额的外币信用卡，会进行一笔预支付验证（好像是1刀），稍后会退回。在左边栏找到**团队和资源 -> 设备 -> 添加设备**，下载并安装好软件，点继续，添加允许登录的邮箱，默认包括你Cloudflare的注册邮箱，也可以添加别的。服务模式、路由、拆分隧道保持默认即可。接下来在Warp客户端设置，账户里点`通过Cloudflare One登录`，然后写你的团队名，验证邮箱后就能用了。如果是安装后第一次打开，可以直接选择 Zero Trust 模式。但 Zero Trust 有时候连不上。注意如果验证 Zero Trust 团队后想切换 Warp 模式，需要卸载客户端重新安装并在第一次打开时选择 Warp 模式。

当然免流肯定要用ipv6连接，虽说warp客户端会优先用v6，但是保不齐他会抽风。按照原生免流里介绍的方法在控制面板禁用wifi网卡的v4协议即可，cloudflare会自动配置v6 dns。

**补坑！！**[Cloudflare代理进阶](https://ich.cc.cd/2026/04/12/Cloudflare%E4%BB%A3%E7%90%86%E8%BF%9B%E9%98%B6/)

---
## 二、套用Cloudflare代理

推荐阅读：[Cloudflare代理速成](https://ich.cc.cd/2026/03/31/Cloudflare%E4%BB%A3%E7%90%86%E9%80%9F%E6%88%90/)

众所周知教育网v6传统玩法是使用v6 vps搭vpn节点。诶那么我们的赛博大善人Cloudflare恰好就有全球Anycast的边缘计算服务，并且还是双栈的。以下从几个方法展示校园网套用Cloudflare代理实现v6免流的效果。没有一个方法是兼容所有优点的，这些方案优点就是免费。当然如何看待优点与否取决于你满意程度。CF代理博大精深，原理和其他玩法放在Cloudflare代理进阶讲。

注意针对v6客户端需要特别配置，v2ray在设置里 “TUN模式设置” 关上 “自动路由” 并打开 “启用ipv6地址”，否则使用虚拟网卡模式无法连接v6节点，目前最新版本v2ray需要在参数设置 tun设置 启用旧版tun保护；在控制面板网卡填上公共v6 dns，否则一些支持v6的域名无法解析成ip地址。flclash在基本配置打开ipv6，进阶配置开启覆写DNS，关闭状态（使用系统dns），开启ipv6，但无法使用tun。其他软件同理，为了防止订阅没有自带v6 dns，所以需要手动在系统设置v6 dns，并且强制代理软件不使用订阅配置dns而去使用系统dns。推荐使用全局模式。订阅类客户端（比如FlClash）如使用全局模式需要在代理一栏中GLOBAL一栏切换有效节点（比如自动选择）！

#### 一键筛选

你可能已经注意到[Joey](https://ich.cc.cd/2026/03/31/cloudflare%E4%BB%A3%E7%90%86%E9%80%9F%E6%88%90/#%E4%BA%8C%E3%80%81Joey-blog)的后台有ipv4/ipv6的选项，这就是相对来说最简便的切换方法了。只保留ipv6然后重新导入订阅，把网卡v4禁用，测一下延迟，正常应该40ms左右，有-1是正常的。启用优选域名的其中一些是支持ipv6的，有时候启用优选ip会抽风。禁用v4就是为了防止在用支持v6的域名节点的时候因为解析出v4地址而走的v4。

⚠️ **steam下载**需启用网卡v4，选v6 ip地址节点，否则域名节点可能解析成v4节点。

#### 指定地址

如果不使用Joey的源码，可以通过手动指定ipv6地址来进行连接。这里以v2ray为例，方便编辑单节点设置。修改节点的地址和端口，改成cloudflare网络上支持的ipv6地址即可。这一步其实也算优选，原理和获取地址的方法在Cloudflare代理进阶，并且有维护自建优选api。对于懒人和新手，可以从以下示例中随便选一个填入节点编辑的地址中，端口使用443即可。

>time.is
>ip.sb
>wto.org
>www.whatismyip.com
>icook.hk
>steamdb.info
>russia.com
>japan.com
>singapore.com
>www.digitalocean.com
>2606:4700::

我这里也有自己维护的一些：[CF代理 API | Raziore Blog](https://ich.cc.cd/2026/04/13/CF%E4%BB%A3%E7%90%86%20API/)

最后，很多人没有意识到，如果使用纯 ipv6 优选域名或 ipv6 地址作节点的话，是不需要禁用网卡 v4 的，但是一定要启用全局模式。连接指定的 ipv6 地址和仅能解析出 ipv6 的域名后，这就意味着连接入口已经确定了，没有 ipv4 的事。只需要确保所有流量都走了节点即可。

ok，懒人cf校园网v6免流到这里差不多就结束了（可以说是懒人吧？），套cf代理这一块可能比较草率了，我也没想好该怎么区分新手和高手版本合适，真的写新手教程才是最难的，得时刻斟酌哪些要解释，哪些要跳过，希望小白最后都能成为大佬吧。