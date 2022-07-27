# Qbittorrent

## 安装

\#宿主机root用户环境下使用例子(非官方,仅供参考)

\#创建要挂载的目录,此时该目录属root用户和root组&#x20;

mkdir -p /home/qbt/config

\#创建docker用户(默认会顺带新建同名Group)&#x20;

useradd dockeruser&#x20;

\#修改文件夹归属(R代表递归操作,文件夹下的也一并修改)&#x20;

chown -R dockeruser:dockeruser /opt/letsencrypt&#x20;

\#查看dockeruser的用户id和群id&#x20;

id dockeruser

### Docker版qbt--cgkings自用版

{% embed url="https://hub.docker.com/repository/docker/cgkings/qbittorrent" %}

{% embed url="https://github.com/cgkings/cg_dockerfiles/tree/main/qbittorrent" %}

* 基于debian:bookworm-slim镜像制作
* qBittorrent版本为`v4.4.3.1`
* 已内置GeoIP数据库
* 包含下载完成自动上传脚本
* 默认设置为简体中文界面
* 默认用户名/密码是`admin/adminadmin`

```
 docker run -d --name=qbittorrent \
  -e PUID="$UID" \
  -e PGID="$GID" \
  -e WEBUI_PORT="8070" \
  -e BT_PORT="34567" \
  -p 34567:34567 \
  -p 34567:34567/udp \
  -p 8070:8070 \
  -v /home/qbt/config:/etc/qBittorrent \
  -v /home/qbt/downloads:/downloads \
  -v /usr/bin/fclone:/usr/bin/fclone \
  -v /root/.config/rclone/rclone.conf:/root/.config/rclone/rclone.conf \
  -v /home/vps_sa/ajkins_sa:/home/vps_sa/ajkins_sa \
  --restart unless-stopped \
  cgkings/qbittorrent:latest
```

大部分设置均可通过WEB界面直接修改，若需要修改部分特殊配置，可自行修改配置文件夹<mark style="color:orange;">`/你的挂载路径/config/qBittorrent.conf`</mark>，修改后重启下容器`docker restart qbittorrent`

### Docker版qbt-nevinee PT专用版

{% embed url="https://hub.docker.com/r/nevinee/qbittorrent" %}

{% embed url="https://github.com/devome/dockerfiles/tree/master/qbittorrent" %}

