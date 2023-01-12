# cloudflare自选IP+caddy2反代

* **Caddy介绍**

Caddy 是一个用 Golang 写的高效 Web Server，相比 Nginx，它的配置和使用要简单很多，并且支持自动化 HTTPS。并且 Caddy支持自动签订[Let’s Encrypt SSL](https://link.juejin.cn/?target=http%3A%2F%2F51.ruyo.net%2Fp%2F3163.html)证书，什么都不需要你管，只需要提供一个邮箱，剩下的他会自己申请、配置和续约 SSL证书。

* **Caddy源的添加及安装**

在Caddy第一个版本时代，安装Caddy需要下载安装包，但是在**caddy2**时代，**官方针对**

**Debian, Ubuntu已经提供了可用的源**。

1\)使用如下语句为Debian添加源：

```
echo "deb [trusted=yes] https://apt.fury.io/caddy/ /" \
    | sudo tee -a /etc/apt/sources.list.d/caddy-fury.list
```

2\)使用apt指令安装Caddy2:

```
sudo apt update 
sudo apt install caddy
```

安装之后，caddy会自动注册为一个systemd服务，同时注册一个名为caddy的nologin不可登录帐户。

* **Caddy反向代理配置文件编写**

新版的Caddy2支持使用JSON或者Caddyfile进行配置，由于Caddyfile比较简单，我们沿用Caddyfile方式。一般caddy安装好后，系统使会在/etc目录自动生成一个名为caddy的文件夹，里面包含一个默认的Caddyfile配置（如果没有，可以用touch命令自行创建）。

1\)使用编辑器打开，以nano为例：

```
sudo nano /etc/caddy/Caddyfile
```

2\)打开后，键入以下内容：

```
#你的域名
xxx.xxxx.com  

#压缩方式
encode gzip

#tls安全连接,其中xxxx@xxxx.com为你邮箱的地址，caddy据此来自动申请SSL证书
tls  xxxx@xxxx.com {    
   on_demand 
}  

#配置日志文件
log {      
    output file /var/log/caddy.log 
}

#配置服务器页面文件根目录,一般默认为该目录
 root * /usr/share/caddy

# 打开静态文件服务功能
file_server

#反向代理配置,其中/dir为反向代理的目录前缀
#也即只有访问前缀为xxx.xxxx.com/dir时才会进行反代，可依据自身情况调整或删除
#XXXXX为本地反向代理的端口号
reverse_proxy /dir {
   to localhost:XXXXX
}
```

你需要依据自身服务器的情况填入正确的域名、邮箱地址、以及本地反向代理目录前缀和端口号。完成配置之后，还有一个**重要的步骤便是将相应文件或目录的权限授予caddy，没有这一步时，caddy启动会由于权限问题而报错。**

3\)为caddy进行访问授权

访问授权一般使用chown或chmod。一般使用chmod改变文件读写属性即可。需要授权的文件有：/etc/caddy/Caddyfile和/var/log/caddy.log

```
sudo chmod o+r  /etc/caddy/Caddyfile
sudo chmod o+w  /var/log/caddy.log
```

通过以上两个命令，授予other用户caddy对/etc/caddy/Caddyfile的读权限和对/var/log/caddy.log的写权限。

一切完毕后，重新启动caddy即可。

```
sudo systemctl restart caddy
```

**\*注意，如果debian打开了防火墙，还应该关闭防火墙对433端口的限制。**

Caddy会自动签署SSL证书，证书存放在

/var/lib/caddy/.local/share/caddy/certificates/acme-v02.api.letsencrypt.org-directory

#### **4、Caddy的操作命令**

```
systemctl enable caddy.service   # 开机启动
systemctl start caddy.service    # 启动
systemctl stop caddy.service     # 停止
systemctl restart caddy.service  # 重启
systemctl status caddy.service   # 查看状态
systemctl daemon-reload          # 重载配置
```

此时，caddy已经可以愉快的奔跑了，快到浏览器中键入自己的域名，测试一下caddy欢迎页吧！
