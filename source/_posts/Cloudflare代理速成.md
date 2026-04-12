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
## TG群组

[@ernst_loosen_bot](https://t.me/ernst_loosen_bot)通过此bot加入，欢迎来玩喵~

---

>免责声明：《中华人民共和国计算机信息网络国际联网管理暂行规定》第六条规定：“计算机信息网络直接进行国际联网，必须使用邮电部国家公用电信网提供的国际出入口信道。任何组织和个人不得自行建立或者使用其他信道进行国际联网。”，第十四条：“违反本规定第六条、第八条和第十条的规定的，由公安机关责令停止联网，给予警告，可以并处15000元以下的罚款；有违法所得的，没收违法所得。”根据法律规定，请勿向他人售卖、传播vpn，否则可能构成违法行为，受到行政处罚并记录。本文既不提倡“翻墙”行为，也不提供工具和源码，仅供讨论、学习网络技术，严禁用于任何形式的非法网络访问，因技术滥用导致的一切后果由使用者自行承担。另Cloudflare服务存在明确的[使用条款限制](https://www.cloudflare.com/zh-cn/terms/)，使用者应自行斟酌。

**阅读本文可能需要对Github、代理等有一小点了解**

经过几年的技术沉淀，Cloudflare代理已经变得相对成熟，甚至一定程度上比普通机场和自建代理表现更好。本文选取几个常见的项目逐一介绍，速成并不能面面俱到，原理及进阶使用方式另开一篇。每个项目的README也有详尽的介绍。另外由于Cloudflare网站界面经常更新变动（没错我刚写完一周cf又改新界面了），本文倾向使用文字描述而不提供图片指示，我用的英文版懒得改了，有什么翻译问题不要在意。

---
## 准备及说明

如果你懒得做任何准备，请仅确保有一个[Cloudflare](https://dash.cloudflare.com/login)账户和Clash或v2rayN类的客户端。

对于更全面的配置，需要[Cloudflare](https://dash.cloudflare.com/login)账户、[GitHub](https://github.com/)账户、托管在Cloudflare的域名和客户端。

⚠️ 对于你托管在cf且要使用的域名，进入Cloudflare该域名设置 -> 网络，把**gRPC**打开。同时推荐禁用默认分配的域名，防止他人扫描消耗请求额度等。

客户端推荐：

- [FlClash](https://github.com/chen08209/FlClash/releases/latest)：Windows、Android、macOS、Linux
- [Clash Verge](https://github.com/clash-verge-rev/clash-verge-rev/releases/latest)：Windows、macOS、Linux
- [v2rayN](https://github.com/2dust/v2rayN/releases/latest)：Windows、macOS、Linux
- [v2rayNG](https://github.com/2dust/v2rayNG/releases/latest)：Android
- [SingBox](https://github.com/GUI-for-Cores/GUI.for.SingBox/releases/latest)：Windows、Linux
- [Hiddify](https://github.com/hiddify/hiddify-app/releases/latest)：Windows、Android、[iOS](https://apps.apple.com/us/app/hiddify-proxy-vpn/id6596777532)、macOS、Linux

特别提醒：不支持Wireguard客户端及L2TP类。

⚠️ Cloudflare的Workers域名`workers.dev`被严重污染，大部分被墙，需要通过代理访问。同理需要通过代理订阅更新。较新版本的v2ray等一些客户端支持ECH和分片（Fragment）功能，开启后对于绕过域名阻断有一定的效果。Pages域名`pages.dev`大部分情况可以直接访问。如果你部署正常项目都1101，可以重新设置账户域名或干脆注册一个新账户。

如果需要使用其他类型的订阅，可以使用[Subconverters](https://subconverters.com/)等其他订阅转换工具。

⚠️ Cloudflare Workers & Pages每日限量10w请求数，但基本上用不完，而且有人用完之后还可以使用。理论上Cloudflare代理拥有无限流量，并且极难死绝。切记切记不要使用未经混淆的包含代理功能的JS源码部署项目，否则Cloudflare账户基本可以扔了。

（排序即推荐顺序）

---
## 一、甬哥侃侃侃

>YouTube：[@ygkkk](https://www.youtube.com/@ygkkk)
>Github：[@yonggekkk](https://github.com/yonggekkk)

要用到的项目：[Github: yonggekkk/Cloudflare-vless-trojan](https://github.com/yonggekkk/Cloudflare-vless-trojan)

通过上面的链接进入此项目Github仓库，点开[Vless_workers_pages](https://github.com/yonggekkk/Cloudflare-vless-trojan/tree/main/Vless_workers_pages)文件夹，点击[worker混淆.js](https://github.com/yonggekkk/Cloudflare-vless-trojan/blob/main/Vless_workers_pages/_worker%E6%B7%B7%E6%B7%86.js)，复制全部代码。此项目不能使用Pages部署方式，登陆Cloudflare后进入仪表板（Dashboard），在左侧找到**计算 -> Workers & Pages -> 创建应用 -> 从Hello World开始**，随便取一个名字，或保持默认，点击部署。部署完成后点击右上角编辑代码，把代码区原本的代码全部删除，将复制的代码粘贴进去。找一个uuid生成器，[比如这个](https://uuidgenerator.dev/)，生成一个随机uuid粘贴到`userID = ""`的双引号中，把原来的值覆盖掉，不要有空格。点击右上角部署，部署完成后回到你的项目页面。

如果你没有托管在Cloudflare上的域名，到这里就算完成了。在右边一栏有你的项目的域名，粘贴到地址栏并在后面加上`/你刚才生成的uuid`获取你的订阅链接。v2ray使用第一个聚合通用订阅链接，clash使用第二个Clash-meta订阅链接。别忘了通过代理订阅更新。

如果你有域名，可以进行接下来的配置。进入你的项目的设置，在域和路由禁用分配的workers.dev域名。点击添加 -> 自定义域名，给你的项目分配一个二级域名（需要填写完整域名）。添加好以后可以通过`自定义域名/uuid`进入订阅页面。这样订阅更新时就不需要通过代理了，并且可以使用TLS加密节点。

---
## 二、Joey blog

>YouTube：[@joeyblog](https://www.youtube.com/@joeyblog)
>Github：[@byJoey](https://github.com/byJoey)

项目地址：[Github: byJoey/cfnew](https://github.com/byJoey/cfnew)（界面有点雷霆，之前不是这样的😅）

此项目可以通过Workers或Pages部署，区别在于Workers可以使用xhttp协议，而Pages不行，无关痛痒。Workers部署使用代码文件[少年你相信光吗](https://github.com/byJoey/cfnew/blob/main/%E5%B0%91%E5%B9%B4%E4%BD%A0%E7%9B%B8%E4%BF%A1%E5%85%89%E5%90%97)，方法同上，不再赘述。部署好以后需要配置uuid变量和KV，详细方法见下文Pages部署方式。

Pages部署方式请在Github项目页面右侧Releases下载最新版本`Pages.zip`。Cloudflare仪表板找到**计算 -> Workers & Pages -> 创建应用**，在页面下方找到`Looking to deploy Pages? Get started`这行小字，点开链接，选择拖入你的文件，为项目取一个名字或保持默认，下一步上传刚刚下载的文件，不需要解压。等待部署完成后点击继续处理项目，进入项目页面，转到设置 -> 变量和机密 -> 添加。变量名称填`u`（注意大小写），值填一个随机生成的uuid，都不要有空格或换行。点击部署。在Cloudflare仪表板找到**存储和数据库 -> Workers KV -> 创建实例**，随便取个名字即可。创建好后返回你的项目设置，找到绑定，添加KV命名空间，变量名称填写大写`C`，然后选择刚刚创建的KV，保存。点击右上角创建部署，再上传一遍刚刚的文件，保存并部署。完成后可通过`项目域名/uuid`访问订阅页面。

>⚠️ Cloudflare更新js运行时后，现在部署需要在项目设置里把运行时兼容日期改成**2026-01-20**。

配置方面建议启用Vless和Trojan协议，并启用ECH，保存协议配置。如果不想使用非TLS节点，可以往下拉，找到TLS控制，选择仅TLS节点，保存高级配置（建议启用仅TLS）。然后可以在最上方选择客户端处按需导入订阅。

同样推荐启用自定义域名。

---
## 三、CM

>Youtube：[@CMLiussss](https://www.youtube.com/@CMLiussss)
>Github：[@cmliu](https://github.com/cmliu)

项目地址：[Github: cmliu/edgetunnel](https://github.com/cmliu/edgetunnel)

支持Workers部署，使用文件[_worker.js](https://github.com/cmliu/edgetunnel/blob/main/_worker.js)；

支持Pages部署，使用文件[main.zip](https://github.com/cmliu/edgetunnel/archive/refs/heads/main.zip)。

需要配置变量，名称填写`ADMIN`，值为你的管理员密码。Pages需要重新创建部署，Workers则立刻生效。

需要配置KV命名空间，绑定时变量名称填写`KV`。Pages记得重试部署。

推荐启用自定义域名。

访问`你的域名/admin`输入管理员密码即可登录后台。

---
## 四、BPB

经典项目，地址：[Github: bia-pain-bache/BPB-Worker-Panel](https://github.com/bia-pain-bache/BPB-Worker-Panel)

不推荐新手使用，有兴趣可以折腾。

---
## 五、佬王

>YouTube：[@eooce](https://www.youtube.com/@eooce)
>Github：[@eooce](https://github.com/eooce)

项目地址：[Github: eooce/Cloudflare-proxy](https://github.com/eooce/Cloudflare-proxy)

多种选择，不详细介绍了，看README即可。内置Proxy IP多半失效。相比之下有些简陋和鸡肋。

---
## 五、白嫖Snippets

项目地址：[Github: cmliu/CF-Workers-BPSUB](https://github.com/cmliu/CF-Workers-BPSUB)

进入proxy_host，复制_worker.js的全部代码，在Cloudflare新建一个Workers，粘贴覆盖源码，将第一行`const hostname = ""`双引号中的值改为`snip.yutian.nyc.mn`。部署后，设置一个自定义域名。在[BPSUB Snippets订阅生成器](https://bp.sub.cmliussss.net/)中把你的自定义域名填入HOST，优选IP设置改为优选订阅生成器，填入优选订阅生成器地址，以下随便选一个：

- owo.o00o.ooo
- vless.cmliussss.net
- sub.danfeng.eu.org
- cfsub.cfcdn.xx.kg

节点高级设置勾选跳过证书验证。订阅链接导入软件即可使用。

---
## 测速及ip、ping测试

**测速**：[Google Fiber](https://fiber.google.com/speedtest)、[Cloudflare](https://speed.cloudflare.com)、[Speedtest.cn](https://speedtest.cn)、[Fast.com](https://fast.com)

**IP**：[IPPure](https://ippure.com/)、[CM](https://cf.090227.xyz)、[IP.SB](https://ip.sb)、[ip-api](https://ip-api.com)、[What Is My IP](https://www.whatismyip.com)

**Ping/TCPing**：[vps789](https://vps789.com)、[itdog](https://www.itdog.cn)

尽量少测速，这样会给proxyip服务器带来较大的cpu载荷，影响线路质量。更常见的测试方法是Youtube 4k视频。跳ip是正常现象，有些AI需要非香港地区ip，请尝试切换订阅。可指定国家ip的方法将在四月底前更新在Cloudflare代理进阶，敬请期待！

---
## 结语

本文仅提供了最最基本的信息，很多奇妙小技巧另开篇目，未详尽之处恳请指出。下一篇：Cloudflare代理进阶