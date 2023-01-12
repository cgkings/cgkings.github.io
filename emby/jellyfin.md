# jellyfin

## 一、常用插件

## 二、备份还原

## 三、常见问题

### 1、首页中文显示方框

{% tabs %}
{% tab title="linux" %}
```
apt update
apt install fonts-noto-cjk-extra
reboot
#注意：安装完字体，必须重启生效，然后首页删除已有的封面图，重新扫描生成即可
```
{% endtab %}
{% endtabs %}

![](<../.gitbook/assets/image (27).png>)

### 2、字幕中文显示方框

在 Jellyfin 挂载的目录中，新建一个文件夹 fonts。我放在了挂载的 config 目录中。

首先下载 [Noto Sans SC woff2 字体包](https://github.com/CodePlayer/webfont-noto/raw/master/release/NotoSansCJKsc-hinted-standard.zip)。

解压后找到 `NotoSansCJKsc-Medium.woff2` 这个文件，将其复制到上面说的 fonts 目录中。

然后在控制台-播放中设置启用备用字体：

![](<../.gitbook/assets/image (30).png>)
