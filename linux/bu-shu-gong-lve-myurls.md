---
description: ⚠️本教程基于掌握基础的服务器操作与代码修改技能之上
---

# MyUrls 短链服务部署

​[MyUrls](https://github.com/CareyWang/MyUrls) 是基于 golang1.13 与 Redis 实现的本地短链接服务，用于缩短请求链接与短链接还原

![](https://agwa5783.gitbook.io/\~/files/v0/b/gitbook-28427.appspot.com/o/assets%2F-M1inSzG-42ledaB5Umy%2F-M3tn2Lqc1JFZqkMMyXe%2F-M3tnpxSjljlrSDhzwiU%2FMyUrls.png?alt=media\&token=a11ef19a-70cd-4fb4-af54-37c1f444d3b3)

## 一、前期准备：

[MyURLs – Github 作者主页](https://cnboy.org/go.php?aHR0cHM6Ly9naXRodWIuY29tL0NhcmV5V2FuZy9NeVVybHM=) ，上面有docker 和 docker-compose 的安装代码，也可以前往 [Release](https://cnboy.org/go.php?aHR0cHM6Ly9naXRodWIuY29tL0NhcmV5V2FuZy9NeVVybHMvcmVsZWFzZXM=) 下载对应平台可执行文件，本教程使用可执行文件。

1、一台 VPS，普通配置即可。

2、一个域名，用于访问服务。（请提前使用 Cloudflare 做好域名解析以备用）

⚠️ 本文以 suo.yt 代表短链服务访问域名，牵扯到域名的操作请自行将相关参数修改为自己的域名。

⚠️ 请 Fork Sub-Web 前端源码后修改 /src/views/Subconverter.vue 文件中第 223 行「const shortUrlBackend = "https://suo.yt/short";」并保存。

⚠️ 建议开启 Cloudflare 的 CDN，避免 VPS 的 IP 被攻击。

⚠️ 部署完成后域名 SSL/TLS 加密模式必须为 Full，否则前端跨域配置可能会失效。

## 二、必备环境和软件 <a href="#1-dependencies" id="1-dependencies"></a>

这里以debian 11系统为例，其它系统所使用的安装命令请自行研究：

### 1 执行以下命令安装 Redis 服务：

```
sudo apt-get update
sudo apt-get install redis-server -y
```

{% hint style="info" %}
本服务依赖于 Redis 提供长短链接映射关系存储，你需要本地安装 Redis 服务来保证短链接服务的正常运行。
{% endhint %}

### 2 安装caddy2

可以使用本人写的一键caddy2脚本，初始化即安装caddy2

```
bash <(curl -sL git.io/cg_caddy2)
```

### 3 安装docker和docker-compose <a href="#2-an-zhuang-pm2" id="2-an-zhuang-pm2"></a>

同样可以使用本人写的装机一键脚本，基本的环境都会安装的，具体可以--help看帮助

```
bash <(curl -sL git.io/cg_1key_dd) --basic
```

## 三、安装后端 <a href="#2-an-zhuang-pm2" id="2-an-zhuang-pm2"></a>

1 执行以下命令

```
wget -q https://github.com/CareyWang/MyUrls/releases/download/v1.10/linux-amd64.tar.gz -O /home/myurls.tar --no-check-certificate
tar -xvf /home/myurls.tar -C /home && rm -f /home/myurls.tar
chmod -R 755 /home/myurls && chown -R caddy.caddy /home/myurls
```

2 编辑/home/myurls/public/index.html文件，将const backend这里改成自己的域名

![](<../.gitbook/assets/image (62).png>)

3 新建文件 /etc/systemd/system/myurls.service，写入内容然后保存：（ExecStart 、WorkingDirectory两个参数后面的路径改成自己的网站根目录，以及将 example.com 替换为自己的网址）

```
[Unit]
Description=A API For Short URL Convert
After=network.target

[Service]
Type=simple
ExecStart=/home/myurls/linux-amd64-myurls.service -domain example.com
WorkingDirectory=/home/myurls
Restart=always
RestartSec=10

[Install]
WantedBy=multi-user.target
```

4 使用以下命令启动后端服务

```
systemctl daemon-reload && systemctl enable myurls && systemctl start myurls
```

## 四、配置前端 <a href="#3-xia-zai-bing-yun-hang-myurls" id="3-xia-zai-bing-yun-hang-myurls"></a>

编辑`/etc/caddy/Caddyfile`,也可以使用该命令添加

```
cat << "EOF" >> /etc/caddy/Caddyfile
xgo.gq {
	encode gzip
	root * /home/myurls/public
	file_server
	reverse_proxy localhost:8002
	header Access-Control-Allow-Origin *
}
EOF
```

使用以下命令重启caddy

```
systemctl reload caddy
```

