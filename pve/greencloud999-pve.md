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
$(curl -sL ifconfig.me)   $(hostnamectl | grep hostname | awk '{print $3}').proxmox.com $(hostnamectl | grep hostname | awk '{print $3}')

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
apt remove linux-image-amd64 'linux-image-5.10*' && update-grub
```

## 7. 删除操作系统探测器软件包

该`os-prober`软件包会扫描主机的所有分区以创建双引导 GRUB 条目。但是扫描的分区还可以包括那些分配给虚拟机的分区，这些分区不想添加为引导条目。

如果您没有将 Proxmox VE 作为双启动安装在另一个操作系统旁边，您可以安全地删除该`os-prober`软件包：

```
apt remove os-prober -y
```

# 二、网络配置

## 1.配置网卡

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
    address 2402:a7c0:8100:a015::5eb:9342/64
    gateway 2402:a7c0:8100:a015::1

auto vmbr1
iface vmbr1 inet static
    address 192.168.0.1
    netmask 255.255.255.0
    bridge_ports none
    bridge_stp off
    bridge_fd 0
    post-up iptables -t nat -A POSTROUTING -s '192.168.0.0/24' -o vmbr0 -j MASQUERADE
    post-down iptables -t nat -D POSTROUTING -s '192.168.0.0/24' -o vmbr0 -j MASQUERADE
    post-up iptables -t raw -I PREROUTING -i fwbr+ -j CT --zone 1
    post-down iptables -t raw -D PREROUTING -i fwbr+ -j CT --zone 1
iface vmbr1 inet6 static
    address 2402:a7c0:8100:a015::5eb:9343/64
EOF
```

```
systemctl restart networking
```

## 2.配置TCP

```
cat > /etc/sysctl.conf << EOF
fs.file-max = 6553500

net.ipv4.tcp_congestion_control=bbr
net.core.default_qdisc=fq
net.ipv4.conf.all.rp_filter=1
net.ipv4.icmp_echo_ignore_broadcasts=1
net.ipv4.conf.default.forwarding=1
net.ipv4.conf.default.proxy_arp = 0
net.ipv4.ip_forward=1
kernel.sysrq = 1
net.ipv4.conf.default.send_redirects = 1
net.ipv4.conf.all.send_redirects = 0

net.ipv4.ip_forward= 1
net.ipv6.conf.all.accept_dad = 1
net.ipv6.conf.all.accept_ra = 0
net.ipv6.conf.all.accept_redirects = 1
net.ipv6.conf.all.accept_source_route = 0
net.ipv6.conf.all.autoconf = 0
net.ipv6.conf.all.disable_ipv6 = 0
net.ipv6.conf.all.forwarding = 1
net.ipv6.conf.all.proxy_ndp = 1
EOF
```

```
sysctl -p
```

## 3.配置HDCP V4

- 安装HDCP 服务

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
INTERFACESv4="vmbr0"  //网卡
#INTERFACESv6=""  
```

- 找到打开 /etc/dhcp/dhcpd.conf

```
subnet 192.168.0.0 netmask 255.255.255.0 {
option routers 192.168.0.1;
option subnet-mask 255.255.255.0;
option domain-name-servers 8.8.8.8;
range 192.168.0.2 192.168.0.100;
}
```





