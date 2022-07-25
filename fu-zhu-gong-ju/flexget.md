# Flexget

### Flexget配置 <a href="#h_479886858_8" id="h_479886858_8"></a>

#### 安装flexget <a href="#h_479886858_9" id="h_479886858_9"></a>

```yaml
pip3 install flexget #安装flexget
which flexget #查询 FlexGet 安装位置，例如位置为 /usr/local/bin/flexget
flexget -V #查看flexget版本
```

#### 配置Flexget目录及config文件 <a href="#h_479886858_10" id="h_479886858_10"></a>

```yaml
mkdir /root/.flexget #创建.flexget目录
nano /root/.flexget/config.yml # 创建及更改config.yml内容
mkdir ~/.flexget/plugins && cd ~/.flexget/plugins && wget https://github.com/Juszoe/flexget-nexusphp/releases/download/v1.4/nexusphp.py #创建plugins目录，下载nexusphp插件
flexget web passwd Peterpt@pt311%s #设置web ui 后台密码 为 Peterpt@pt311%s
flexget daemon start --daemonize #开启 webui 后台运行
```

实际情况如图

![](https://pic3.zhimg.com/80/v2-3dc140a63824e9e535adc37a8874532a\_1440w.jpg)

webui运行后，直接登录浏览器，3539为我设置的端口，前面为盒子的ipv4地址

![](https://pic1.zhimg.com/80/v2-907921e316a34f417883afe6c028a654\_1440w.jpg)

#### Config文件样本 <a href="#h_479886858_11" id="h_479886858_11"></a>

config.yml可以登录web ui后台去编辑也可以用notepad++打开.flexget文件夹下面的config.yml来编辑。这里给出个简单的样本

```yaml
web_server:
  bind: 0.0.0.0
  port: 3539
  web_ui: yes

schedules:
  - tasks: [Test]
    interval:
      minutes: 5 #每5分钟R一次种子

templates:
  qb:
    qbittorrent:
      path: /home/你设置的username/qbittorrent/Downloads/
      host: 你盒子的Ipv4地址
      port: 8080
      username: 用户名
      password: 密码

# 任务
tasks:
  Test:
    rss: 站点RSS链接
      url: 
      other_fields: [link]
    nexusphp:
      cookie: '站点的cookie'
      hr: no   
    content_size: #这里我筛选20G到300G的种子
      min: 20000
      max: 307200 
      strict: no 
    template: qb
    qbittorrent:
      label: Test # QB里设置category为Test
      maxupspeed: 100000 #设置限速100MB/s 针对有上传限速的网站
```

编辑完后在webui后台选择Tasks→Execute，这里先进行一次learn, 来筛选掉符合条件的旧种

![](https://pic2.zhimg.com/80/v2-b5071a0295239a1a843e625826729709\_1440w.jpg)

#### flexget其他指令 <a href="#h_479886858_12" id="h_479886858_12"></a>

\


![](https://pic4.zhimg.com/80/v2-f360a487063b1551781adfa33122642b\_1440w.jpg)
