# How to install PVE into MS-R1

## Intro
You can install PVE8.3 into MS-R1's Debian12 Systen
Please ensure that the network cable is connected during installation and that there is no Internet connection.

## How To Install
### 1. Prepare your environment
#### 1.1 Setting your root password
PVE System needs root account to login web admin. You needs make sure your root password have already set.
```bash
passwd root
```

#### 1.2 Install `sshd`, and make sure root can login.
```bash
apt update
apt install ssh
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config
```


### 2. Add repo
#### 2.1 Add PVE ARM community repo source.
```bash
curl -L https://mirrors.lierfang.com/pxcloud/lierfang.gpg -o /etc/apt/trusted.gpg.d/lierfang.gpg
echo "deb https://mirrors.lierfang.com/pxcloud/pxvirt bookworm main">/etc/apt/sources.list.d/pxvirt-sources.list
```

There are some other's community repo. You can choose your best speed server and replace it.
```
China: https://mirrors.lierfang.com
Germany: https://de.mirrors.lierfang.com
Cloudflare: https://mirrors.apqa.cn
Netherlands: https://apt.dedi.zone/pxcloud/pxvirt
Japan: https://mirrors.homelabproject.cc
```

### 3. Configuring your Networking, Modify hosts for PVE
!!! warning
    1. All of IP Address "192.168.32.116" is a example.
    2. You needs replace it to your own ip address.

#### 3.1 Modify hosts for PVE
You needs add your ip and your hostname into /etc/hosts
##### For example:

Using `ip addr show` to get your network card devices name and ip address
Then Edit your /etc/hostsname file: `nano /etc/hosts`

```bash
127.0.0.1       localhost
192.168.32.116	mini-localhost
127.0.1.1 mini-localhost
```
Make sure you added this line `192.168.32.116	mini-localhost`

192.168.32.116 is currently my ip address. You need repalce it by you internel networking ip.

#### 3.2 Disable NetworkManager and using ifupdown2
!!!note
    Change network manager just in order to maintain the official PVE network manager. You can manage the network in the PVE web management. 
    You can keep Debian's NetworkManager. But if you want change networking setting, you needs go to Debian's desktop network manager panel.
!!!warning
    If you don't understand this step, please don't do step 3.2
PVE using ifupdown2 configuration networking. You needs disable NetworkManager and enable ifupdown2

```bash
systemctl disable NetworkManager
systemctl stop NetworkManager
apt update
apt install ifupdown2 -y
rm /etc/network/interfaces.new
```
Check your currently networking information
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
There have a ip address on `eth1`. My ip address is `192.168.32.116`, my gateway is `192.168.32.1`
Create networking config file. (Create virtual bridge let VMs can share host networkign)

!!!note
    Sometime, network device name show as `eth0` `eth1`, but sometimes or you upgrade your system package, it will show as `enp1s0` `enp49s0`.
    You need to base it on your actual situation.

```bash
nano /etc/network/interfaces
```
Write these information
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

Then reboot your system

!!! warning
    Then restart the machine to ensure the network is properly applied. If the network configuration is incorrect, it might cause installation interruption due to network disconnection, making the machine inaccessible remotely.

!!! warning
    After this step. You network setting have be config to Fixed configuration
    You need remmember your port location and always use that port, before you manually change your network setting.


### 4. Install PVE Software

#### 4.1 Install PVE software package
```bash
apt remove cix-openssl
apt install rsyslog
modprobe fuse
apt update && apt install proxmox-ve
```

!!!notice
    1. Remember remove "cix-openssl" package.(Even sometime there is no this package)
    2. Remember load module "fuse"
    3. Configuring email: choose "no configration"
    4. Configuring kexec-tools: choose "No"



#### 4.2 Add kernel module load service
Make sure important kernel modules are added before network configuration
```bash
tee /etc/systemd/system/load-beforenet.service << EOF
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
```
```bash
systemctl enable load-beforenet.service
systemctl start load-beforenet.service
```

#### 4.3 If you do not need graphical interface you can disable it.

```bash
systemctl set-default multi-user.target # disable graphical interface
systemctl set-default graphical.target # enable graphical interface
```

### 5. Reboot and enjoy your PVE VMs system

!!!notice Tips 
    Core "0-1,10-11" is big cores, core "6-9" is middle cores, core "2-5" is small cores.
    Big core and middle cores are same architechture which is different from small cores.
    VMs boot with mixed big core/moddle cores + small cores will cause cannot boot up.
    So please remember setting `Core Affinity` after you finished create a new VM. 

!!!notice Core Frequency
    | Core Num |Architecture| Frequency | OpenSSL AES-128-GCM Score |
    |----------|----------|----------|----------|
    | 0  |Cortex-A720| 2.6Ghz | 2564396.37k |
    | 1  |Cortex-A720| 2.6Ghz  | 2554669.74k  |
    | 2  |Cortex-A520| 1.8Ghz | 564560.75k  |
    | 3  |Cortex-A520| 1.8Ghz  | 564230.87k  |
    | 4  |Cortex-A520| 1.8Ghz  | 566136.16k |
    | 5  |Cortex-A520| 1.8Ghz  | 566869.85k  |
    | 6  |Cortex-A720| 2.4Ghz  | 2258687.32k |
    | 7  |Cortex-A720| 2.4Ghz  | 2254432.94k |
    | 8 |Cortex-A720| 2.3Ghz  | 2154856.45k  |
    | 9  |Cortex-A720| 2.3Ghz  | 2157428.74k  |
    | 10  |Cortex-A720| 2.5Ghz  | 2451182.93k  |
    | 11  |Cortex-A720| 2.5Ghz | 2451466.92k  |
