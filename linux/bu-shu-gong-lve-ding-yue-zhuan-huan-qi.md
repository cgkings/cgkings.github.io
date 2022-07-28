# 部署攻略=》订阅转换器

## 一、准备工作

{% hint style="info" %}
#### [前端项目地址](https://github.com/CareyWang/sub-web)

#### [后端项目地址](https://github.com/tindy2013/subconverter)
{% endhint %}

### 1、在线平台的准备

* VPS一台并装好主流的系统（作者演示用CentOS）
* 解析两个域名：一个用于前端，一个用于后端）

&#x20;       **前端** 我们使用 sub.cgking.com 进行解析

&#x20;       **后端** 我们使用 suc.cgking.com 进行解析

PS：若是为了你的服务器安全，建议使用CDN！

### 2、本地平台的准备

* PVE（Esxi）下创建的虚拟机VPS

## 二、SubConverter后端搭建

1、docker部署

```
docker run -d --restart=always -p 25500:25500 tindy2013/subconverter:latest
```

2、验证成功

```
curl http://localhost:25500/version
```

&#x20;（正常的话，会返回下面的信息）

`subconverter v0.7.2-a24cb7c backend`

## 三、搭建Sub-Web前端

### 1、配置环境

更新系统并安装 Node 与 Yarn

依次运行下面四行代码，若是 Debian/Ubuntu 系统，请自行替换下面前两行命令中的 `yum` 为 `apt`

```
apt update -y && apt install -y nodejs npm && npm install -g yarn
```

命令执行完毕以后，请运行查询 Node 与 Yarn 是否安装成功，若是成功会返回版本号

```
node -v
yarn --version
```

### 2、下载并安装 Sub-Web

运行如下代码：

```
cd /home && git clone https://github.com/cgkings/sub-web-modify.git && chmod -R 755 sub-web-modify && chown -R caddy.caddy sub-web-modify && cd sub-web-modify && yarn install
```

如果yarn install出错，可以尝试运行`yarn config set ignore-engines true`

```
yarn serve
```

通过`yarn serve`命令，可以浏览器访问 http://服务器ip:8080/ 进行前端 sub-web 预览调试

一般是通过修改`/home/sub-web-modify/src/views/Subconverter.vue`文件来实现调试

我自己的`Subconverter.vue`文件有备份，就不说具体修改内容了，想改的，参考几个大佬的转换的地址源代码改吧，抄袭是代码学习的捷径

也可以看这个教程[https://www.back2me.cn/skills/clash.html](https://www.back2me.cn/skills/clash.html)

{% hint style="info" %}
[https://subcon.dlj.tf/](https://subcon.dlj.tf/)

[https://acl4ssr-sub.github.io/](https://acl4ssr-sub.github.io/)  ac4ssr大佬

[https://subcon.dlj.tf/](https://subcon.dlj.tf/)   TindyX大佬

[https://bianyuan.xyz/](https://bianyuan.xyz/)
{% endhint %}

CTRL+C ，退出当前调试，然后执行下面的命令进行打包：

```
cd /home/sub-web-modify && yarn build && chown -R caddy.caddy /home/sub-web-modify
```

执行以下打包命令，在 `/home/sub-web-modify` 下面会生成一个 `dist` 目录，这个目录即为网页的发布目录。

本教程使用caddy发布，故需要给予权限，尽量不要放在/root下

```
mkdir -p /var/www/html && cp -r /home/sub-web-modify/dist/* /var/www/html && chmod -R 755 /var/www/html/ && chown -R caddy.caddy /var/www/html
```

### 3、caddy部署网站

编辑`/etc/caddy/Caddyfile`，然后`systemctl reload caddy`

```
cat << "EOF" >> /etc/caddy/Caddyfile
sub.cgking.gq {
	encode gzip
	file_server
	root * /home/sub-web-modify/dist
}
EOF
```

## 后记

到这里，我们也就搭建完毕了，大家可以在前端订阅自己的SSR/V2RAY/TROJAN机场链接或是自建的的节点链接进行转换了。















