---
author: Raziore
title: 贝壳校园网：代理与免流
date: 2026-10-19T15:40:00+08:00
description: 链式代理和地区选择
top_img: https://img.ich.cc.cd/file/ichblog/post_img/cover/110618471_p1.webp
cover: https://img.ich.cc.cd/file/ichblog/post_img/cover/110618471_p1.webp
tags:
  - Net
  - Tech
  - Proxy
  - Cloudflare
  - 校园网
categories: Proxy
---
👉 **[校园网免流软件](https://freev6.cc.cd/)**

>免责声明：《中华人民共和国计算机信息网络国际联网管理暂行规定》第六条规定：“计算机信息网络直接进行国际联网，必须使用邮电部国家公用电信网提供的国际出入口信道。任何组织和个人不得自行建立或者使用其他信道进行国际联网。”，第十四条：“违反本规定第六条、第八条和第十条的规定的，由公安机关责令停止联网，给予警告，可以并处15000元以下的罚款；有违法所得的，没收违法所得。”根据法律规定，请勿向他人售卖、传播vpn，否则可能构成违法行为，受到行政处罚并记录。本文既不提倡“翻墙”行为，也不提供工具和源码，仅供讨论、学习网络技术，严禁用于任何形式的非法网络访问，因技术滥用导致的一切后果由使用者自行承担。另Cloudflare服务存在明确的[使用条款限制](https://www.cloudflare.com/zh-cn/terms/)，使用者应自行斟酌。

经常有人问，可不可以开免流同时使用代理。理论上讲是可以的，从 FreeV6 v0.4.9 开始，设置中的链式代理功能正是为了实现“代理嵌套”，将已有的订阅节点套在免流节点里，实现方法参见[虚空终端 Docs](https://wiki.metacubex.one/config/proxies/dialer-proxy/)。那没有自己的订阅节点有没有解决办法呢，也有的。只是需要换一种免流方式。按照我的风格，它依旧不需要花钱。如果你已经有节点了，这说不定是一种更好的解决方案。

FreeV6 使用的是 Warp 节点，免费版 Warp 不支持更换节点地区，只能就近分配，通常分到北京、天津或香港。对于比较常见的科研场景需求，例如使用 AI 服务，通常使用香港节点无法满足地区政策要求，而使用美国节点延迟又会太高。按照我的经验，我会推荐使用新加坡节点，平衡延迟、速度和 ip 纯净度，注意是在贝壳校园网环境下。为了实现更换出口 ip 地区——

**Introducing Cloudflare Workers!**

是的还是我们的赛博大善人。在之前的文章中我已经介绍过 Cloudflare Workers 实现代理的方法，这里当作前置内容，能了解一下最好，详见 [Cloudflare代理速成](https://ich.cc.cd/2026/03/31/Cloudflare%E4%BB%A3%E7%90%86%E9%80%9F%E6%88%90/) 及 [Cloudflare代理进阶](https://ich.cc.cd/2026/04/12/Cloudflare%E4%BB%A3%E7%90%86%E8%BF%9B%E9%98%B6/) ，重点是优选 ip 和 proxy ip。之前我排序推荐的项目是 [Cloudflare-vless-trojan](https://github.com/yonggekkk/Cloudflare-vless-trojan)，但现在我会推荐首选 [edgetunnel](https://github.com/cmliu/edgetunnel)。这里详细介绍的方法就采用这个项目。

项目 Github：[@cmliu/edgetunnel](https://github.com/cmliu/edgetunnel)

官方已有足够详尽的部署教程，我贴在这里，并更改了几处对于 Cloudflare 网站的描述。可以采用两种方式部署，Workers 和 Pages， 选其一即可。这里有一个两种部署方式区别的简单对比，方便做出选择。

|            | Pages | Workers | 备注                    |
| ---------- | ----- | ------- | --------------------- |
| 自定义域名      | 不需要   | 需要      | Workers默认域名被墙         |
| 选择部署地域     | 不支持   | 支持      | 实例可选部署地区，影响不大         |
| 自定义域名必须同账户 | 否     | 是       | Workers不能连接未托管在此账户的域名 |

关于免费的二级域名，可以看这：[可托管Cloudflare的免费域名](https://ich.cc.cd/2026/03/31/%E5%8F%AF%E6%89%98%E7%AE%A1Cloudflare%E7%9A%84%E5%85%8D%E8%B4%B9%E5%9F%9F%E5%90%8D/)

<details>
<summary><code><strong>Pages 部署教程</strong></code></summary>

1. 部署 CF Pages：
   - 下载 [main.zip](https://github.com/cmliu/edgetunnel/archive/refs/heads/main.zip) 文件
   - 在侧栏选择 `计算` > `Workers 和 Pages` ，点击 `创建应用程序` > `继续前往 Pages`，选择 `拖放文件`后，为你的项目取名后点击 `创建项目`，然后上传你下载好的 [main.zip](https://github.com/cmliu/edgetunnel/archive/refs/heads/main.zip) 文件后点击 `部署站点`
   - 部署完成后点击 `继续处理站点` 后，选择 `设置` > `变量和密钥` > 添加，类型为文本，变量名称填写**ADMIN**，值则为你的管理员密码（你自己定），点击 `保存`

2. 绑定 KV 命名空间：
   - 在侧栏选择 `存储和数据库` > `Workers KV` ，点击 `创建 KV 命名空间`，名称可以随便取，数据位置选择 `标准`
   - 在你刚刚创建的 Pages 的 `设置` 选项卡中选择 `绑定` > `添加` > `KV 命名空间`，`变量名称` 填写**KV**，然后选择刚才创建的 KV 命名空间，点击 `保存`
   - 在右上角点击 `创建部署` 后，重新上传 [main.zip](https://github.com/cmliu/edgetunnel/archive/refs/heads/main.zip) 文件后点击 `保存并部署`

3. （可选）给 Pages 绑定 CNAME 自定义域：[视频教程](https://www.youtube.com/watch?v=LeT4jQUh8ok&t=851s)
   - 在 Pages 的 `自定义域` 选项卡，点击 `设置自定义域`
   - 填入你的自定义子域名，注意避免使用根域名
   - 按照 CF 的要求返回你的域名 DNS 服务商，添加该自定义域的 CNAME 记录后，点击 `激活域`
   
4. 访问后台：
   - 访问 `https://你的Pages项目域名/admin` 输入你设置的管理员密码即可登录后台

</details>

<details>
<summary><code><strong>Workers 部署教程</strong></code></summary>

1. 部署 CF Worker：
   - 在侧栏选择 `计算` > `Workers 和 Pages` ，点击 `创建应用程序` > `从 Hello World! 开始`，为你的项目取名后点击 `部署`
   - 点击右上角 `编辑代码`，将 [worker.js](https://github.com/cmliu/edgetunnel/blob/main/_worker.js) 的内容粘贴到编辑器中，编辑器中原内容删除即可，点击右上角 `部署`
   - 返回 Workers 项目，在 `设置` 选项卡中，选择 `运行时变量和密钥` > `添加变量`，密钥填写**ADMIN**，值则为你的管理员密码（你自己定），点击 `添加变量并部署`

1. 绑定 KV 命名空间：
   - 在侧栏选择 `存储和数据库` > `Workers KV` ，点击 `创建 KV 命名空间`，名称可以随便取，数据位置选择 `标准`
   - 在你刚刚创建的 Pages 的 `设置` 选项卡中选择 `绑定` > `添加绑定` > `KV 命名空间` > `添加绑定`，`变量名称` 填写**KV**，然后选择刚才创建的 KV 命名空间，点击 `添加绑定`

1. 给 Workers 绑定 自定义域： 
   - 在 Workers 的 `域` 选项卡，点击 `添加域名`
   - 选择你已转入的域名，填写自定义子域名，点击 `添加域名`，等待证书生效即可

4. 访问后台：
   - 访问 `https://你的Workers项目域名/admin` 输入管理员密码即可登录后台

</details>

后台默认设置就可以使用了，但默认设置不方便免流用，落地 ip 也是随机的。我们需要改几个设置。先在后台右上角点击白色按钮“我是高手”。进入高级模式后，可以更改更多设置。我们主要修改的就是**优选 ip** 和 **proxy ip**。

在 `优选订阅生成` 可以更改优选 ip，将优选订阅模式改为自定义订阅后填写。优选 ip 可以理解成接入点，是所属 Cloudflare 的官方 ip。由于 Cloudflare 的 ip 是泛播 ip，不同地区甚至是不同运营商的策略差异，都会极大影响连通质量。所以优选 ip 并不存在真正的通用选项，根据当时的网络环境而定。将优选 ip 聚集起来做成某个域名的 DNS 记录，这个域名就是优选域名，通常是对某个运营商做特异优化。一些使用 Cloudflare 网络的大厂域名也可以当作优选域名使用，因为它们本身就指向 Cloudflare 官方 ip，且更加稳定。绝大多数优选域名都是纯 IPv4 或 IPv4 + IPv6 双栈，我们要实现纯 IPv6 免流不能直接拿来用，否则连接可能走 IPv4。目前推荐拿来优选的 Cloudflare 官方 IPv6 段是 **2606:4700::/32**。可以利用一些开源的优选工具来快速筛选，例如 [CFData-WEB](https://github.com/PoemMisty/CFData-WEB)（操作比较简单）。校园网环境特殊，建议不要使用网络上现成的优选 ip 或 api，表现可能不尽人意。Workers & Pages 支持 TLS 的端口有 443、2053、2083、2087、2096、8443，可与优选 ip 任意组合使用。 `[2606:4700::]` 本身就相当于一个优选 ip，可以直接使用。

在 `Cloudflare CDN 访问设置` 可以更改 proxy ip，将反代模式改为 PROXYIP，并取消启用自动获取。proxy ip 是比较重要的，这也是更换出口 ip 的关键。它相当于 Workers 代理加上一个链式代理，proxy ip 作为访问使用 Cloudflare 网络的网站的出口 ip，落地 ip 为非 CF ip（CF ip 不能访问同为 CF 网络的其他 ip）。流量链路为：用户 → 优选 ip → Workers / Pages → proxy ip → 目标。Workers & Pages 具有双栈，所以 proxy ip 是 IPv4 或 IPv6 都可以。在 CF 代理中，proxy ip 同样可以以域名的形式存在，通常按地区区分。例如 `proxyip.hk.cmliussss.net`，hk 可换成 sg、us、jp 等。proxy ip 有效性检测网站可以使用 [Check ProxyIP](https://check.proxyip.cmliussss.net/)（[CF-Workers-CheckProxyIP](https://github.com/cmliu/CF-Workers-CheckProxyIP)）。更多 proxy ip 的获取就靠自己找了，或者[加 tg 群](https://t.me/ernst_loosen_bot)了解。proxy ip 的纯净度也是能否正常访问 AI 服务的关键，所以好的 proxy ip 来之不易。另外它的更新频率也较快，像我举的例子就是三小时一更，所以如果你在订阅中用的 proxy ip 失效了，先更新下订阅。

最后提醒免责声明里的风险项不是虚有，尊重法规，谨慎学习。