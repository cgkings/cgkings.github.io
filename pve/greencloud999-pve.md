# greencloud999 PVE

# 一.Debian11 部署PVE

## 1.安装一个标准的 Debian Bullseye （amd64)

```
apt update --fix-missing && apt install -y curl sudo git make wget tree vim nano tmux htop net-tools parted nethogs screen ntpdate manpages-zh screenfetch file virt-what iperf3 jq expect 2> /dev/null && apt install -y ca-certificates dmidecode findutils dpkg tar zip unzip gzip bzip2 unar p7zip-full pv ffmpeg build-essential ncdu zsh fonts-powerline fuse 2> /dev/null
```

## 2.为您的 IP 地址添加 /etc/hosts 条目

例如，如果您的IP地址是192.168.15.77，并且您的主机名prox4m1，那么您/etc/hosts文件可能如下所:`192.168.15.77 prox4m1.proxmox.com prox4m1`

便捷的方法就是复制执行下面的命令：

```
cat > /etc/hosts << EOF
127.0.0.1       localhost.localdomain localhost
$(curl -sL ifconfig.me)   $(hostnamectl | grep hostname | awk '{print $3}').xgh1.com $(hostnamectl | grep hostname | awk '{print $3}')

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
EOF
```

## 3.安装 Proxmox VE : 调整您的来源列表

添加 Proxmox VE 存储库：

```
echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
```

将Proxmox VE存储库密钥添加为根（或使用sudo）：

```
wget -q https://enterprise.proxmox.com/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg
```

{% hint style="info" %}
```
#验证命令
sha512sum /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg 
7fb03ec8a1675723d2853b84aa4fdb49a46a3bb72b9951361488bfd19b29aab0a789a4f8c7406e71a69aabbc727c936d3549731c4659ffa1a08f44db8fdcebfa  /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg
```
{% endhint %}

## 4.安装 Proxmox VE :  &#x20;

```
apt update -y && apt full-upgrade -y && apt install -y pve-kernel-5.15 && systemctl reboot
```

请注意，虽然这`pve-kernel-5.15`是 Proxmox VE 7.x 系列的默认稳定内核，但还有一个更新的可选内核可用`pve-kernel-5.19`，可能有助于解决现代系统上的一些与硬件相关的问题。

## 5.安装 Proxmox VE :  &#x20;

```
apt install -y proxmox-ve postfix open-iscsi
```

根据您的需要配置在安装时需要用户输入的软件包。

如果您的网络中有邮件服务器，您应该将 postfix 配置为**卫星系统**。然后，您现有的邮件服务器将成为_中继主机_，它将 Proxmox VE 发送的电子邮件路由到其最终收件人。如果您不知道在此处输入什么，请选择**仅本地**并保持_系统名称_不变。

## 6. 删除 Debian 内核

Proxmox VE附带了自己的内核，保留Debian默认内核可能会导致升级问题，例如，使用Debian点版本。 因此，您必须删除默认的 Debian 内核更新和检查 grub2 配置：

```
apt remove -y linux-image-amd64 'linux-image-5.10*' && update-grub
```

## 7. 删除操作系统探测器软件包

该`os-prober`软件包会扫描主机的所有分区以创建双引导 GRUB 条目。但是扫描的分区还可以包括那些分配给虚拟机的分区，这些分区不想添加为引导条目。

如果您没有将 Proxmox VE 作为双启动安装在另一个操作系统旁边，您可以安全地删除该`os-prober`软件包：

```
apt remove os-prober -y
```

# 二.网络配置

## 1.配置网卡

安装完成后，登录到proxmox的控制台，编辑grub配置文件：

```
nano /etc/default/grub
```

修改如下配置，加入net.ifnames=0 biosdevname=0内核启动参数：

```
GRUB_CMDLINE_LINUX_DEFAULT="quiet net.ifnames=0 biosdevname=0"
```

更新grub：

```
update-grub
```

然后编辑网卡配置文件：

