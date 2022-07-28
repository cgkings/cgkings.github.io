---
cover: >-
  https://images.unsplash.com/photo-1604346336129-b18c9fbcd1d3?crop=entropy&cs=tinysrgb&fm=jpg&ixid=MnwxOTcwMjR8MHwxfHNlYXJjaHw1fHxhcmlhfGVufDB8fHx8MTY1ODk4MDIwNg&ixlib=rb-1.2.1&q=80
coverY: 0
---

# Aria2

## DOCKER安装 aria2 pro

```
docker run -d \
    --name aria2-pro \
    --restart unless-stopped \
    --log-opt max-size=1m \
    --network host \
    -e PUID=$UID \
    -e PGID=$GID \
    -e RPC_SECRET=passwd123 \
    -e RPC_PORT=6800 \
    -e LISTEN_PORT=6888 \
    -v /root/aria2:/config \
    -v /home/dl:/downloads \
    -v /usr/bin/fclone:/usr/local/bin/rclone \
    -v /home/vps_sa/ajkins_sa:/home/vps_sa/ajkins_sa \
    -e SPECIAL_MODE=rclone \
    p3terx/aria2-pro
```

添加rclone.conf

```
cp ~/.config/rclone/rclone.conf ~/aria2
```

修改上传rclone remote

```
[ -z "$(grep "$rclone_remote" ~/aria2/script.conf)" ] && sed -i 's/drive-name=.*$/drive-name='$rclone_remote'/g' ~/aria2/script.conf
```

## Docker安装aria2ng

```
docker run -d \
    --name ariang \
    --restart unless-stopped \
    --log-opt max-size=1m \
    -p 6880:6880 \
    p3terx/ariang
```
