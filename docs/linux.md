### ubuntu/centos 换源脚本

```bash
bash <(curl -sSL https://linuxmirrors.cn/main.sh)
```

### setting proxy

```bash
export http_proxy=http://192.168.0.1:10809
export https_proxy=http://192.168.0.1:10809
unset http_proxy
unset https_proxy
```

### setting static address

推荐使用nmtui命令

```bash

nano /etc/sysconfig/network-scripts/ifcfg-enp0s3 #enp0s3为网卡名

BOOTPROTO="static"        #dhcp改为static

DNS1=192.168.0.1          #NDS
IPADDR=192.168.0.171      #IP地址
GATEWAY=192.168.0.1       #网关
PREFIX=24

修改后重启网络
```

```bash
# 连接wifi
nmcli dev wifi
nmcli dev wifi connect <SSID>  password <password>
# 修改静态ip
nmcli connection modify <SSID> ipv4.method manual ipv4.addresses 192.168.0.250/24 ipv4.gateway 192.168.0.1 ipv4.dns 192.168.0.1 connection.autoconnect yes
# 添加dns
nmcli con mod <SSID> +ipv4.dns 114.114.114.114
# 重启网络
nmcli conn up <SSID>
```

### 重启网卡

```
 ip link set wlp5s0 down
 ip link set wlp5s0 up
```

#### error and fix

* SIOCSIFFLAGS: Operation not possible due to RF-kill

```
rfkill list
rfkill unblock all
```

### restart network

```bash
# centos7
sudo systemctl restart NetworkManager
```

### 时间同步

```bash

# 1.安装ntp
yum install ntp

# 2.修改文件
nano /etc/ntp.conf
# new(新增)
server ntp1.aliyun.com prefer #prefer:优先使用该时间服务器
server ntp2.aliyun.com

# old(原有)
server 0.centos.pool.ntp.org iburst #iburst:当一个运程NTP服务器不可用时，向它发送一系列的并发包进行检测
server 1.centos.pool.ntp.org iburst
server 2.centos.pool.ntp.org iburst
server 3.centos.pool.ntp.org iburst

# 3.查看时区
timedatectl status

# 4.设置时区
timedatectl set-timezone Asia/Shanghai
# 或者
cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# 5.时间同步
ntpdate ntp1.aliyun.com

# 6.启动ntp
systemctl start ntpd

# 7.开机启动
systemctl enable ntpd
```

### 修改主机名

```bash
hostnamectl set-hostname 主机名
```

### multipass

```bash
multipass launch -n herouu -c 2 -d 50G -m 3G 20.04
```

### centos7升级rockylinux8

参考 https://blog.csdn.net/qq_44379042/article/details/130981771

```
应答  /var/log/leapp/answerfile
leapp answer --section remove_pam_pkcs11_module_check.confirm=True
```

### vps测速

```

* 带宽
curl -s https://packagecloud.io/install/repositories/ookla/speedtest-cli/script.rpm.sh | sudo bash
sudo yum install speedtest


* 
wget -O jcnfbox.sh https://raw.githubusercontent.com/Netflixxp/jcnf-box/main/jcnfbox.sh && chmod +x jcnfbox.sh && clear && ./jcnfbox.sh


* 综合测试工具
wget -qO- https://raw.githubusercontent.com/LemonBench/LemonBench/main/LemonBench.sh | bash -s -- --fast

* ping值
https://tools.ipip.net/newping.php
https://ping.pe/

```

### linux安全

* ssh:notty
`lastb`命令,出现大量的输出说明你的服务器正在遭受黑客们的暴力破解

```
* 修改ssh默认端口
* 禁止root登录
* 使用ssh免秘钥登录
* 禁用ip
```

* ssh连接经常断线,开启如下配置
```
TCPKeepAlive yes
ClientAliveInterval 360
ClientAliveCountMax 10
```

### scp

```
# 从本地复制到远程
scp -r local_folder remote_username@remote_ip:remote_folder

# 从远处复制到本地
scp -r remote_username@remote_ip:remote_folder local_folder

```

### frp

```
wget -O frpc https://nya.globalslb.net/natfrp/client/frpc/0.45.0-sakura-7/frpc_linux_amd64
chmod u+x /root/frpc/frpc && ln -s /root/frpc/frpc /usr/local/bin/frpc
```

### 文件占用
```
# 显示包括子目录在内的最大文件及文件夹
du -Sh | sort -rh | head -5

# 显示文件夹 
du -h | sort -rh  | head -5
 
du --max-depth=1 -h / | sort -rh  | head -5
```

### 磁盘扩容

```
fdisk -l
fdisk /dev/vdb

pvcreate /dev/sdb1
vgdisplay

vgextend centos /dev/sdb1
lvextend -L +100G /dev/mapper/centos-root

xfs_growfs /dev/mapper/centos-root
df -lh
```
参考 https://www.cnblogs.com/lvzhenjiang/p/15887422.html

### cpu
```
* 查看当前所有CPU的信息
cpupower -c all frequency-info

* 查看某个CPU的信息
cpupower -c 1 frequency-info

* 查看当前cpu可用的策略
cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_available_governors

* 查看当前cpu生效的策略
 cat /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor

* 设置cpu模式 performance-性能模式 powersave-省电模式 conservative-保守模式
cpupower -c all frequency-set -g powersave

* 查看cpu频率
cat /proc/cpuinfo | grep "cpu MHz*"
```


### 学习文档

[Linux工具快速教程](https://linuxtools-rst.readthedocs.io/zh_CN/latest/index.html)