```
cat > /etc/network/interfaces << EOF
source /etc/network/interfaces.d/*

auto lo
iface lo inet loopback

iface $(ifconfig | grep -B 1 "$(curl -sL ifconfig.me)" | head -n 1 | awk -F: '{print $1}') inet manual

auto vmbr0
iface vmbr0 inet static
    address $(curl -sL ifconfig.me)/24
    gateway $(ip route list | grep default | awk '{print $3}')
    bridge-ports $(ifconfig | grep -B 1 "$(curl -sL ifconfig.me)" | head -n 1 | awk -F: '{print $1}')
    bridge-stp off
    bridge-fd 0

iface vmbr0 inet6 static
    address $(ip -6 a|grep -m 1 global|awk '{print $2}'|awk -F "/" '{print $1}')/64
    gateway $(ip -6 route list | grep -m 1 default | awk '{print $3}')

auto vmbr1
iface vmbr1 inet static
    address 192.168.0.1
    netmask 255.255.255.0
    bridge_ports none
    bridge_stp off
    bridge_fd 0
    post-up echo 1 > /proc/sys/net/ipv4/ip_forward
    post-up iptables -t nat -A POSTROUTING -s '192.168.0.0/24' -o vmbr0 -j MASQUERADE
    post-down iptables -t nat -D POSTROUTING -s '192.168.0.0/24' -o vmbr0 -j MASQUERADE
    post-up iptables -t raw -I PREROUTING -i fwbr+ -j CT --zone 1
    post-down iptables -t raw -D PREROUTING -i fwbr+ -j CT --zone 1
EOF
```



```
cat > /etc/network/interfaces << EOF
auto lo
iface lo inet loopback

iface eth0 inet manual

auto vmbr0
iface vmbr0 inet static
        address 164.152.166.199/24
        gateway 164.152.166.1
        netmask 255.255.254.0
        dns-nameservers 8.8.8.8 8.8.4.4
        bridge-ports eth0
        bridge-stp off
        bridge-fd 0

auto vmbr1
iface vmbr1 inet static
        address 192.168.0.1
        netmask 255.255.255.0
        bridge_ports none
        bridge_stp off
        bridge_fd 0
        post-up echo 1 > /proc/sys/net/ipv4/ip_forward
        post-up iptables -t nat -A POSTROUTING -s '192.168.0.0/24' -o vmbr0 -j MASQUERADE
        post-down iptables -t nat -D POSTROUTING -s '192.168.0.0/24' -o vmbr0 -j MASQUERADE
EOF
```

```
systemctl restart networking.service
```



## 2.配置TCP

```
echo "net.ipv6.conf.all.forwarding = 1" >> /etc/sysctl.conf && sysctl -p
```

```shell
cat > /etc/sysctl.conf << EOF
fs.file-max = 6553500
net.core.default_qdisc=fq
net.ipv4.tcp_congestion_control=bbr
net.ipv4.conf.all.rp_filter=1
net.ipv4.icmp_echo_ignore_broadcasts=1
net.ipv4.conf.default.forwarding=1
net.ipv4.conf.default.proxy_arp = 0
net.ipv4.ip_forward=1
kernel.sysrq = 1
net.ipv4.conf.default.send_redirects = 1
net.ipv4.conf.all.send_redirects = 0
net.ipv4.ip_forward=1
net.ipv6.conf.all.accept_dad = 1
net.ipv6.conf.all.accept_ra = 0
net.ipv6.conf.all.accept_redirects = 1
net.ipv6.conf.all.accept_source_route = 0
net.ipv6.conf.all.autoconf = 0
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.all.forwarding=1
net.ipv6.conf.all.proxy_ndp=1
EOF
```

## 3.配置DHCP V4

- 安装DHCP 服务

```
#安装hdcp
apt install isc-dhcp-server
#启动查看状态 
systemctl status isc-dhcp-server
#重启hdcp
service isc-dhcp-server restart
#开机启动
systemctl enable isc-dhcp-server  
```

- 找到并编辑 /etc/default/isc-dhcp-server 

```
cat > /etc/default/isc-dhcp-server << EOF
INTERFACESv4="vmbr0"  //网卡
#INTERFACESv6=""  
EOF
```

- 找到打开 /etc/dhcp/dhcpd.conf

```
cat > /etc/dhcp/dhcpd.conf << EOF
subnet 192.168.0.0 netmask 255.255.255.0 {
option routers 192.168.0.1;
option subnet-mask 255.255.255.0;
option domain-name-servers 8.8.8.8;
range 192.168.0.2 192.168.0.100;
};
EOF
```



```
cat > /etc/dhcp/dhcpd.conf << EOF
# A slightly different configuration for an internal subnet.
subnet 192.168.0.0 netmask 255.255.255.0 {
   option routers 192.168.0.1;
   option subnet-mask 255.255.255.0;
   option domain-name-servers 8.8.8.8;
   range 192.168.0.2 192.168.0.200;
   #option domain-name "internal.example.org";
   #option broadcast-address 192.168.0.255;
   default-lease-time 600;
   max-lease-time 7200;
};
EOF
```

