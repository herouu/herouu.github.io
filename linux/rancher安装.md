## rancher开发环境安装

* ubuntu 换源脚本
```
curl -sSL https://gitee.com/SuperManito/LinuxMirrors/raw/main/ChangeMirrors.sh | sudo bash
```

* docker安装
```
curl -sSL https://get.docker.com/ | CHANNEL=stable sh
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
curl -sSL https://get.daocloud.io/docker | sh
```

* 添加docker用户组
```
sudo groupadd docker
sudo addgroup $USER docker
```

* docker添加国内镜像
```
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
```
docker volume create portainer_data
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:2.17.1
```

* rancher安装
```
docker run -d --name=rancher --restart=unless-stopped \
  -p 8080:80 -p 8443:443 \
  -v /opt/rancher:/var/lib/rancher \
  -v /var/log/rancher/auditlog:/var/log/auditlog \
  -e CATTLE_TLS_MIN_VERSION="1.0" \
  --privileged \
  rancher/rancher:v2.7.1
```

* k8s安装nacos https://artifacthub.io/
```
helm repo add ygqygq2 https://ygqygq2.github.io/charts/
helm install my-nacos --set replicaCount=2 ygqygq2/nacos --version 2.1.4
```

* k3s
```
curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | INSTALL_K3S_MIRROR=cn sh -
```

* multipass
```
multipass launch -n herouu -c 2 -d 50G -m 3G 20.04
```



## k3s
## 设置root用户密码 
sudo passwd root
## 添加用户sudo权限
chmod u+w /etc/sudoers

### linux换源脚本
bash <(curl -sSL https://gitee.com/SuperManito/LinuxMirrors/raw/main/ChangeMirrors.sh)
### docker安装
```
wget https://gitee.com/SuperManito/LinuxMirrors/raw/main/DockerInstallation.x -O install.x && chmod +x install.x && ./install.x
```
### k3s 安装
```
curl -sfL http://rancher-mirror.cnrancher.com/k3s/k3s-install.sh | INSTALL_K3S_MIRROR=cn K3S_KUBECONFIG_MODE=644 sh -s - --docker
```
### 配置环境变量
```
vim /etc/profile
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
source /etc/profile
```

### helm 设置国内镜像 
```
helm repo remove stable
helm repo add stable http://mirror.azure.cn/kubernetes/charts/
helm repo update
```
### docker pull 设置代理
```
mkdir /etc/systemd/system/docker.service.d
vim  http-proxy.conf 
```

```
[Service]
Environment="HTTPS_PROXY=http://192.168.43.141:10809"

[Service]
Environment="HTTP_PROXY=http://proxy.example.com:80/"
Environment="HTTPS_PROXY=http://proxy.example.com:80/"
```

```
# Flush changes:
sudo systemctl daemon-reload
# Verify that the configuration has been loaded:
sudo systemctl show --property Environment docker
sudo systemctl show --property Environment docker
sudo systemctl restart docker
```

###  解决docker ps sudo命令
```
sudo gpasswd -a ${USER} docker 
newgrp docker
```

## kubectl 命令

### 获取所有pod状态
kubectl get pods --all-namespaces

### 查询pod描述
kubectl describe pods -n openfaas nats-697d4bd9fd-gbmrf

## openfaas
密码
```
echo $(kubectl -n openfaas get secret basic-auth -o jsonpath="{.data.basic-auth-password}" | base64 --decode)

2EAQdSwxtrlb
```

- 参考文档

https://stackoverflow.com/questions/23111631/cannot-download-docker-images-behind-a-proxy
Kubernetes中文文档 https://hardocs.com/d/kubernetes/149-kubectl_describe.html

https://blog.csdn.net/ilfrost/article/details/105921549

https://www.jianshu.com/p/7d11d9e8792a


## nodejs
curl -sL https://deb.nodesource.com/setup_14.x | sudo bash
npm config set registry https://registry.npm.taobao.org 
npm config get registry


## maven 
```
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
https://developer.aliyun.com/mirror/maven
```
### adoptjdk install
```
wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add -
sudo add-apt-repository --yes https://mirrors.tuna.tsinghua.edu.cn/AdoptOpenJDK/deb
sudo apt-get update
```
# linux 代理工具 
apt-get install proxychains
配置/etc/proxychains.conf


# 代理设置
### linux
```
export http_proxy=http://192.168.0.7:10809
export https_proxy=http://192.168.0.7:10809
unset http_proxy
unset https_proxy
```
### window
#### cmd
```
set http_proxy=http://127.0.0.1:10809
set https_proxy=http://127.0.0.1:10809
```
### powershell
```
$env:HTTP_PROXY="http://127.0.0.1:10809"
$env:HTTPS_PROXY="http://127.0.0.1:10809"
```

### scoop安装 
```
powershell 代理

Set-ExecutionPolicy RemoteSigned -scope CurrentUser

$env:SCOOP='D:\Applications\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')

Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
# or shorter
iwr -useb get.scoop.sh | iex
```

### 重启网络
```
sudo systemctl restart NetworkManager
```