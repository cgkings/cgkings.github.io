# greencloud999 PVE

## 一.Debian11 部署PVE

### 1.安装一个标准的 Debian Bullseye （amd64）

### 2.为您的 IP 地址添加 /etc/hosts 条目

计算机的主机名必须可通过/etc/hosts解析。 这意味着在/etc/hosts中，您需要以下条目之一作为主机名：

* 1 个 IPv4 或
* 1 个 IPv6 或
* 1 个 IPv4 和 1 个 IPv6

**注意**：这也意味着删除默认地址127.0.1.1。

例如，如果您的IP地址是192.168.15.77，并且您的主机名prox4m1，那么您/etc/hosts文件可能如下所示：

```
127.0.0.1       localhost
192.168.15.77   prox4m1.proxmox.com prox4m1

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

您可以使用主机名命令测试您的设置是否正常：

```
hostname --ip-address
```

> 192.168.15.77 # should return your IP address here

### 3.安装 Proxmox VE

#### 3.1 调整您的来源列表

添加 Proxmox VE 存储库：

```
echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bullseye pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
```

将Proxmox VE存储库密钥添加为根（或使用sudo）：

```
wget https://enterprise.proxmox.com/debian/proxmox-release-bullseye.gpg -O /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg 
# verify
sha512sum /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg 
7fb03ec8a1675723d2853b84aa4fdb49a46a3bb72b9951361488bfd19b29aab0a789a4f8c7406e71a69aabbc727c936d3549731c4659ffa1a08f44db8fdcebfa  /etc/apt/trusted.gpg.d/proxmox-release-bullseye.gpg 
```

通过运行以下命令更新存储库和系统：

```
apt update && apt full-upgrade
```

#### 3.2 安装 Proxmox VE 软件包

安装Proxmox VE软件包

```
apt install proxmox-ve postfix open-iscsi
```

根据您的需要配置需要用户在安装时输入的软件包。

如果您的网络中有邮件服务器，则应将 postfix 配置为**附属系统**。 然后，您现有的邮件服务器将成为_中继主机_，它将Proxmox VE发送的电子邮件路由到其最终收件人。 如果您不知道在此处输入什么，请选择**“仅本地”**并保留_系统名称_。

#### 3.3 将系统重新启动到Proxmox VE

最后，**重新启动**系统。

```
 systemctl reboot
```

#### 3.4 删除 Debian 内核

Proxmox VE附带了自己的内核，保留Debian默认内核可能会导致升级问题，例如，使用Debian点版本。 因此，您必须删除默认的 Debian 内核：

```
apt remove linux-image-amd64 'linux-image-5.10*'
```

通过运行以下命令更新和检查 grub2 配置：

```
update-grub
```

#### 3.5 建议：删除操作系统探测器软件包

软件包会扫描主机的所有分区以创建双引导 GRUB 条目。 但是扫描的分区也可以包括分配给虚拟机的分区，这些分区不想添加为启动项。`os-prober`

如果您没有将Proxmox VE作为另一个操作系统旁边的双启动安装，则可以安全地删除该软件包：`os-prober`

```
apt remove os-prober
```
