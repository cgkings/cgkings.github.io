# greencloud999 PVE

## 一.Debian11 部署PVE

### 1.安装一个标准的 Debian Bullseye （amd64)



```
apt update --fix-missing 2> /dev/null | grep packages | cut -d '.' -f 1 && apt install -y curl sudo git make wget tree vim nano tmux htop net-tools parted nethogs screen ntpdate manpages-zh screenfetch file virt-what iperf3 jq expect 2> /dev/null && apt install -y ca-certificates dmidecode findutils dpkg tar zip unzip gzip bzip2 unar p7zip-full pv ffmpeg build-essential ncdu zsh fonts-powerline fuse 2> /dev/null
```

### 2.为您的 IP 地址添加 /etc/hosts 条目

例如，如果您的IP地址是192.168.15.77，并且您的主机名prox4m1，那么您/etc/hosts文件可能如下所:`192.168.15.77 prox4m1.proxmox.com prox4m1`



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

### 3.安装 Proxmox VE : 调整您的来源列表

添加 Proxmox VE 存储库：

```
echo "deb [arch=amd64] http://download.proxmox.com/debian/pve Bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
```

将Proxmox VE存储库密钥添加为根（或使用sudo）：

```
wget -q https://enterprise.proxmox.com/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg
```

{% hint style="info" %}
```
# verify
sha512sum /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg 
7fb03ec8a1675723d2853b84aa4fdb49a46a3bb72b9951361488bfd19b29aab0a789a4f8c7406e71a69aabbc727c936d3549731c4659ffa1a08f44db8fdcebfa  /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg
```
{% endhint %}

### 4.安装 Proxmox VE :  &#x20;

```
apt update -y && apt full-upgrade -y && apt install -y pve-kernel-5.15 && systemctl reboot
```

请注意，虽然这`pve-kernel-5.15`是 Proxmox VE 7.x 系列的默认稳定内核，但还有一个更新的可选内核可用`pve-kernel-5.19`，可能有助于解决现代系统上的一些与硬件相关的问题。

### 5.安装 Proxmox VE :  &#x20;

```
apt install -y proxmox-ve postfix open-iscsi
```

根据您的需要配置在安装时需要用户输入的软件包。

如果您的网络中有邮件服务器，您应该将 postfix 配置为**卫星系统**。然后，您现有的邮件服务器将成为_中继主机_，它将 Proxmox VE 发送的电子邮件路由到其最终收件人。如果您不知道在此处输入什么，请选择**仅本地**并保持_系统名称_不变。

### 6. 删除 Debian 内核

Proxmox VE附带了自己的内核，保留Debian默认内核可能会导致升级问题，例如，使用Debian点版本。 因此，您必须删除默认的 Debian 内核更新和检查 grub2 配置：

```
apt remove linux-image-amd64 'linux-image-5.10*' && update-grub
```

### 7. 删除操作系统探测器软件包

该`os-prober`软件包会扫描主机的所有分区以创建双引导 GRUB 条目。但是扫描的分区还可以包括那些分配给虚拟机的分区，这些分区不想添加为引导条目。

如果您没有将 Proxmox VE 作为双启动安装在另一个操作系统旁边，您可以安全地删除该`os-prober`软件包：

```
apt remove os-prober -
```

