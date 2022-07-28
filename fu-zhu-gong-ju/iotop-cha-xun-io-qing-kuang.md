---
cover: >-
  https://images.unsplash.com/photo-1484662020986-75935d2ebc66?crop=entropy&cs=tinysrgb&fm=jpg&ixid=MnwxOTcwMjR8MHwxfHNlYXJjaHw4fHxoZGR8ZW58MHx8fHwxNjU4OTk0MTI5&ixlib=rb-1.2.1&q=80
coverY: 0
---

# IOTOP查询IO情况

iotop命令是一个用来监视磁盘I/O使用状况的top类工具。iotop具有与top相似的UI，其中包括PID、用户、I/O、进程等相关信息。

Linux下的IO统计工具如iostat，nmon等大多数是只能统计到per设备的读写情况，如果你想知道每个进程是如何使用IO的就比较麻烦，使用iotop命令可以很方便的查看。

**语法格式：**iotop \[参数]

**常用参数：**

| -o      | 只显示有io操作的进程        |
| ------- | ------------------ |
| -b      | 批量显示，无交互，主要用作记录到文件 |
| -n NUM  | 显示NUM次，主要用于非交互式模式  |
| -d SEC  | 间隔SEC秒显示一次         |
| -p PID  | 监控的进程pid           |
| -u USER | 监控的进程用户            |

**参考实例**

使用-o参数只显示IO操作进程：

```
[root@linuxcool ~]# iotop -o
```

使用-b参数批量显示，无交互：

```
[root@linuxcool ~]# iotop -b
```

使用-u参数显示root用户的IO进程：

```
[root@linuxcool ~]# iotop -u root
```
