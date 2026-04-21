---
author: Raziore
title: Cloudflare代理进阶
date: 2026-04-12T13:58:00+08:00
description: 所有焚诀都在这了
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/110618471_p1.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/110618471_p1.webp
tags:
  - Net
  - Tech
  - Proxy
  - Cloudflare
categories: Proxy
---
>免责声明：《中华人民共和国计算机信息网络国际联网管理暂行规定》第六条规定：“计算机信息网络直接进行国际联网，必须使用邮电部国家公用电信网提供的国际出入口信道。任何组织和个人不得自行建立或者使用其他信道进行国际联网。”，第十四条：“违反本规定第六条、第八条和第十条的规定的，由公安机关责令停止联网，给予警告，可以并处15000元以下的罚款；有违法所得的，没收违法所得。”根据法律规定，请勿向他人售卖、传播vpn，否则可能构成违法行为，受到行政处罚并记录。本文既不提倡“翻墙”行为，也不提供工具和源码，仅供讨论、学习网络技术，严禁用于任何形式的非法网络访问，因技术滥用导致的一切后果由使用者自行承担。另Cloudflare服务存在明确的[使用条款限制](https://www.cloudflare.com/zh-cn/terms/)，使用者应自行斟酌。

*这次真的是最后一篇干货了* ，如果你不知道我在说什么没关系，**欢迎来tg群玩：[@ernst_loosen_bot](https://t.me/ernst_loosen_bot)**

[Discord服务器](https://discord.gg/vNfM9ezTdE) 加discord群

---
## Zero Trust

先补一下之前的坑。首先肯定是尽量用 Masque 协议，Wireguard 流量特征非常明显，一看就知道你在用代理，Masque 是标准的 443 端口，https 流量和平常浏览网页一样，较难识别。但不用担心，使用这两个协议其他人完全看不到你的流量内容，只是如果用Wireguard容易让别人知道你在用代理。

Mac 和 iOS 的情况我不知道，Windows 禁用 v4 后有时连不上 zero trust 是因为 Cloudflare 的 v6 dns 没连上，这个时候在控制面版设置其他 dns 是没用的，Zero Trust 会强制使用 Warp 网关，我们只能去官网修改设置，在 **Zero Trust 团队和资源 设备 设备配置文件**，可以新建一个单独的配置指定生效的用户，也可以直接修改默认配置。**Maskque 协议**也是在这里改，往下拉一点找到服务模式，改成**不带 DNS 筛选的安全 Web 网关**，英文模式下叫 **Traffic only mode**，不知道为啥中文翻译成这么个东西。最后一定拉到页面最下方保存。改完了以后**再配置系统的 v6 dns** 就能连上了，填个阿里的就行（2400:3200::1 2400:3200:baba::1）。

安卓情况不太乐观，Zero Trust 基本用不了，并且安卓设备不能使用 **Traffic only mode**，而且没有 root 的机子没办法单独设置系统层面的 v6 dns。但是没啥关系，大部分免流的场景还是以电脑使用为主。

---
## Cloudflare 代理原理

在 Cloudflare Workers/Pages 部署的项目会同步部署到 Cloudflare 位于全球的机房中，提供边缘计算服务，当用户访问时会自动从附近响应最迅速的节点提供服务。一个账户有每天 10w 请求的免费额度，CPU 时间限制 10ms，对绝大部分人来说完全用不完，并且有很多人超了也可以正常用。所以我们在 Workers 或者 Pages 上运行一个脚本，我们的设备把访问请求发给 Worker，由它代替我们对目标服务器发起访问，然后把服务器响应的内容返回给我们，原理和正向代理完全一样。在空闲时间 Worker 不会消耗请求额度和 CPU 时间，而一旦用户发起连接，Worker 会立即响应，延迟几乎可以忽略不计；只有当真正处理流量时才会占用 CPU 时间。但和使用 vps 搭建代理不同的是，vps 必须有公网 ip 地址才能实现代理服务，而 Cloudflare 由于全球任播（Anycast）的特性，Workers 和 Pages 是没有公网 ip 地址的，你可以理解为 Cloudflare 内网有一堆服务器都部署有你的项目可以运行，它们没有公网 ip，如果你要访问你的项目，会首先访问 Cloudflare 的公网入口，然后把你要访问的项目告诉 Cloudflare，Clodflare 就会在一堆服务器里挑一个合适的，把你的数据包通过内部高速骨干网传给那个服务器，由它处理你的请求；原理和反向代理几乎完全一样，可以叫做 “分布式反向代理集群”。为了简便，以下 Workers/Pages 简称为 Worker。

Cloudflare 代理有两个重要的问题：**优选 IP** 和 **Proxy IP**。

#### 优选 IP

因为相关法规规定，所有在中国大陆提供互联网服务的服务器和域名都需要备案，所以 Cloudflare 虽然在大陆有节点，但不提供给免费版的个人用户。当我们请求访问 Worker 时，解析出来的节点 ip 都不在大陆范围内，这里的节点就是所谓的 Cloudflare 公网入口，通常会解析到香港等地区。但由于不同运营商线路的选择，有可能实际会绕路至美国、欧洲等地，见下表：

| 运营商 | 常见解析地区                   | 说明                           |
| --- | ------------------------ | ---------------------------- |
| 移动  | 香港 HKG                   | 移动线路直连 CMI 链路速度极快。北方也可能解析至日本 |
| 联通  | 香港 HKG / 日本 NRT / 德国 FRA | 联通出海路径较多，有时会绕道欧洲或经由日本        |
| 电信  | 香港 HKG / 美国西海岸 SJC/LAX   | 电信普通 163 骨干网拥堵严重，通常优选美西大带宽节点 |

默认分配给免费用户的 IP 段由于使用者极多，容易受到运营商限速或防火墙重点关照，从而影响线路质量。这个时候我们需要选出适合你当前网络环境的公网入口，**优选 IP** 本质上是在 Cloudflare 庞大的公网入口 IP 池中进行优选。虽然这些 IP 最终都通向 Cloudflare 内部网络，但换一个 IP 可能就从绕路美西变成直连香港。还有一种站在巨人肩膀上的方法，就是**优选域名**。Cloudflare 会为不同的客户分配不同的入口 IP 组。一些企业大厂客户由于业务量大、合规性高，获配的 IP 段通常更干净，且在运营商那里的优先级更高。访问这些大厂域名解析出来的入口 IP 往往能够走更优的跨境线路，并且更加稳定。也有公益性质的自建优选地址，一些大佬愿意分享出来。你也可以自建 CDN，当然你不可能做的和大佬一样复杂且优秀。使用你自己分配的项目域名就是使用默认的 Cloudflare 为你选择的 IP 入口，这个不算优选，但为 Worker 和 Page 设置自定义域名本质上也是绕过封锁的一种手段。

#### Proxy IP

第二大问题，Cloudflare 禁止直接连接到 Cloudflare 自有的 IP 地址段（CF to CF），也就是说 Worker 代理无法直接访问由 Cloudflare CDN 托管的网站，例如ChatGPT、推特等（全世界有超过 20% 的网站都在使用 Cloudflare 的服务）；另外一些网站也会封锁来自 Cloudflare 机房 IP 的连接。这个时候我们需要一个中转 ip，通过中转 Worker 发出的请求绕过 CF to CF 和其他针对 CF ip 的封锁。

最后使用 Proxy IP 完整的流量路径是这样：用户 → 优选 IP → Worker  → Proxy IP → 目标网站

不使用 Proxy IP 的路径就省了中间那一步，这样就无法访问 CF 类网站。Proxy IP 失效同样无法访问 CF 类网站。

#### 代理协议选择

Cloudflare Worker 只支持 TCP 连接，像 Hysteria2 之类使用 UDP 协议的代理协议无法使用。你看到的大多数脚本都支持 Vless 和 Trojan 协议，他们的主要区别是 Trojan 比 Vless 多了一层密码加密，但这不代表 Vless 的安全性弱。判断安全性最重要的还是看有没有开启 TLS 加密，开启 TLS 后这两个协议外层都有 TLS 的保护，它的安全性完全不必担心。虽然对 Worker 处理 JS 的 V8 引擎来说处理加密解包开销较小，但依然会影响速度和延迟，CPU 时间必然会增加。所以理论上兼顾安全和性能最强的协议是 vless + ws + tls 组合。

---
## 进阶玩法

*这才是 Cloudflare 代理真正有意思的地方*

>v2ray 最新版本（v7.20.2）中 DoH https://dns.alidns.com/resolve 不可用，请更换成 https://dns.alidns.com/dns-query 或其他 DoH。请在参数设置，TUN 模式设置里启用旧版 tun 保护并关闭自动路由，否则 tun 模式无法连接。Alpn 请选不带 h2 的选项，推荐 h3 即可。如果其他版本也出现相同问题欢迎[在群里反馈](https://t.me/ernst_loosen_bot)。

回头我会自建优选 ip、优选域名的 api 和优选 proxyip 供大家使用，所以具体列表就不在文中展示了，只放一些经典示例。

#### 优选 IP & 优选域名

Cloudflare 公布的官方 ip 范围：[IP Ranges | Cloudflare](https://www.cloudflare.com/ips/)

CF官方优选 80 系端口：80、8080、8880、2052、2082、2086、2095

CF官方优选 443 系端口：443、2053、2083、2087、2096、8443

说明：以上端口可随便填写，开启 TLS 加密则必须选择 443 系端口。

Github 上有很多优选 IP 脚本，感兴趣可以搜搜。如优选 ipv6 的话推荐从 **2606:4700::/32** 优选，另外 2606:4700:: 本身就是一个很稳的 ip，你可以直接填进节点地址。关于优选 IP 和优选域名的关系，前者快但不稳，后者不快但稳。

优选域名也是有大厂域名和其他大佬自建域名，比如几个常用的大厂域名：

- time.is
- ip.sb
- www.visa.cn
- www.shopify.com

哦对，还打算做一个监测站，方便查看表现。对优选域名的监测已经陆续开始了。

对于有 **ipv6** 需求的情况，例如校园网免流，需要确保优选 ipv6 地址或优选的域名支持 ipv6。Cloudflare Worker 本身支持双栈进出站，并且理论上和至少从在我这里的表现来看国内使用 Cloudflare ipv6 比 v4 表现好很多。目前优选 v6 已经有了第一版，还在完善，后续会加入自动优选更新，并针对校园网免流环境建立特别优化版。

#### ECH

传统的 https 连接中，tls 握手阶段也就是连接初期，sni（Server Name Indication）是以明文的方式传输的，即使实际通信内容已使用 tls 加密，中间设备比如运营商也可以通过嗅探 sni 这部分信息知道你在访问什么域名。

ECH 全称 Encrypted Client Hello，可以加密你真实访问的域名，现在脚本基本都已支持启用，具体原理可以看 Cloudflare 博客的[这篇文章](https://blog.cloudflare.com/zh-cn/announcing-encrypted-client-hello/)。ech 需要配合 doh 使用，否则在 dns 查询阶段就会泄露访问域名，v6 doh 在[记-北科原生v6免流](https://ich.cc.cd/2026/03/27/%E8%AE%B0-%E5%8C%97%E7%A7%91%E5%8E%9F%E7%94%9Fv6%E5%85%8D%E6%B5%81%EF%BC%88%E8%BF%9B%E9%98%B6%EF%BC%89/)里收录了一些，平常我们使用阿里的 https://dns.alidns.com/dns-query 就可以，这个是支持 v6 连接的。除此之外也有大佬建的一些 doh 域名，到时候我一块放在自建 api 里列出来，多个选择吧。

#### 指定 Proxy IP

这个可就没官方的了，都是从各处搜集来的，有些非常nb的大佬甚至能分区域做机房，还有一些是用家宽。这个 proxyip 也可以自建，不难，有个 vps 就行，就是给 worker 和 vps 之间建一条隧道，用 warp 或者 socks5 啥的都行，~~但这就有点舍本逐末了，毕竟买 vps 的话 Cloudflare 应该是反过来用来加速 vps 节点的才对~~ 一般直连效果不好的 vps 可能直连有 2M ，但做成 proxyip 就有 200M，这个时候就可以理解为 Cloudflare 在给你和 vps 加速。

常见的 CF 代理脚本都内置了 Proxy IP，如果表现不佳或需要特定区域 ip 落地可以指定其他 Proxy IP。需要注意尽量使用同一区域的 Proxy IP，否则 ip 会在国家之间乱跳。大部分脚本也支持单节点指定 Proxy IP，比如甬哥脚本可以在路径（path）添加 **pyip=** ，Joey 脚本可以添加 **&wk=** 指定区域等，具体用法参考官方 README。

**🎉 彩蛋：** 之前提到一些 DNS64 对应的 NAT64 的妙用，一些 NAT64 是可以作为 Proxy IP 使用的：[NAT64/DNS64 可用性检测](https://check.nat64.cmliussss.net/)。这部分我也会挑选一些纯净的 ip 单独做成优选 proxyip（有一些 ip 比较纯净）。这些小型商业公司或组织维护的服务器一般来说比大佬维护的公益 proxy ip 要稳，但速度也较慢，且可能出现泄露 cf ip 的情况。*4.15 注：检测项目出现异常响应，可能是cf最近限制来自ip的cgi解析请求导致。可手动查看开发人员工具 check 请求响应。*

#### IP 检测 & 测速

- [IPPure](https://ippure.com/): Proxy IP 查询、IP 纯净度查询
- [What Is My IP](https://www.whatismyip.com/): 优选 IP 查询
- [Cloudflare Speed Test](https://speed.cloudflare.com/): Proxy IP 测速
- [Google Fiber](https://fiber.google.com/speedtest/): 优选 IP 测速

>尽量别用 Cloudflare 测速，对 Proxy IP 服务器压力很大。

传统但比较合理的测速方法是去油管找一段 4K 视频，右键播放器显示统计信息，关注一下掉帧数和连接速度。播放一段时间后万帧掉帧数在个位数、连接速度十万以上就算比较优秀的节点了。

*暂时想不到别的了，我先去做api*