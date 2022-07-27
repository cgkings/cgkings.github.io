# Page 1

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

