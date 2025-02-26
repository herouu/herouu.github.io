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

```bash
 ip link set wlp5s0 down
 ip link set wlp5s0 up
```

#### error and fix

* SIOCSIFFLAGS: Operation not possible due to RF-kill

```bash
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

### centos7升级rockylinux8

参考 <https://blog.csdn.net/qq_44379042/article/details/130981771>

```bash
应答  /var/log/leapp/answerfile
leapp answer --section remove_pam_pkcs11_module_check.confirm=True
```

### vps测速

```bash

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

```vim
* 修改ssh默认端口
* 禁止root登录
* 使用ssh免秘钥登录
* 禁用ip
```

* ssh连接经常断线,开启如下配置

```vim
TCPKeepAlive yes
ClientAliveInterval 360
ClientAliveCountMax 10
```

### scp

```bash
# 从本地复制到远程
scp -r local_folder remote_username@remote_ip:remote_folder

# 从远处复制到本地
scp -r remote_username@remote_ip:remote_folder local_folder

```

### frp

```bash
wget -O frpc https://nya.globalslb.net/natfrp/client/frpc/0.45.0-sakura-7/frpc_linux_amd64
chmod u+x /root/frpc/frpc && ln -s /root/frpc/frpc /usr/local/bin/frpc
```

### 文件占用

```bash
# 显示包括子目录在内的最大文件及文件夹
du -Sh / | sort -rh | head -5

# 显示文件夹 
du -h | sort -rh  | head -5
 
du --max-depth=1 -h / | sort -rh  | head -5
```

### 磁盘扩容

```bash
fdisk -l
fdisk /dev/vdb

pvcreate /dev/sdb1
vgdisplay

vgextend centos /dev/sdb1
lvextend -L +100G /dev/mapper/centos-root

xfs_growfs /dev/mapper/centos-root
df -lh
```

参考 <https://www.cnblogs.com/lvzhenjiang/p/15887422.html>

### cpu

```bash
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

### DNS

#### 公共DNS

>[!NOTE|label:阿里]
> 223.5.5.5 223.6.6.6
>
> * 阿里DoH  
<https://dns.alidns.com/dns-query>  
https://alidns_ip/dns-query

>[!NOTE|label:腾讯]
>119.29.29.29 182.254.116.116
>
> * 腾讯DoH  
<https://doh.pub/dns-query>

#### adguardhome  

```docker
docker run -d --name adguardhome \
    --restart unless-stopped \
    -v /my/own/workdir:/opt/adguardhome/work \
    -v /my/own/confdir:/opt/adguardhome/conf \
    --net=host \
    -d adguard/adguardhome
# 详细参数请参考 https://hub.docker.com/r/adguard/adguardhome

```

#### mosdns

* [mosdns](https://github.com/IrineSistiana/mosdns)
* [easymosdns](https://github.com/pmkol/easymosdns)

参考: [利用 Mosdns 和 AdGuardHome 搭建自己的 DNS](https://www.xukecheng.tech/use-mosdns-and-adguardhome-to-build-your-own-dns)


#### DNS安全协议

* DNSSEC
* DNSCrypt
* DNS over TLS
* DNS over HTTPS

### systemd

* [simple-online-systemd-service-generator](https://techoverflow.net/2019/03/11/simple-online-systemd-service-generator/)

```
[Unit]
Description=example

[Service]
ExecStart=/usr/bin/nodejs
WorkingDirectory=/root/example
Restart=always

[Install]
WantedBy=multi-user.target
```

* `sudo cp example.service /etc/systemd/system`
* `sudo systemctl enable example.service` (auto-start on boot)
* `sudo systemctl start example.service` (start right now)
* To view the latest logs, use `sudo journalctl -xfu example.service`
* To view the current service status, use `sudo systemctl status example.service`

### 学习文档

[Linux工具快速教程](https://linuxtools-rst.readthedocs.io/zh_CN/latest/index.html)

### 命令行代理

* [proxychains](https://github.com/rofl0r/proxychains-ng)
* [graftcp](https://github.com/hmgle/graftcp.git)
  - 原理：https://raw.githubusercontent.com/hmgle/graftcp/master/README.zh-CN.md
  - 根据该原理的golang实现：https://github.com/mzz2017/gg.git
* [nsproxy](https://github.com/nlzy/nsproxy.git)
  - 原理：https://github.com/nlzy/nsproxy/blob/master/README

### perf命令

* 安装 `sudo apt install linux-perf`
* 报错
  > $> perf record  
  Error:  
  You may not have permission to collect stats.  
  Consider tweaking /proc/sys/kernel/perf_event_paranoid:  
  -1 - Not paranoid at all  
  0 - Disallow raw tracepoint access for unpriv  
  1 - Disallow cpu events for unpriv  
  2 - Disallow kernel profiling for unpriv  
  
* 解决
  > sudo sysctl -w kernel.perf_event_paranoid=-1  
    或者  
    sudo sh -c 'echo "kernel.perf_event_paranoid=-1" >> /etc/sysctl.conf'  
    sudo sysctl -p

### 性能优化

#### 明明用户CPU使用率已经高达80%，但我却怎么都找不到是哪个进程的问题?

* 第一个原因，进程在不停地崩溃重启，比如因为段错误、配置错误等等，这时，进程在退出后可能又被监控系统自动重启了。

* 第二个原因，这些进程都是短时进程，也就是在其他应用内部通过 exec 调用的外面命令。这些命令一般都只运行很短的时间就会结束，你很难用 top 这种间隔时间比较长的工具发现（上面的案例，我们碰巧发现了）

##### bcc工具install

* https://github.com/iovisor/bcc.git 

`sudo apt-get install bpfcc-tools linux-headers-$(uname -r)`

##### execsnoop-bpfcc

```
stress           1532552 1532549   0 /usr/local/bin/stress -t 1 -d 1
stress           1532551 1532550   0 /usr/local/bin/stress -t 1 -d 1
```

##### 找到父进程 docker容器启动的php-fpm
`pstree | grep stress` 

```
 |-containerd-shim-+-php-fpm---5*[php-fpm---sh---stress---stress]
```

##### 拷贝源码到本地

`docker cp phpfpm:/app .`

##### grep 查找看看是不是有代码在调用stress命令

`grep stress -r app`
```
app/index.php:// fake I/O with stress (via write()/unlink()).
app/index.php:$result = exec("/usr/local/bin/stress -t 1 -d 1 2>&1", $output, $status);
```
##### 查看父进程调用stress代码

`cat app/index.php`
```
<?php
// fake I/O with stress (via write()/unlink()).
$result = exec("/usr/local/bin/stress -t 1 -d 1 2>&1", $output, $status);
if (isset($_GET["verbose"]) && $_GET["verbose"]==1 && $status != 0) {
  echo "Server internal error: ";
  print_r($output);
} else {
  echo "It works!";
}
```

