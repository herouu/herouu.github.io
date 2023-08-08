## window

### git

```
 git config --global http.proxy http://127.0.0.1:10809
 git config --global --unset http.proxy
 git config -l --global
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

### PyPI换源

```
pip config set global.extra-index-url "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple"

```

### github地址解析

```
199.232.4.133 raw.githubusercontent.com
140.82.113.4 github.com
```

## 其他工具

### 文档

* Doks(<https://getdoks.org/>) hugo模板

### 压测

* jmeter
* httprunner(<https://httprunner.com/>)
* locust(<https://github.com/locustio/locust>)
* tsung(<https://github.com/processone/tsung>)
* go-stress-testing(<https://github.com/link1st/go-stress-testing>)

### scoop

#### scoop安装

```bash
Set-ExecutionPolicy RemoteSigned -scope CurrentUser

$env:SCOOP='D:\Applications\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')

Invoke-Expression (New-Object System.Net.WebClient).DownloadString('https://get.scoop.sh')
# or shorter
iwr -useb get.scoop.sh | iex
```

```
# install
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
$env:SCOOP='D:\Applications\Scoop'
[Environment]::SetEnvironmentVariable('SCOOP', $env:SCOOP, 'User')
# irm -useb get.scoop.sh | iex
irm https://ghproxy.com/raw.githubusercontent.com/lzwme/scoop-proxy-cn/master/install.ps1 | iex

# config
scoop config SCOOP_REPO https://ghproxy.com/github.com/ScoopInstaller/Scoop
scoop bucket rm main
scoop bucket add main https://ghproxy.com/github.com/ScoopInstaller/Main
scoop bucket add spc https://ghproxy.com/https://github.com/lzwme/scoop-proxy-cn

# show help
scoop help

# install 7zip、aria2、scoop-search...
scoop install spc/7zip spc/aria2 spc/scoop-search
```


#### bucket自定义

* Scoop bucket(<https://ericzong.github.io/posts/tool-scoop-bucket.html>)


#### tailscale自建节点

https://icloudnative.io/posts/custom-derp-servers/#%E4%BD%BF%E7%94%A8%E7%BA%AF-ip

```
docker run -d \
--name derper \
--restart always  \
-p 5443:443 \
-p 3478:3478/udp  \
-d hausen1012/ip_derper:latest
```
#### install java

```bash
wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add -
sudo add-apt-repository --yes https://mirrors.tuna.tsinghua.edu.cn/AdoptOpenJDK/deb
sudo apt-get update
```