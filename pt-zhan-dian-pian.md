---
description: 介绍PT站点基础知识
cover: >-
  https://images.unsplash.com/photo-1579942440390-7a275bcdec21?crop=entropy&cs=tinysrgb&fm=jpg&ixid=MnwxOTcwMjR8MHwxfHNlYXJjaHw0fHxET1dOTE9BRHxlbnwwfHx8fDE2NTg5NzkyNTg&ixlib=rb-1.2.1&q=80
coverY: 0
---

# PT站点篇

## PT黑话

#### 考核期 <a href="#kao-he-qi" id="kao-he-qi"></a>

大部分站点都有邀请考核制度，需要在一个月时间内达成一定的上传量、下载量以及魔力值。部分站点考核由于不计算魔力值的兑换上传量，建议老老实实下载热门种子并做种。

邀请者可以消耗数倍的魔力值来兑换一个无需新手考核的邀请码。被邀请人也可以付费捐赠（几百元不等）来免除新手考核。

#### 考核后

对于一些以影视作品为主的网站，尽可能的下载热门种子，这样能更快的获得上传量。可以使用 BT 客户端的 RSS 订阅功能，实现无人值守下载。\
对于以小种为主的 PT 站，如 OpenCD 以及大部分教育网站点，则需要通过下载大量小体积种子并长时间做种以换取魔力值，再使用魔力值兑换上传。

#### 养老期

此时混 PT 只为偶尔下几个资源，而不是那么注重参与了，因此保持一个良好的分享率以备不时之需是很有必要的。

部分站点可以通过捐赠获得终身 VIP 头衔，从此不再需要担心分享率问题。

#### 刷流量

想快速将上传、下载流量刷上去，最快的办法就是租用 Seedbox。Seedbox 即专用于跑 BT/PT 的服务器，一般位于海外（德国、罗马尼亚、荷兰）等地。

