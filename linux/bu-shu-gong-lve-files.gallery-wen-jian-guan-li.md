# 部署攻略=>files.gallery文件管理

[files.gallery官网](https://www.files.gallery)

pre-step:下载并配置index.php

```
wget -qP /home/mydisk https://cdn.jsdelivr.net/npm/files.photo.gallery/index.php
```

配置见[官方配置文档](https://www.files.gallery/docs/config/)，默认仅可浏览下载

step1：搭建php环境

```
sudo apt update && sudo apt upgrade
```

```
sudo apt install php7.4-cgi php7.4-fpm php7.4-curl php7.4-gd php7.4-mbstring php7.4-xml
```

```
sudo systemctl start php7.4-fpm.service && sudo systemctl enable php7.4-fpm.service
```

step2:安装caddy

```
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo tee /etc/apt/trusted.gpg.d/caddy-stable.asc
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
sudo systemctl start caddy
sudo systemctl enable caddy
```

step3:首次执行Index.php,生成`_files`文件夹，用于存储缓存、配置和可选的包含文件。

```
cd /home/mydisk && php index.php
```

```
sudo chown caddy:caddy -R /home/mydisk && sudo chmod 755 -R /home/mydisk
```

step4:caddy2发布index.php

编辑添加如下内容到`/etc/caddy/Caddyfile`

```
king.gq {
    log {
        output file /etc/caddy/caddy.log
    }
    root * /home/mydisk
    encode zstd gzip
    file_server
    header Access-Control-Allow-Origin *
    php_fastcgi unix//run/php/php7.4-fpm.sock
}
```

```
sudo systemctl restart php7.4-fpm.service && sudo systemctl restart caddy
```

访问[https://king.gq/index.php](https://x-king.gq/index.php),搭建成功！



访问[https://king.gq/index.php?check=1](https://king.gq/index.php?check=1)，可以测试您的 PHP 和服务器的各种功能

![](<../.gitbook/assets/image (75).png>)

最后

修改PHP上传限制，upload 128M,post 1024M,max time 600,memory\_limit 256M

```
max_execution_time
```

```
max_input_time
```

```
memory_limit
```

```
post_max_size
```

```
upload_max_filesize
```



