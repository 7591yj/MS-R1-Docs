# 在 MS-R1 上安装 PVE 的方法

## 介绍
你可以在 MS-R1 的 Debian 12 系统上安装 PVE 8.3。  
请确保在安装过程中网线已连接并能上网。本指南假设系统已能访问互联网。

## 安装步骤

### 1. 准备环境

#### 1.1 设置 root 密码
本 playbook 中的每一步都需要 root 权限。切换到 root 用户：
```bash
sudo -i
```
设置 root 密码：
```bash
passwd root
```

PVE 的 Web 管理需要使用 root 账户登录，确保已为 root 设置密码：
```bash
passwd root
```

#### 1.2 安装 `sshd` 并允许 root 登录
```bash
apt update
apt install ssh
# 允许 root 登录（追加，不覆盖）
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
systemctl restart ssh
```

### 2. 添加仓库

#### 2.1 添加 PVE ARM 社区仓库源
```bash
curl -L https://mirrors.lierfang.com/pxcloud/lierfang.gpg -o /etc/apt/trusted.gpg.d/lierfang.gpg
echo "deb https://mirrors.lierfang.com/pxcloud/pxvirt bookworm main" >/etc/apt/sources.list.d/pxvirt-sources.list
apt update
```

还有其他社区镜像；选择对你最快的：
- 中国: https://mirrors.lierfang.com
- 德国: https://de.mirrors.lierfang.com
- Cloudflare: https://mirrors.apqa.cn
- 荷兰: https://apt.dedi.zone/pxcloud/pxvirt
- 日本: https://mirrors.homelabproject.cc

### 3. 配置网络并为 PVE 修改 hosts

> **警告**
> 1. 文中所有出现的 IP 地址 `192.168.32.116` 仅为示例。  
> 2. 请用你自己的 IP 地址替换示例地址。

#### 3.1 为 PVE 修改 /etc/hosts
使用 `ip addr show` 获取你的网卡名和 IP，然后编辑 `/etc/hosts`：
```bash
nano /etc/hosts
```
示例内容：
```
127.0.0.1       localhost
192.168.32.116 mini-localhost
127.0.1.1       mini-localhost
```
确保已添加包含主机 IP 和主机名的那一行（示例：`192.168.32.116 mini-localhost`）。

#### 3.2 禁用 NetworkManager 并使用 ifupdown2
> **注意**
> 只在想用 PVE 的网络配置时更改网络管理器。如果你更喜欢保留 Debian 的 NetworkManager，也可以保留，但之后需要通过桌面网络面板来修改网络。
>
> **警告**
> 如果你不理解本步骤，请跳过 3.2。

PVE 使用 ifupdown2。禁用 NetworkManager 并安装 ifupdown2：
```bash
systemctl disable NetworkManager
systemctl stop NetworkManager
apt update
apt install ifupdown2 -y
rm -f /etc/network/interfaces.new
```

检查当前网络：
```bash
root@cix-localhost:~# ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host noprefixroute 
       valid_lft forever preferred_lft forever
2: eth0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc mq state DOWN group default qlen 1000
    link/ether 38:05:25:30:bf:e5 brd ff:ff:ff:ff:ff:ff
    altname eth0
3: eth1: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP group default qlen 1000
    link/ether 38:05:25:30:bf:e6 brd ff:ff:ff:ff:ff:ff
    altname eth1
    inet 192.168.32.116/24 brd 192.168.32.255 scope global dynamic noprefixroute eth1
       valid_lft 5741sec preferred_lft 5741sec
    inet6 fe80::8574:50d4:6212:23f2/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
root@cix-localhost:~# ip route
default via 192.168.32.1 dev eth1 proto dhcp src 192.168.32.116 metric 100 
192.168.32.0/24 dev eth1 proto kernel scope link src 192.168.32.116 metric 100
```
上例中 `eth1` 有 IP 地址。我的 IP 是 `192.168.32.116`，网关是 `192.168.32.1`。  
创建网络配置文件（创建虚拟桥接，使虚拟机能共享主机网络）：

!!!note
    有时网卡名称会显示为 `eth0` `eth1`，但有时或在升级系统包后会显示为 `enp1s0` `enp49s0`。请根据实际情况调整。

```bash
nano /etc/network/interfaces
```
示例内容：
```
auto lo
auto vmbr0
iface vmbr0 inet static
    address 192.168.32.116/24
    gateway 192.168.32.1
    bridge-ports eth1
    bridge-stp off
    bridge-fd 0
```

重启以应用更改。

> **警告**
> 此步骤之后网络将被配置为静态。注意物理网口的使用情况，并在确认配置正确之前不要更改网线连接；错误的网络设置可能导致主机无法访问。

### 4. 安装 PVE 软件

#### 4.1 安装 PVE 包
```bash
apt remove cix-openssl || true
apt install rsyslog
modprobe fuse
apt update && apt install proxmox-ve
```

说明：
1. 如果存在 `cix-openssl`，请先移除。
2. 加载 `fuse` 内核模块。
3. 安装过程中可能会提示：邮件配置选择 “no configuration”，如果被询问 kexec-tools，选择 “No”。

#### 4.2 添加在网络前加载内核模块的服务
创建一个 systemd 单元，以便在网络服务之前加载所需模块：
```bash
tee /etc/systemd/system/load-beforenet.service << 'EOF'
[Unit]
Description=Load Bridge Module
DefaultDependencies=no
Before=network.target
Before=pve-cluster.service

[Service]
Type=oneshot
ExecStart=/sbin/modprobe -a bridge fuse
RemainAfterExit=true

[Install]
WantedBy=multi-user.target
EOF

systemctl enable load-beforenet.service
systemctl start load-beforenet.service
```

#### 4.3 禁用图形界面（可选）
如果不需要图形桌面：
```bash
systemctl set-default multi-user.target   # 禁用图形界面
# 如需重新启用：
# systemctl set-default graphical.target
```

### 5. 重启并开始使用你的 PVE 虚拟机系统
重启宿主机以确保所有更改生效：
```bash
reboot
```

### 6. 备注与提示
- 核心分组：核心 "0-1,10-11" 为大核；"6-9" 为中核；"2-5" 为小核。不要在同一个 VM 的 vCPU 中混合大/中核与小核，可能导致启动问题 — 建议在创建 VM 后再设置核心亲和性。
- 核心频率与 OpenSSL AES-128-GCM 分数（示例）：

| 核心编号 | 架构 | 频率 | OpenSSL AES-128-GCM 得分 |
|----------|------|------|--------------------------|
| 0  | Cortex-A720 | 2.6 GHz | 2,564,396.37 k |
| 1  | Cortex-A720 | 2.6 GHz | 2,554,669.74 k |
| 2  | Cortex-A520 | 1.8 GHz | 564,560.75 k |
| 3  | Cortex-A520 | 1.8 GHz | 564,230.87 k |
| 4  | Cortex-A520 | 1.8 GHz | 566,136.16 k |
| 5  | Cortex-A520 | 1.8 GHz | 566,869.85 k |
| 6  | Cortex-A720 | 2.4 GHz | 2,258,687.32 k |
| 7  | Cortex-A720 | 2.4 GHz | 2,254,432.94 k |
| 8  | Cortex-A720 | 2.3 GHz | 2,154,856.45 k |
| 9  | Cortex-A720 | 2.3 GHz | 2,157,428.74 k |
| 10 | Cortex-A720 | 2.5 GHz | 2,451,182.93 k |
| 11 | Cortex-A720 | 2.5 GHz | 2,451,466.92 k |