## 4.安装ndppd：

```
apt -y install ndppd
```

写入如下配置，ipv6地址块换成你自己的：

```
cat > /etc/ndppd.conf << EOF
proxy vmbr0 {
  rule 2402:a7c0:8100:a015::5eb:9342/64 {
    static
  }
}
EOF
```

重启ndppd服务使其生效，并设置开机自启：

```
systemctl restart ndppd.service && systemctl enable ndppd.service
```

现在开小鸡的话ipv6实际上就能用了，但是只能手动设置小鸡的ipv6地址

## 5.安装radvd

小鸡的ipv6地址，每台都手动设置的话太麻烦，重复劳动浪费时间，所以这里可以配置radvd实现slaac

```
apt -y install radvd
```

新建radvd配置文件：

```
nano /etc/radvd.conf
```

写入如下配置，ipv6地址块换成你自己的：

```
cat > /etc/radvd.conf << EOF
interface vmbr0 {
  AdvSendAdvert on;
  MinRtrAdvInterval 30;
  MaxRtrAdvInterval 100;
  prefix 2402:a7c0:8100:a015::/64 {
    AdvOnLink on;
    AdvAutonomous on;
    AdvRouterAddr on;
  };
};
EOF
```

重启radvd服务使其生效，并设置开机自启：

```
systemctl restart radvd.service && systemctl enable radvd.service
```

下面是开小鸡时的设置，注意不论是lxc还是kvm架构的小鸡，我这个配置统一都需要添加两个网卡

## 6.设置V6 DNS

- 在PVE面板中添加一个v6DNS用于解析V6

![img](https://xidcn.com/wp-content/uploads/2022/02/Screenshot_20220209-212247-1024x396.jpg)

# 三.创建LXC小鸡

对于lxc小鸡，在创建的时候可以先添加一个用于ipv6网络的eth0，桥接vmbr0，ipv6选择slaac：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_12-56-13.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_12-56-13.png)

等小鸡创建完成后再添加一个用于ipv4网络的eth1，桥接vmbr1，手动配置ipv4的地址和网关：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-02-07.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-02-07.png)

正常情况下，小鸡现在就是natipv4+ipv6双栈网络了：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-07-26.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-07-26.png)

对于kvm小鸡，实际操作大同小异，这里我就开个debian11的小鸡演示一下配置。

# 四.创建KVM小鸡

对于kvm小鸡，实际操作大同小异，这里我就开个debian11的小鸡演示一下配置。

创建的时候先添加一个桥接vmbr0的网卡：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-09-31.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-09-31.png)

等小鸡创建完成后再添加一个桥接vmbr1的网卡：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-10-53.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-10-53.png)

打开vnc控制台开始安装系统：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-13-00.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-13-00.png)

这里有两块网卡ens18和ens19，实际ens18对应vmbr0，ens19对应vmbr1，这里选择ens18就可以实现ipv6的slaac：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-13-51.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-13-51.png)

至于ens19那块对应vmbr1的网卡，等待系统安装完成后手动配置ipv4即可。

接下来需要配置一个dns：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-16-57.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-16-57.png)

剩下的就是设置root密码，创建普通用户，还有分区这些了，这里就不多说了。等系统安装好，登录进去后，测试ipv6是否正常：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-28-14-1024x199.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-28-14.png)

现在这台小鸡只有ipv6，如果还需要natipv4的话就得修改网卡配置文件，默认的配置是这样：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-30-13-1024x242.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_13-30-13.png)

只需要在文件的尾部添加ens19网卡的ipv4配置：

```
auto ens19
iface ens19 inet static
	address 192.168.0.4/32
	gateway 192.168.0.1
```

然后添加一个dns服务器：

```
nano /etc/resolv.conf
```

写入如下配置：

```
nameserver 8.8.8.8
```

重启机器即可。

# 五.创建win小鸡

首先准备win11的iso和virtio驱动的iso。

win11iso下载：https://www.microsoft.com/zh-cn/software-download/windows11

可以把地址复制到pve面板里面下载：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-25-37.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-25-37.png)

virtio驱动iso下载，下载latest版本：https://github.com/virtio-win/virtio-win-pkg-scripts

