---
coverY: 0
---

# mktorrent

1.下载mktorrent。

github链接在这里&#x20;

git clone https://github.com/Rudde/mktorrent.git&#x20;

2.下载完成后进入到文件夹里面 例如：

cd mktorrent（如果是根目录的话）&#x20;

3\. make&#x20;

4\. make install&#x20;

5\. 默认安装目录位于/usr/local/bin，使用cd命令，从默认的/root路径切换到要制作成种子的文件上一级。 例如cd /Downloads&#x20;

6\. 制作种子命令为：&#x20;

mktorrent -v -p -l 22 -a tracker\_address -o name.torrent file\_name&#x20;

参数说明：&#x20;

tracker\_address为你要发布的网站的tracker。&#x20;

name.torrent为对生成torrent种子文件的命名，规则为：xxx.torrent。&#x20;

file\_name为你要做种的文件或文件夹。避免含有空格。

7\. 等待一会儿会提示做种完成，在当前目录下即可找到

**二、CentOS 7下，使用mktorrent制作种子**

1、安装mktorrent

`yum -y install epel-release`\
`yum -y install mktorrent screen`

2、mktorrent具体的用法：

`mktorrent -v -p -l 22 -a URL -o NAME.torrent FOLDER`

参数注释：

\-v：显示制作过程中的详细信息。\
\-p：将这个种子设置为私有，如果你是制作PT站的种子，那么这个参数是必加的。\
\-l：分块大小，建议使用22。\
\-a：指定Tracker服务器地址。\
\-o：定义制作的种子名字\
URL：修改为你打算制作种子所使用的Tracker服务器地址\
NAME：填写一个你的种子名字\
FOLDER：制作种子的目录

3、制作种子

a、进入到你打算制作种子目录的上级目录。\
比如我要分享 /root/tumblr2 下的文件，进入 /root

b、制作种子

`screen -S ctbt`\
`mktorrent -v -l 22 -a https://tp.m-team.cc/announce.php -o tumblr2.torrent tumblr2`
