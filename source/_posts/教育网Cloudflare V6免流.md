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
  - Project
  - Proxy
---
之前原生V6免流的方法有一定局限，一些服务器可能不支持DNS64请求。用CF的这个方法比较繁琐，并且涉及到代理的一些成分，但兼容性和安全性确实更好。另外玩游戏还是老老实实用流量。

**还没做好，下面只是提纲！！**

## 一、WARP/Zero Trust

warp+wireguard/warp+/warp+masque/zero trust+masque/提取warp wireguard/优选ip

## 二、套用Cloudflare代理

推荐阅读：[Cloudflare代理速成](https://ich.cc.cd/2026/03/31/Cloudflare%E4%BB%A3%E7%90%86%E9%80%9F%E6%88%90/)