同样也可以在pve面板里面下载：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-33-45.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-33-45.png)

创建虚拟机，选择刚下载的win11iso，注意选择正确的操作系统类别和版本：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-35-19.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-35-19.png)

下一步到系统配置这块，如果上一步选择的版本是11/2022那么这里默认就是最佳配置了，只需要选择一下efi和tpm的存储池即可，另外建议启用qemu代理这个选项，后面再详细介绍qemu代理的作用：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-39-47.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-39-47.png)

硬盘这里的总线/设备配置为virtioblock，缓存配置为回写，这样可以让虚拟机的硬盘性能达到最好：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-40-56.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-40-56.png)

cpu类别建议选择host，并且启用numa，其他根据自己的需要配置即可：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-42-27.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-42-27.png)

内存的设置这里就不截图详细说明了，建议给4g以上。

网络模型建议选择virtio半虚拟化，这样可以让虚拟机的网络性能达到最好：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-43-06.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-43-06.png)

由于我是ipv4+ipv6双栈，一个网卡只处理一种协议，之前的vmbr0只处理ipv6，所以这里等机器创建好以后继续添加一个vmbr1的网卡来支持ipv4：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-45-59.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-45-59.png)

关于网络这块的配置可以参考我这篇文章：



接下来还需要添加一个cd/dvd驱动器来挂载之前下载的virtio驱动，由于我们之前在创建虚拟机的时候，硬盘和网卡都设置的是virtio模型，所以这里必须要用到virtio的驱动，否则在安装的时候系统识别不到硬盘和网络：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-48-21.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-48-21.png)

全部设置好了后，启动虚拟机，打开vnc控制台在看到proxmox的logo时按回车进入windows的安装界面：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-50-09-1024x765.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-50-09.png)

不出意外的话，在硬盘分区这一步的时候windows安装程序会提示找不到任何硬盘，此时点击加载驱动程序：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-54-18-1024x764.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-54-18.png)

浏览virtio-win这个驱动器，找到里面的目录amd64/w10：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-55-35-1024x767.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-55-35.png)

即可安装硬盘驱动：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-56-06-1024x766.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-56-06.png)

此时可以看到硬盘能够识别了，但别急着分区，还是点击加载驱动程序：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-57-15-1024x763.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-57-15.png)

和之前一样浏览virtio-win驱动器，找到netkvm/w10/amd64目录：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-58-40-1024x766.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-58-40.png)

安装网卡驱动：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-58-56-1024x763.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-25_20-58-56.png)

然后又会回到硬盘分区那里，此时virtio硬盘驱动和网卡驱动均已安装完成，一直下一步即可完成安装。

win11的网络配置，打开设置-网络和internet-以太网：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_14-49-27.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_14-49-27.png)

里面会有两个网络，第一个网络实际对应vmbr0，第二个网络实际对应vmbr1：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_14-50-33.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_14-50-33.png)

第一个网络是用来支持ipv6的，由于我之前配置radvd的时候忘记配置dns了，所以这里radvd虽然自动分配了ipv6地址，但是没有配置dns，手动加个dns地址即可：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_14-54-59.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_14-54-59.png)

第二个网络是用来支持ipv4的，这里全部手动配置一下即可：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_14-57-35.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_14-57-35.png)

嫌麻烦的话也可以在母鸡装个dhcp服务，这里就不介绍了，以前水过文章的。。

在系统安装的时候只装了硬盘和网卡驱动，现在把剩下没有安装的驱动全部安装上。

打开virtio-win驱动器，双击virtio-win-gt-x64开始安装：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_15-06-17.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_15-06-17.png)

在之前创建虚拟机的时候勾选了qemu代理，为了这个功能能够正常使用，你还需要双击virtio-win-guest-tools来安装qemu代理：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_15-08-30.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_15-08-30.png)

验证qemu代理是否正常，可以在pve面板看到虚拟机的网络信息：

[![img](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_15-11-35.png)](https://lala.im/wp-content/uploads/2021/11/lala.im_2021-11-28_15-11-35.png)

qemu代理主要的作用：

https://pve.proxmox.com/wiki/Qemu-guest-agent

# 六.暴露端口

在母鸡上做dnat，把小鸡的ssh端口暴露出来：

```
iptables -t nat -A PREROUTING -p tcp -m tcp --dport 16823 -j DNAT --to-destination 192.168.0.5:3389
```