* 自动按`tracker`分类（**可以选择关闭**）。
* 下载完成发送通知（**可以选择关闭**），可选途径：钉钉（[效果图](https://gitee.com/evine/dockerfiles/raw/master/qbittorrent/pictures/notify.png)）, Telegram, ServerChan, 爱语飞飞, PUSHPLUS推送加；搭配RSS功能（[RSS教程](https://www.jianshu.com/p/54e6137ea4e3)）自动下载效果很好；下载完成后还可以补充运行你的自定义脚本。
* 故障时发送通知，可选途径同上。
* 按设定的cron检查tracker状态，如发现种子的tracker状态有问题，将给该种子添加`TrackerError`的标签，方便筛选；如果tracker出错数量超过设定的阈值，给设定渠道发送通知。
* **一些辅助功能：批量修改tracker；检测指定文件夹下未做种的子文件夹/文件；生成做种文件清单；生成未做种文件清单；配合IYUU自动重新校验和自动恢复做种；指定设备上线时自动限速；多时段限速等等。**
* **如需要下载完成后自动触发EMBY/JELLYFIN扫描媒体库，触发ChineseSubFinder自动为刚刚下载完成的视频自动下载字幕，请按照** [**这里**](https://github.com/devome/dockerfiles/tree/master/qbittorrent/diy) **操作。**
* 日志输出到docker控制台，可从portainer查看。
* `python`为可选安装项，设置为`true`就自动安装。
* 体积小，默认中文UI，默认东八区时区。
* `iyuu`标签集成了[IYUUPlus](https://github.com/ledccn/IYUUPlus)，自动设置好下载器，减少IYUUPlus设置复杂程度

```
docker run -d --name=98t \
  --hostname qbittorrent \
  -v /home/qbittorrent:/data \
  -e PUID="$UID" \
  -e PGID="$GID" \
  -e WEBUI_PORT="8077" \
  -e BT_PORT="34569" \
  -p 8077:8077 \
  -p 34569:34569/tcp \
  -p 34569:34569/udp \
  --restart always \
  nevinee/qbittorrent
```

### Docker版qbt--linuxserver

{% embed url="https://hub.docker.com/r/linuxserver/qbittorrent" %}

{% embed url="https://github.com/linuxserver/docker-qbittorrent" %}

* 定期和及时的应用程序更新
* 简单的用户映射（PGID、PUID）
* 带有 s6 覆盖的自定义基础图像
* 在整个 LinuxServer.io 生态系统中使用通用层每周更新基本操作系统，以最大限度地减少空间使用、停机时间和带宽
* 定期安全更新
* 默认用户名/密码是`admin/adminadmin`

```
docker run -d \
  --name=qbittorrent \
  -e PUID=$UID \
  -e PGID=$GID \
  -e TZ=Asia/Shanghai \
  -e WEBUI_PORT=8070 \
  -p 8070:8070 \
  -p 51414:51414 \
  -p 51414:51414/udp \
  -v /home/qbt/config:/config \
  -v /home/qbt/downloads:/downloads \
  --restart unless-stopped \
  lscr.io/linuxserver/qbittorrent:latest
```

## Qbittorrent 性能参数校准

本文的目的在于让 Qbittorrent 实现最大化的上传下载速度，无论有没有公网IPv4。

### 硬件配置

> 不说硬件配置就说软件优化的都是流氓.png

主机：NUC10i5FNH(Intel i5-10210U (8) @ 4.200GHz)\
内存：32G DDR4 3200\
主数据盘：HC550 16t\
操作系统：Debian 11

### Qbittorrent版本

![](<../.gitbook/assets/image (5).png>)

此版本为我自己编译的版本，并没有修改源代码，只是指定了`Libtorrent 1.2`。

> 本文写作时`Libtorrent 2.0`有严重的内存泄露问题，因此使用1.2版本。

### 参数含义及校准建议

#### 连接设置

![](<../.gitbook/assets/image (10).png>)

#### 加密设置

1. 上面三项建议全部启用，PT、BT会自动管理，不会互相干扰。
2. `加密模式`建议设置为`强制加密`以规避ISP QOS以及被动流量监听。

> 缺点在于与极其老旧的BT客户端连线会失败。

![](<../.gitbook/assets/image (1) (1).png>)

#### Trackers设置

1. 最大并发 HTTP 发布建议值为`30`,这样Trackers更新不会停滞或消耗过多资源。
2. `总是向同级的所有 Tracker 汇报`需开启，否则Trackres会一直处于`未联系`状态。
3. Trackers List推荐`https://trackerslist.com/best.txt`

> best.txt相比all.txt，连接超时的Trackers更少，可以节省网络资源。

![](<../.gitbook/assets/image (2) (1).png>)

![](<../.gitbook/assets/image (7).png>)

![](<../.gitbook/assets/image (9) (1).png>)

#### 网络设置

网络接口建议设置为外网接口加所有地址，以免浪费网络资源。\


![](<../.gitbook/assets/image (4) (1).png>)

`与 peers 连接的服务类型（ToS）`建议设置为`0`以减少流量特征。\


![](<../.gitbook/assets/image (8).png>)

#### 安全设置

`服务器端请求伪造（SSRF）攻击缓解`与`禁止连接到特权端口上的 Peer`选项建议开启，防止连接到`1024`端口以下的peer，可有效防止DDOS与DHT投毒。\


![](<../.gitbook/assets/image (6).png>)

#### 缓存设置

磁盘缓存是必需的，因为机械硬盘的IO有限。

1. 异步 I/O 线程数填`4倍CPU线程数`，[来源](https://github.com/qbittorrent/qBittorrent/issues/11461)
2. 文件池大小建议设置为`100000`，这个设置等于`Linux Nofile limit`。
3. 磁盘缓存建议设置为`-1`，让程序自己管理。
4. 磁盘缓存过期时间间隔建议设置为`15`，即最快刷新。

![](<../.gitbook/assets/image (3).png>)

#### 连接限制

Qbittorrent默认有连接数限制，主机性能足够可以考虑全部关闭。\


![](<../.gitbook/assets/image (9).png>)

### 测试是否生效

建议进行对比测试，就能知道是否生效了。本文不作相关论述。

### 总结

Qbittorrent的默认设置其实相对保守，因此需要我们手动去校准以最大化性能。

