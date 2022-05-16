# 部署攻略=》哪吒探针

哪吒探针原作者GitHub：[https://github.com/naiba/nezha](https://github.com/naiba/nezha)

![](<../.gitbook/assets/image (84).png>)

## 一、准备工作

### 1 VPS \* 1

VPS 需要放行`8008`、`5555`两个端口，这是默认的，如果你程序中改为其他的，防火墙放行相应的端口

### 2 域名

强烈建议用两个(子)域名做解析

第一个是面板的域名，套CDN比较方便

第二个仅仅解析到面板服务器的域名，用于客户端连接服务端使用，不可以套CDN（这个可以没有，但是不建议，如果直接用IP的话，迁移面板后会非常麻烦！）

### 3 GitHub上创建一个OAuth Apps

#### 3.1 创建一个OAuth Apps

先打开：[https://github.com/settings/developers](https://github.com/settings/developers)，然后点击New OAuth App按钮

![](<../.gitbook/assets/image (39).png>)

在这里其实有一个分岔口，也就是你是否要反代(和WEB服务器共用80口)、配置SSL，那么填写的内容、后面操作的先后顺序也不同。我就只说其中一个方法，免得把各位看官绕晕了，还请先往下看吧！

参考图片填写，记得端口也填写上去，不填域名填IP也行，如果你只是用IP访问的话

```
http://你的面板域名:8008
http://你的面板域名:8008/oauth2/callback
```

![](<../.gitbook/assets/image (68).png>)

#### 3.2 OAuth Apps的Client ID和Client secrets

创建好OAuth Apps后看图操作

![](<../.gitbook/assets/image (50).png>)

![](<../.gitbook/assets/image (57).png>)



## 二、部署服务器端(面板端)

哪吒面板开发者提供了一键脚本

{% tabs %}
{% tab title="国外VPS" %}
```
bash <(curl -Lso- https://raw.githubusercontent.com/naiba/nezha/master/script/install.sh)
```


{% endtab %}

{% tab title="国内VPS" %}
```
curl -L https://raw.sevencdn.com/naiba/nezha/master/script/install.sh -o nezha.sh && chmod +x nezha.sh
CN=true ./nezha.sh
```


{% endtab %}
{% endtabs %}

![](<../.gitbook/assets/image (55).png>)

安装面板端，输入1回车

接着就要输入前面记录下来的GitHub账号、OAuth Apps的Client ID、OAuth Apps的Client secrets了，按照提示输入即可

到这里面板服务算是完成了大部分了

可以访问`http://你的面板域名:8008`查看，用你的GitHub账号验证登录可以管理后台

## 三、部署客户端(被监控机器)

### 1 面板添加客户端，获取客户端密钥

用你的GitHub账号，通过`http://你的面板域名:8008`登录，管理后台

按图操作

![](<../.gitbook/assets/image (65).png>)

![](<../.gitbook/assets/image (46) (1).png>)

### 2 客户端安装脚本

{% tabs %}
{% tab title="国外VPS" %}
```
bash <(curl -Lso- https://raw.githubusercontent.com/naiba/nezha/master/script/install.sh)
```
{% endtab %}

{% tab title="国内VPS" %}
```
curl -L https://raw.sevencdn.com/naiba/nezha/master/script/install.sh -o nezha.sh && chmod +x nezha.sh
CN=true ./nezha.sh
```


{% endtab %}
{% endtabs %}

安装服务端，输入`8`回车

2.1 "解析到面板所在IP的域名"就是指本文中说的"第二个域名"，也可以直接填面板端VPS的IP(不建议)

2.2 输入前面记录下来的`密钥`了，

{% hint style="info" %}
如果添加多台客户端，就重复1，2，也就是先在面板后台添加，再在客户端VPS安装
{% endhint %}

## 四、主题自定义

![](<../.gitbook/assets/image (61).png>)

{% tabs %}
{% tab title="默认主题自定义" %}
方案挺多，可以多看看大佬们的探针源代码，自己改改，我的代码效果图如下：

![](<../.gitbook/assets/image (77).png>)

```
<style>
/* 屏幕适配 */
@media only screen and (min-width: 1200px) {
    .ui.container {
    width: 80% !important;
}
}

@media only screen and (max-width: 767px) {
    .ui.card>.content>.header:not(.ui), .ui.cards>.card>.content>.header:not(.ui) {
        margin-top: 0.4em !important;
    }
}

/* 整体图标 */
i.icon {
    color: #000;
    width: 1.2em !important;
}

/* 背景图片 */
body {
    content: " " !important;
    background: fixed !important;
    z-index: -1 !important;
    top: 0 !important;
    right: 0 !important;
    bottom: 0 !important;
    left: 0 !important;
    background-position: top !important;
    background-repeat: no-repeat !important;
    background-size: cover !important;
    background-image: url(https://uploadfile.huiyi8.com/2014/0708/20140708050046711.jpg) !important;
    font-family: Arial,Helvetica,sans-serif !important;
}

/* 导航栏 */
.ui.large.menu {
    border: 0 !important;
    border-radius: 0px !important;
    background-color: rgba(255, 255, 255, 55%) !important;
}

/* 首页按钮 */
.ui.menu .active.item {
    background-color: transparent !important;
}

/* 导航栏下拉框 */
.ui.dropdown .menu {
    border: 0 !important;
    border-radius: 0 !important;
    background-color: rgba(255, 255, 255, 80%) !important;
}

/* 登陆按钮 */
.nezha-primary-btn {
    background-color: transparent !important;
    color: #000 !important;
}

/* 大卡片 */
#app .ui.fluid.accordion {
    background-color: #fbfbfb26 !important;
    border-radius: 0.4rem !important;
}

/* 小卡片 */
.ui.four.cards>.card {
    border-radius: 0.6rem !important;
    background-color: #fafafaa3 !important;
}

.status.cards .wide.column {
    padding-top: 0 !important;
    padding-bottom: 0 !important;
    height: 3.3rem !important;
}

.status.cards .three.wide.column {
    padding-right: 0rem !important;
}

.status.cards .wide.column:nth-child(1) {
    margin-top: 2rem !important;
}

.status.cards .wide.column:nth-child(2) {
    margin-top: 2rem !important;
}

.status.cards .description {
    padding-bottom: 0 !important;
}

/* 小鸡名 */
.status.cards .flag {
    margin-right: 0.5rem !important;
}

/* 弹出卡片图标 */
.status.cards .header > .info.icon {
    margin-right: 0 !important;
}

.nezha-secondary-font {
    color: #21ba45 !important;
}

/* 进度条 */
.ui.progress {
    border-radius: 50rem !important;
}

.ui.progress .bar {
    min-width: 1.8em !important;
    border-radius: 15px !important;
    line-height: 1.65em !important;
}

.ui.fine.progress> .bar {
    background-color: #21ba45 !important;
}

.ui.progress> .bar {
    background-color: #000 !important;
}

.ui.progress.fine .bar {
    background-color: #21ba45 !important;
}

.ui.progress.warning .bar {
    background-color: #ff9800 !important;
}

.ui.progress.error .bar {
    background-color: #e41e10 !important;
}

.ui.progress.offline .bar {
    background-color: #000 !important;
}

/* 上传下载 */
.status.cards .outline.icon {
    margin-right: 1px !important;
}

i.arrow.alternate.circle.down.outline.icon {
    color: #21ba45 !important;
}

i.arrow.alternate.circle.up.outline.icon {
    color: red !important;
}

/* 弹出卡片小箭头 */
.ui.right.center.popup {
    margin: -3px 0 0 0.914286em !important;
    -webkit-transform-origin: left 50% !important;
    transform-origin: left 50% !important;
}

.ui.bottom.left.popup {
    margin-left: 1px !important;
    margin-top: 3px !important;
}

.ui.top.left.popup {
    margin-left: 0 !important;
    margin-bottom: 10px !important;
}

.ui.top.right.popup {
    margin-right: 0 !important;
    margin-bottom: 8px !important;
}

.ui.left.center.popup {
    margin: -3px .91428571em 0 0 !important;
    -webkit-transform-origin: right 50% !important;
    transform-origin: right 50% !important;
}

.ui.right.center.popup:before,
.ui.left.center.popup:before {
    border: 0px solid #fafafaeb !important;
    background: #fafafaeb !important;
}

.ui.top.popup:before {
    border-color: #fafafaeb transparent transparent !important;
}

.ui.popup:before {
    border-color: #fafafaeb transparent transparent !important;
}

.ui.bottom.left.popup:before {
    border-radius: 0 !important;
    border: 1px solid transparent !important;
    border-color: #fafafaeb transparent transparent !important;
    background: #fafafaeb !important;
    -webkit-box-shadow: 0px 0px 0 0 #fafafaeb !important;
    box-shadow: 0px 0px 0 0 #fafafaeb !important;
    -webkit-tap-highlight-color: rgba(0,0,0,0) !important;
}

.ui.bottom.right.popup:before {
    border-radius: 0 !important;
    border: 1px solid transparent !important;
    border-color: #fafafaeb transparent transparent !important;
    background: #fafafaeb !important
    -webkit-box-shadow: 0px 0px 0 0 #fafafaeb !important;
    box-shadow: 0px 0px 0 0 #fafafaeb !important;
    -webkit-tap-highlight-color: rgba(0,0,0,0) !important;
}

.ui.top.left.popup:before {
    border-radius: 0 !important;
    border: 1px solid transparent !important;
    border-color: #fafafaeb transparent transparent !important;
    background: #fafafaeb !important;
    -webkit-box-shadow: 0px 0px 0 0 #fafafaeb !important;
    box-shadow: 0px 0px 0 0 #fafafaeb !important;
    -webkit-tap-highlight-color: rgba(0,0,0,0) !important;
}

.ui.top.right.popup:before {
    border-radius: 0 !important;
    border: 1px solid transparent !important;
    border-color: #fafafaeb transparent transparent !important;
    background: #fafafaeb !important;
    -webkit-box-shadow: 0px 0px 0 0 #fafafaeb !important;
    box-shadow: 0px 0px 0 0 #fafafaeb !important;
    -webkit-tap-highlight-color: rgba(0,0,0,0) !important;
}

.ui.left.center.popup:before {
    border-radius: 0 !important;
    border: 1px solid transparent !important;
    border-color: #fafafaeb transparent transparent !important;
    background: #fafafaeb !important;
    -webkit-box-shadow: 0px 0px 0 0 #fafafaeb !important;
    box-shadow: 0px 0px 0 0 #fafafaeb !important;
    -webkit-tap-highlight-color: rgba(0,0,0,0) !important;
}

/* 弹出卡片 */
.status.cards .ui.content.popup {
    min-width: 20rem !important;
    line-height: 2rem !important;
    border-radius: 5px !important;
    border: 1px solid transparent !important;
    background-color: #fafafaeb !important;
    font-family: Arial,Helvetica,sans-serif !important;
}

.ui.content {
    margin: 0 !important;
    padding: 1em !important;
}

/* 服务页 */
.ui.table {
    background: RGB(225,225,225,0.6) !important;
}

.ui.table thead th {
    background: transparent !important;
}

/* 服务页进度条 */
.service-status .good {
    background-color: #21ba45 !important;
}

.service-status .danger {
    background-color: red !important;
}

.service-status .warning {
    background-color: orange !important;
}

/* 版权 */
.ui.inverted.segment, .ui.primary.inverted.segment {
    color: #000 !important;
    font-weight: bold !important;
    background-color: #fafafaa3 !important;
}
</style>

<!--Logo和版权-->
<script>
window.onload = function(){
var avatar=document.querySelector(".item img")
var footer=document.querySelector("div.is-size-7")
footer.innerHTML="Copyright © 2021 Server Status| Powered by 王大锤 <br/>友情链接: <a href='https://status.zmylove.ml' target='_blank'>Do Do</a> | <a href='https://tz.36rocky.com' target='_blank'>Tel Me</a> | <a href='https://status.wuaicc.com' target='_blank'>adminroot</a> | <a href='http://showoff.1895898.xyz' target='_blank'>ShuHang</a>"
footer.style.visibility="visible"
avatar.src="https://z3.ax1x.com/2021/10/20/507NVA.gif"
avatar.style.visibility="visible"
}
</script>
```
{% endtab %}

{% tab title="cokemin hotaru主题自定义" %}
这个是直接抄的，背景图更换，自己找找替换就好，效果图如下：

留空

```
// Some code
```
{% endtab %}
{% endtabs %}

## 五、反代、SSL、CDN

方法很多，本部分留空



## 六、一键关闭ssh功能

* \--report-delay 系统信息上报的间隔，默认为 1 秒，可以设置为 3 来进一步降低 agent 端系统资源占用（配置区间 1-4）
* \--skip-conn 不监控连接数，机场/连接密集型机器推荐设置，不然比较占 CPU(shirou/gopsutil/issues#220)
* \--skip-procs 不监控进程数，也可以降低 agent 占用
* \--disable-auto-update 禁止 Agent 自动更新（安全特性）
* \--disable-command-execute 禁止在 Agent 机器上执行定时任务、打开在线终端（安全特性）

编辑/etc/systemd/system/nezha-agent.service，以下命令仅仅是添加了

```
sed -i '/ExecStart/ s/$/ --skip-conn --disable-command-execute/' /etc/systemd/system/nezha-agent.service && systemctl daemon-reload && bash <(curl -Lso- https://raw.githubusercontent.com/naiba/nezha/master/script/install.sh) restart_agent
```



## 常见问题

#### 1. 国内服务器出现curl: (7) Failed to connect to raw.githubusercontent.com

```
echo 199.232.68.133 raw.githubusercontent.com >> /etc/hosts
```

#### 2. 修改服务器备注后变成"离线"久不恢复

在安装客户端(被监控)的服务器上执行`systemctl restart nezha-agent`重启程序

#### 启动nezha-agent还是一直显示离线

检查配置有没有写对`cat /etc/systemd/system/nezha-agent.service | grep ExecStart`