可以通过 SeedBox 例如 [FeralHosting](https://feralhosting.com/) 或 [Seedbox.io](https://seedbox.io/) 配合 RSS 订阅来实现无人值守下载。由于 Seedbox 带宽相对较大（普遍在 1-10Gbps 左右），盒子间互刷效果过于明显，部分 PT 站会有 seedbox 限制政策，例如限制上传、下载速度或者最大上传量。

也可以通过 [Hetzner](https://www.hetzner.com/)、[Kimsufi](https://www.kimsufi.com/en/)、[OVHCloud](https://www.ovhcloud.com/) 等云服务及托管商购买独立服务器（dedicated server，也称作独服、杜甫）。“大盘鸡”是坊间黑话，用以指代硬盘很大的服务器。

当然，也可以自己搭建黑群晖或直接使用群晖 Synology、威联通 QNAP 等成品 NAS 或使用 OpenMediaVault、VMWare ESXi、UNRaid 搭建私人的 HTPC 平台。

## PT站点介绍

### PT站点数据

那个思维导图就不上了，从数据上看看各站的长项吧

![](.gitbook/assets/image.png)

国内 PT 站可以划分为两个系别：**教育网 PT 站** 与 **公网 PT 站**。

### 教育网 PT 站点

以[北邮人](http://bt.byr.cn/)（北京邮电大学）、[蒲公英](https://npupt.com/)（西北工业大学）、[极速之星](http://bitpt.cn/)（北京理工大学）和[六维空间](http://bt.neu6.edu.cn/)（东北大学）为主。

高校校园网提供的 IPv4 网络一般是有计费及限速策略的，由校方向电信、联通、移动、鹏博士等运营商采购带宽并在出口实施自动分流。而为了推广 IPv6 业务，各高校所接入的中国教育网（CERNET）的 IPv6 网络一般是不计费且不限速的，因此组建一个依托于 IPv6 的免费资源分享网络有着很大意义。

一般来说，教育网 PT 站的原创影视资源较少，大部分为转载资源。Coursera、Udacity 等公开课资源（WEB-DL）、考研视频、电子书等内容较多，此外，北邮人等站点还提供一些诸如 Steam 游戏数据备份文件，这样学生就不用担心被几十个 G 的 Steam 游戏更新榨干网费了。

目前来看教育网 PT 站或多或少获得了学校网络中心的技术和政策支持，所以才能存留至今。部分教育网 PT 站仅允许 IPv6 或仅归属于教育网 ASN 的 IPv6 访问，因此对于公众来说访问有些难度，可以通过 HE.net 提供的 IPv6 TunnelBroker 隧道，或者 IPv6 VPS 来中转流量。

从分享率和发种规范来看，教育网 PT 十分宽松，但保种率可能不如公网 PT 站高，资源也没那么丰富。但对于追热门电影和美剧的轻度用户来说还是足够了。

### 公网 PT 站点

传说中国有五大公网 PT 站：HDS、TTG、HDC、CHD、HDR，也有三大 PT 站的说法（即 CHDbits、HDChina、TTG）。后来经过世界版权日风波（即被称作“中国版权第一案”的思路网侵权案），[HDStar（思路网）管理组被捕入狱](https://zh.wikipedia.org/wiki/%E6%80%9D%E8%B7%AF%E7%BD%91)，剩下的站点也多多少少受到了一些影响，有些直接关闭了，有的则隐没了。

希望考古中国 PT 站历史的朋友可以查看这个帖子：[国内外PT站点评](https://web.archive.org/web/20210404133244/https://www.douban.com/group/topic/27989385/)。希望考古国际 PT 站的朋友，可以查看这个帖子：[PT站点大全](https://web.archive.org/web/20201121094012/https://www.douban.com/group/topic/32448115/)。历史比较悠久了，仅供参考。

距离版权日风波已经过去了七八年，现在国内 PT 站又呈现出繁荣景象。一方面国内电视剧集的分级制度迟迟未推出，而 Netflix、HBO、Disney+、Apple TV+ 等境外流媒体业务的订阅费用也居高不下。另一方面，国内百兆、千兆家庭宽带已经覆盖到了三四线城市甚至是县城，养一个 PT 账号的成本已经相当低了。

在此列出几个常见的站点，仅供参考。

* 目前以影视资源为主的有
  * [HDChina 瓷器](https://hdchina.org/)：近 10 年的老站，用户数据继承自原先的 HDWinG 和 HDStar。资源方面，官方制作组（HDCTV 和 HDChina）的 Netflix、HBO 剧集，原盘，录制的电视剧比较多。
  * [CHDbits 彩虹岛](https://chdbits.co/)：近 10 年的老站点，影视资源很丰富。
  * [SSD (Spring Sunday)](https://springsunday.net/)：2010 年创建的老站点，前身为 CMCT 触摸春天。
  * [M-Team 馒头](https://www.m-team.cc/)：有比较多的成人内容，官方提供付费邀请码购买渠道。
  * [BT School 比特校园](https://pt.btschool.club/)：2019 年刚创建的新站点，门槛相对比较低。
  * [HD Time 高清时间](https://hdtime.org/)：老站点，但是没什么名气。
  * [52PT 我爱PT](https://52pt.site/)：新站点。
  * [HDSky 天空](https://hdsky.me/)：综合站点。
  * [TTG](https://totheglory.im/upload.php)：国内大站之一，2014 年末作为 TTG 出现。
  * [Ourbits 我堡](https://ourbits.club/)：算是国内大站了，没啥特色。
  * [HDHome 家园](https://hdhome.org/)：2015 年左右成立的 PT 站，没啥特色。
  * [PTHome 铂金家](http://www.pthome.net/)：2018 年左右成立的新站，没啥特色。
  * [HDCity 城市](https://hdcity.city/)：2016 年底成立的新站（相对来说）。
  * [NicePT 老师站](https://www.nicept.net/)：又成为小馒头（相对于 M-Team 来说），主要为成人内容。
  * [CCFBits 精品高清](http://ccfbits.org/)：挺老的大站了，资源很丰富也很低调。
  * [HDR (HDRoute)](http://www.hdroute.org/)：前身为 HDRoad 思路高清，历史悠久。
  * [LeagueHD 柠檬](https://lemonhd.org/)：小站中发展的还算不错的，2019 年成立。
  * [Haidan 海胆之家](https://www.haidan.video/)：2020 年刚成立的小站。
* 教育网 PT，部分 tracker 可能仅允许教育网或 IPv6 连接。入站门槛较低，可以凭借 edu 教育域名的邮箱进行注册，也可以通过校内用户发起邀请。对分享率和种子规范的限制比较宽松。
  * [NanyangPT 南洋](https://nanyangpt.com/)：西安交通大学 PT 站
  * [BYR 北邮人](https://byr.pt/)：北京邮电大学 PT 站，仅对 IPv6 开放
  * [TJUPT 北洋园](https://www.tjupt.org/)：天津大学 PT 站
  * [葡萄PT](https://pt.sjtu.edu.cn/)：上海交通大学 PT 站
  * [NPUPT 蒲公英](https://npupt.com/)：西北工业大学 PT 站
  * [BITPT 极速之星](http://bitpt.cn/)：北京理工大学 PT 站
  * [六维空间](http://bt.neu6.edu.cn/)：东北大学 PT 站
  * [NexusHD](https://nexushd.org/)：浙江大学 PT 站，仅供校内用户访问。
* 以动漫资源为主的有
  * [U2 动漫花园 幼儿园](https://u2.dmhy.org/)：老牌动漫 PT 站。各种上古资源都能找到。
  * [Skyey Snow 天雪动漫](https://www.skyey2.com/)：基于 Discuz! 构建的 PT 站点，相对来说新一些。
* 以音乐资源为主的有
  * [OpenCD 皇后 PT](https://open.cd/)：小体积种子比较多，基本靠攒魔力值来保号。

## PT站点政策 <a href="#yong-hu-gui-fan" id="yong-hu-gui-fan"></a>

参考 [最新限制表](https://www.kdocs.cn/l/sEi6Sg5iu)

对于用户，无论是公网 PT 站还是教育网 PT 站，往往都有以下要求：

1. 上传、下载量：新手考核计划一般为一个月内 `>=50GB` 的上传下载量。部分教育网 PT 站可能没有新手考核要求。
2. 分享率（上传量/下载量）：PT 站的优质资源离不开各位用户的分享和做种。为了减少吸血鬼用户的影响，几乎所有 PT 站点都会要求用户的风险率超过一定值。除此之外，用户分享率与用户等级相关，如果风险率低于标准会自动回退到较低的用户等级。
3. 魔力值（Bonus points，即积分，获得于用户赠送、活动奖励或做种）：魔力值可以用于兑换站内头衔、更改用户名、消除广告，甚至至博彩小游戏（例如赌球或 24 点，常见于世界杯期间）。
4. 最小做种时间：为了保障种子活跃度，一些 PT 站严格禁止下完就跑（称为 H\&R, Hit and Run）的行为，要求用户至少持续做种一定时间）。摘录一段 OpenCD 的规则作为参考。

### 1 盒子限制政策

![](<.gitbook/assets/image (12).png>)

![](<.gitbook/assets/image (13).png>)

### 2 HR考核政策

![](<.gitbook/assets/image (14).png>)

### 3 定期考核政策

![](<.gitbook/assets/image (3).png>)



