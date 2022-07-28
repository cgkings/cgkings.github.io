---
cover: >-
  https://images.unsplash.com/photo-1501526029524-a8ea952b15be?crop=entropy&cs=tinysrgb&fm=jpg&ixid=MnwxOTcwMjR8MHwxfHNlYXJjaHw3fHx0cmFuc2ZlcnxlbnwwfHx8fDE2NTg5OTQwMzk&ixlib=rb-1.2.1&q=80
coverY: 0
---

# Vnstat流量统计

### 查看流量统计 Vnstat <a href="#h_479886858_29" id="h_479886858_29"></a>

#### 安装Vnstat <a href="#h_479886858_30" id="h_479886858_30"></a>

```yaml
sudo apt-get install vnstat #安装脚本
systemctl status vnstat #查看是否运行
ls /var/lib/vnstat/ #查看监控网口

vnstat -i enp2s0 -l #view live traffic, enp2s0为上面命令得到的监控网口
vnstat -h #查看过去24小时的流量
vnstat -d #查看过去30天的流量
vnstat -m #查看每个月的流量
vnstat -t #查看top10天的流量
```

![](https://pic3.zhimg.com/80/v2-c9fae3856e2a9fb11684f8c2af42afaa\_1440w.jpg)vnstat -h，因为是09h才安装的所以从10h时看才算正常， 06h还没跑完，流量统计不准
