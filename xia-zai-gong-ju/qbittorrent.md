# Qbittorrent

\#宿主机root用户环境下使用例子(非官方,仅供参考)

\#创建要挂载的目录,此时该目录属root用户和root组&#x20;

mkdir -p /home/qbt/config

\#创建docker用户(默认会顺带新建同名Group)&#x20;

useradd dockeruser&#x20;

\#修改文件夹归属(R代表递归操作,文件夹下的也一并修改)&#x20;

chown -R dockeruser:dockeruser /opt/letsencrypt&#x20;

\#查看dockeruser的用户id和群id&#x20;

id dockeruser

## Docker版qbt--helloz

[https://hub.docker.com/r/helloz/qbittorrent](https://hub.docker.com/r/helloz/qbittorrent)

[https://github.com/helloxz/qbittorrent](https://github.com/helloxz/qbittorrent)

* 基于Debian 10 x64镜像制作
* qBittorrent版本为`v4.1.5`
* 已内置GeoIP数据库
* 默认设置为简体中文界面
* 默认用户名/密码是`admin/adminadmin`

```
docker run -d \
  --name=qbittorrent \
  -p 51414:51414 \
  -p 51414:51414/udp \
  -p 18080:18080 \
  -v /data/qbittorrent/config:/etc/qBittorrent \
  -v /data/qbittorrent/downloads:/home/downloads \
  --restart unless-stopped \
  helloz/qbittorrent
```

大部分设置均可通过WEB界面直接修改，若需要修改部分特殊配置，可自行修改配置文件夹<mark style="color:orange;">`/你的挂载路径/config/qBittorrent.conf`</mark>，修改后重启下容器`docker restart qbittorrent`

## Docker版qbt--linuxserver

[https://hub.docker.com/r/linuxserver/qbittorrent](https://hub.docker.com/r/linuxserver/qbittorrent)

[https://github.com/linuxserver/docker-qbittorrent](https://github.com/linuxserver/docker-qbittorrent)

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





