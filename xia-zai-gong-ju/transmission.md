---
cover: >-
  https://images.unsplash.com/photo-1455920013194-211c606abacb?crop=entropy&cs=tinysrgb&fm=jpg&ixid=MnwxOTcwMjR8MHwxfHNlYXJjaHwyfHx0cmFuc21pc3Npb258ZW58MHx8fHwxNjU4OTgwMDgx&ixlib=rb-1.2.1&q=80
coverY: 0
---

# Transmission

Docker版Tr--helloz修改版

```
docker run -d --name="transmission" \
  -p 51413:51413 \
  -p 51413:51413/udp \
  -p 9091:9091 \
  -e USERNAME=admin \
  -e PASSWORD=adminadmin \
  -v /data/downloads:/home/tr/downloads \
  -v /data/transmission:/root/.config/transmission-daemon \
  --restart=always \
  helloz/transmission
```

