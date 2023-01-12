# 甲骨文云二三事

## 简介 <a href="#_0" id="_0"></a>

Oracle Cloud即大名鼎鼎的甲骨文旗下新设立的云服务平台。目前新注册账号可以在自己所选择的初始区域（注册时选择的区域）建立两台永久免费主机，此外还有300美金的试用额度可以在未来一个月内持续使用。本文主要是提供以下如何通过DD网络远程安装的方式来更新Oracle Cloud的系统。

注意：始终免费服务仅限于注册时所选区域标注始终免费的项目，余下的没标注的会扣300美金的试用额度。但只要没有选择升级到付费账号，就不会对信用卡扣款。

## 一、账号注册的那些事 <a href="#_3" id="_3"></a>

### 1、注册教程

Oracle Cloud免费注册：[https://www.oracle.com/cn/cloud/free/](https://www.oracle.com/cn/cloud/free/)

具体申请方法，请参考前一篇有关Oracle Cloud注册申请教程：

Oracle Cloud Free永久免费账户申请方法

### 2、区域介绍

留空

## 二、创建实例的那些事

### 甲骨文云的免费政策

Oracle 云免费套餐包含哪些内容？

2 个自治数据库，每个 20 GB

AMD 和 Arm 计算 VM

200 GB 总块存储

10 GB 对象存储

每月 10 TB 出站数据传输

10 多项永远免费服务

30 天的 300 美元免费积分，甚至更多

{% hint style="info" %}
ARM的免费策略和AMD的不一样

ARM每个月会赠送一定的CPU、内存使用时间，这个免费额度正好等于4C 24G一个月

所以你可以按照这个配置来自由组合：

4C 24G \* 1

2C 12G \* 2

1C 6G \* 4

1C 6G \* 2 + 2C 12G \*1
{% endhint %}

### 1、登录账号

{% tabs %}
{% tab title="方法一" %}
开通成功后，最好是通过官方给你的邮件里面的链接来登陆，

![](https://pic3.zhimg.com/80/v2-0215ddc513f927edff72278ea9aabdf6\_720w.jpg) ![](<../.gitbook/assets/image (60).png>)
{% endtab %}

{% tab title="方法二" %}
一步直达登录入口，https://cloud.oracle.com/?tenant=租户名

{% hint style="info" %}
租户名即你的登录邮箱的前缀，比如123@outlook.com,租户名为123
{% endhint %}

![](<../.gitbook/assets/image (38).png>)
{% endtab %}
{% endtabs %}

![](<../.gitbook/assets/image (40).png>)

中文总是亲切的！

### 2、创建实例

#### step1:点击“创建VM实例”

![](<../.gitbook/assets/image (78).png>)

#### step2:创建页面编辑配置

![](<../.gitbook/assets/image (71).png>)

part1：名称随便填，可根据需要自定义

part2：位置确认是免费的，一般不用改

part3：此部分为选系统和配置(arm/amd)

注：如需dd系统，建议Ubuntu16或Ubuntu18！

![](<../.gitbook/assets/image (53).png>)

part4：甲骨文云默认是SSH密钥登陆，所以，下载保存私钥登录备用。

{% hint style="info" %}
推荐用xshell或者别的MJJ爱用啥都可以

用户名：ubuntu（甲骨文CentOS的用户名为 `opc` ,Ubuntu系统的用户名为`ubuntu`）

密码无，直接刚才下载的文件密匙登录即可（没下载就推倒重来\~）

具体如何导入私钥登录,请自行百度/谷歌！
{% endhint %}

![](<../.gitbook/assets/image (73).png>)

part5:设置硬盘大小

这边现在默认大小是`46.6GB`，如果要自定义大小的话，最少是`50GB`，2台实例总共免费只能挂200g硬盘。

part6：点击创建

### 3、实例列表地址

实例列表地址：[https://cloud.oracle.com/compute/instanc](https://cloud.oracle.com/compute/instances)

![](<../.gitbook/assets/image (67).png>)

### 4、添加IPV6网络

如下图设置路径为：

1网络--2虚拟云网络--3自己的虚拟云网络名称--4CIDR块--5添加IPV6 CIDR块--6子网--7编辑--8启用IPV6 CIDR块--9补齐CIDR块内容(根据示例填就好)--10路由表--11添加路由规则(跟着填就行)--12安全列表-添加IPV6的出入站规则--13去“计算/实例/实例详细信息/附加的 VNIC/VNIC 详细信息/IPv6 地址”分配IPV6地址

ok，添加完成！

![](<../.gitbook/assets/image (47).png>)

![](<../.gitbook/assets/image (76).png>)

![](<../.gitbook/assets/image (72).png>)

![](<../.gitbook/assets/image (79).png>)

![](<../.gitbook/assets/image (63).png>)

![](<../.gitbook/assets/image (45).png>)

![](<../.gitbook/assets/image (49).png>)

![](<../.gitbook/assets/image (43).png>)

### 5、设置进出站规则

新建的甲骨文云帐号是关闭了所有的入站连接的,需要在防火墙处添加规则允许入站

点击前面实例的名称来到这个界面，点击**子网**

![](https://pic2.zhimg.com/80/v2-c67dadad31901cbfa3b6315548fa2995\_720w.jpg)

点击默认的安全子网

![](https://pic4.zhimg.com/80/v2-71885f27452b523c55f19339735d217f\_720w.jpg)

入站规则 – 源CIDR 0.0.0.0/0 – 所有协议

![](<../.gitbook/assets/image (86).png>)

同样的，在左侧可以看到出站规则，同样改一下就好了

### 6、刷新更换IP

目前甲骨文云Oracle Cloud支持手动免费更换IP地址、在实例概览界面下拉，点击附加的VNIC-MAC地址处3点-查看详细信息

![](https://pic3.zhimg.com/80/v2-9948709afde944f7c1d9110642250d16\_720w.jpg)

`IP地址`-`已分配处点击3点`-`编辑`

![](https://pic4.zhimg.com/80/v2-d232f35758dcb924d6a9ac4c2204790b\_720w.jpg)

选择**没有公共IP**-**更新**. 这样就可以释放之前的IP地址,

![](https://pic1.zhimg.com/80/v2-c92f7e806f77b3229179e9992ee7bb6c\_720w.jpg)

重复第2步，然后选择**临时公共IP**-**更新**

系统会再次分配一个不同的IP地址到这个实例了,至此，IP地址更换成功了。

在实例的地方需要下滑排版有点考下位置，确认后记得要保存别直接退出来\
替换IP也就是先释放当前使用的IP后，然后重新获取一个公有IP\
换IP随机和GCP差不多但是不同时间段刷出的段位是玄学盲盒\
这有一定概率会在好几个IP重复出现，当然你可以换个别的时间操作

### 7、关闭插件

网上很多同学有这个步骤，其实如果DD系统的话，应该不需要这步，不放心的话就操作一下吧！

![](<../.gitbook/assets/image (52).png>)

### 8、终止实例

一张图告诉你步骤

![](<../.gitbook/assets/image (69).png>)

##
