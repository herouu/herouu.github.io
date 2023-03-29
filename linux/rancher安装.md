## rancher开发环境安装
* ubuntu/centos 换源脚本
```bash
bash <(curl -sSL https://gitee.com/SuperManito/LinuxMirrors/raw/main/ChangeMirrors.sh)
```

* docker安装
```bash
curl -sSL https://get.docker.com/ | CHANNEL=stable sh
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
curl -sSL https://get.daocloud.io/docker | sh
```

* 添加docker用户组
```bash
sudo usermod -a -G docker $USER
```

* docker添加国内镜像
```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://dockerproxy.com",
        "https://docker.mirrors.ustc.edu.cn",
        "https://docker.nju.edu.cn"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
参考: https://gist.github.com/y0ngb1n/7e8f16af3242c7815e7ca2f0833d3ea6

* portainer安装
```bash
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:2.17.1
```

* rancher安装
```bash
docker run -d --name=rancher --restart=unless-stopped \
  -p 8080:80 -p 8443:443 \
  -v /opt/rancher:/var/lib/rancher \
  -v /var/log/rancher/auditlog:/var/log/auditlog \
  -e CATTLE_TLS_MIN_VERSION="1.0" \
  --privileged \
  rancher/rancher:v2.7.1
```

* k8s安装nacos https://artifacthub.io/
```bash
helm repo add ygqygq2 https://ygqygq2.github.io/charts/
helm install my-nacos --set replicaCount=2 ygqygq2/nacos --version 2.1.4
```

* k3s
```bash
curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | INSTALL_K3S_MIRROR=cn sh -
```

* multipass
```bash
multipass launch -n herouu -c 2 -d 50G -m 3G 20.04
```

## docker

### 常用命令
```bash
# 清空null镜像
docker image prune -f
```

### 有用的镜像

* v2rayA(https://v2raya.org/)
* serjs/go-socks5-proxy(https://github.com/serjs/socks5-server)


## linux

### setting proxy
```bash
export http_proxy=http://192.168.0.1:10809
export https_proxy=http://192.168.0.1:10809
unset http_proxy
unset https_proxy
```

### setting static address
```bash
nano /etc/sysconfig/network-scripts/ifcfg-enp0s3 #enp0s3为网卡名


BOOTPROTO="static"        #dhcp改为static

DNS1=192.168.0.1          #NDS
IPADDR=192.168.0.171      #IP地址
GATEWAY=192.168.0.1       #网关
PREFIX=24

```
修改后重启网络

### restart network
```bash
# centos7
sudo systemctl restart network
# rocky8
sudo systemctl restart NetworkManager
```

### install nodejs
```bash
curl -sL https://deb.nodesource.com/setup_14.x | sudo bash
npm config set registry https://registry.npm.taobao.org 
npm config get registry
```

### install java
```bash
wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add -
sudo add-apt-repository --yes https://mirrors.tuna.tsinghua.edu.cn/AdoptOpenJDK/deb
sudo apt-get update
```

### 修改主机名
```bash
hostnamectl set-hostname 主机名
```

## window

### scoop安装 
```bash
Set-ExecutionPolicy RemoteSigned -scope CurrentUser

$env:SCOOP='D:\Applications\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')

Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
# or shorter
iwr -useb get.scoop.sh | iex
```

### cmd代理
```bash
set http_proxy=http://127.0.0.1:10809
set https_proxy=http://127.0.0.1:10809
```
### powershell代理
```bash
$env:HTTP_PROXY="http://127.0.0.1:10809"
$env:HTTPS_PROXY="http://127.0.0.1:10809"
```



## 常用镜像源

### maven 
```
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```


## 其他工具

* Doks(https://getdoks.org/) 文档生成工具,类似hugo,hexo