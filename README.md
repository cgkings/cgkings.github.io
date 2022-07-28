---
cover: >-
  https://images.unsplash.com/photo-1642507229657-51fc87152fc7?crop=entropy&cs=tinysrgb&fm=jpg&ixid=MnwxOTcwMjR8MHwxfHNlYXJjaHwyfHx1cGxvYWRlcnxlbnwwfHx8fDE2NTg5Nzk3MzA&ixlib=rb-1.2.1&q=80
coverY: 0
---

# 前言

PT (Private Tracker) 是一种基于私有 BT Tracker 服务器的资源传播形式，经授权的用户使用受允许的客户端进行种子制作与下载。相较于传统 BT 和 emule，PT 站往往采取了严格的邀请制度以及免责制度来规避法律风险，同时要求用户客户端开启传输加密以绕过运营商的检测策略。当然，从实际上来说 PT 站的运营、使用仍然是违背了各国版权法的。

许多高清爱好者聚集在 PT 站，发布翻录的蓝光原盘、CD 资源以及录制的高清卫星电视讯号；得益于 Netflix、HBO、Apple TV 等高清流媒体在线视频平台的发展，近年也出现了一些 WEB-DL (Download from Web) 资源。

目前国内使用的 PT 站源码大部分为基于浙江大学 [xiazuojie](mailto:xiazuojie@gmail.com) 团队所开发的开源整站项目 [NexusPHP](https://sourceforge.net/projects/nexusphp/)，基于 PHP + MySQL + memcached。部分站点使用 Discuz! 进行二次开发。

日前，[@burpheart](https://github.com/burpheart/) 基于上游项目，开发了新版的 [NexuxPHP Safe](https://github.com/burpheart/NexusPHP\_safe)。其修复了一些已知的安全漏洞，并加入了一些增强用户体验的小功能。

2022 年更新：[@burpheart](https://github.com/burpheart/) 似乎逐步淡出了项目维护。目前项目迁移至 [xiaomlove/nexusphp](https://github.com/xiaomlove/nexusphp)，由 [@xiaomlove](https://github.com/xiaomlove/) 为主的开发者继续维护。
