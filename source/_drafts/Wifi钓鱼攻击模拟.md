---
author: Raziore
title: Wifi钓鱼攻击模拟
date: 2026-03-16T23:16:00+08:00
description: 我没干那事。。
draft: true
tags:
  - Security
  - Net
  - Tech
categories: Project
---

免责声明：仅供学习网络安全使用，请勿在未经明确授权的情况下于公共网络空间进行测试。因使用本材料而导致的任何法律后果或其他损失，作者概不负责。

首先需要一个前端页，做成什么样无所谓，我们只需要用户名和密码两个输入框。

先把要post的表单action地址改为"你指定的服务器ip/login" ，把登录表单id改成loginForm，把用户名和密码元素的id分别改为username和password。登录按钮改成button，避免其他js干扰。

如果要指定服务器ip或dhcp网段，可以在文件中修改"_" 字段，wifi名称修改"_" 。

先确认网卡支持监听模式：iw dev，假设无线网卡是wlan0（如果不是，需要在文件中修改）。

把wifi_portal.py和index.html放在同一目录下，如果登录页使用了外部静态资源，还需要在脚本中更改路径名。

另外需要特别注意的一点是字符编码格式，由于linux一般不会自动判断html的编码格式，如果html声明的编码不正确或linux自身不支持，网页内容就会乱码，建议使用支持比较广泛的utf-8格式。路径和文件名也尽量不要出现中文字符。

我使用的kali linux已经包含了大部分组件，只需要再安装两个依赖：pip install flask colorama，最后sudo python3 wifi_portal.py启动服务。

如果报错dhcp没有足够的ip池，结束并重启脚本再试。成功启动后，终端里就会出现包含username和password的请求片段了。

详解：