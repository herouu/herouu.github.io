## window

### git

```shell
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

```xml
<mirror>
    <id>aliyunmaven</id>
    <mirrorOf>*</mirrorOf>
    <name>阿里云公共仓库</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

### PyPI换源

```bash
pip config set global.extra-index-url "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple"

```

### github加速

```text
# hostname
199.232.4.133 raw.githubusercontent.com
140.82.113.4 github.com

# 加速网站

https://gitmirror.com/
https://ghproxy.com/
https://ghps.cc/
https://gh.ddlc.top/

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

```bash
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

<https://icloudnative.io/posts/custom-derp-servers/#%E4%BD%BF%E7%94%A8%E7%BA%AF-ip>

* 免认证docker image

```bash
docker run -d \
--name derper \
--restart always  \
-p 5443:443 \
-p 3478:3478/udp  \
-d hausen1012/ip_derper:latest
```

* 配置tailscale -> Access Controls

```json
 "derpMap": {
  "OmitDefaultRegions": false,
  "Regions": {
   "901": {
    "RegionID":   901,
    "RegionCode": "myderp",
    "RegionName": "myderp",
    "Nodes": [{
     "Name":             "myderp",
     "RegionID":         901,
     "HostName":         "frp.top",
     "DERPPort":         6083,
     "InsecureForTests": true,
    }],
   },
  },
 }

```

#### window网络重置

```shell
netsh winsock reset
```

#### install java

```bash
wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add -
sudo add-apt-repository --yes https://mirrors.tuna.tsinghua.edu.cn/AdoptOpenJDK/deb
sudo apt-get update
```

#### powershell(cmd)美化

* [starship](https://github.com/starship/starship)

```shell
# cmd 
scoop install clink 
# 创建lua clink\starship.lua,文件中添加
load(io.popen('starship init cmd'):read("*a"))()

# powershell
$PROFILE 新增
Invoke-Expression (&starship init powershell)
```

### winsw

`scoop install winsw-pre`

* gost

```xml
<service>
  <id>gost-proxy</id>
  <executable>D:\local\bin\gost.exe</executable>
  <arguments>-L :1080</arguments>
</service>
```

* virtualbox

```xml
<service>
  <id>virtualbox</id>
  <executable>C:\Users\mlamp\scoop\apps\virtualbox-np\current\VirtualBoxVM.exe</executable>
  <startarguments>--startvm rockylinux --type headless</startarguments>
</service>
```

### idea激活

```text
--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED
--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED
-javaagent:D:\Program Files\JetBrains\idea\active-agt-idea.jar
```

[idea](/idea/idea.zip)

### navicat破解

[navicat破解](/navicat/navicat-16.1.15.zip)
