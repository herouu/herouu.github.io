## window <!-- {docsify-ignore-all} -->

### wsl2

#### .wslconfig

```text
[wsl2]
swapfile=D:\\temp\\wsl-swap.vhdx
networkingMode=mirrored
[experimental]

```

#### 迁移至D盘

```bash

1. 停止正在运行的wsl
wsl --shutdown

2. 先查看所有WSL
wsl -l --all  -v

3. 其次导出到D盘
wsl --export Ubuntu d:\ubuntu22.tar

4. 导出完成之后，将原有的Linux注销。
wsl --unregister Ubuntu

5. 然后将导出的文件放到需要保存的地方，进行导入即可
wsl --import Ubuntu d:\wsl2\ubuntu d:\ubuntu22.tar --version 2
```

### git

```bash
 git config --global http.proxy http://127.0.0.1:10809
 git config --global --unset http.proxy
 git config -l --global
 
 # git 电脑端提交失败schannel: next InitializeSecurityContext failed: Unknown error (0x80092013）
 git config --global http.schannelCheckRevoke false
 git config --global http.sslVerify false

# ERROR: failed to receive handshake, SSL/TLS connection failed
git config --global http.sslBackend schannel

# git设置user.name 和 user.email
git config --global user.name "名字"
git config --global user.email "邮箱"
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

### PyPI换源

```bash


# 腾讯源
pip config set global.index-url http://mirrors.cloud.tencent.com/pypi/simple
pip config set global.trusted-host mirrors.cloud.tencent.com


# 清华源
pip config set global.index-url http://pypi.tuna.tsinghua.edu.cn/simple
pip config set global.trusted-host pypi.tuna.tsinghua.edu.cn

# 临时使用
pip install tccli -i http://mirrors.cloud.tencent.com/pypi/simple --trusted-host mirrors.cloud.tencent.com

# 多个源，可以多次执行
pip config set global.extra-index-url https://mirrors.aliyun.com/pypi/simple/

```

### github加速

```vim
# hostname
199.232.4.133 raw.githubusercontent.com
140.82.113.4 github.com

# 加速网站

https://gitmirror.com/
https://ghp.ci/
https://ghps.cc/
https://gh.ddlc.top/

```

### github下载子文件夹工具

* 浏览器插件 GitZip for github

* 网页工具 DownGit

<https://minhaskamal.github.io/DownGit/#/home>

<https://blog.luckly-mjw.cn/tool-show/github-directory-downloader/index.html>

## 其他工具

### 文档

* Doks(<https://getdoks.org/>) hugo模板

### 压测

* jmeter
* httprunner(<https://httprunner.com/>)
* locust(<https://github.com/locustio/locust>)
* tsung(<https://github.com/processone/tsung>)
* go-stress-testing(<https://github.com/link1st/go-stress-testing>)

### tailscale自建节点

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

```text

  "derpMap": {
    "OmitDefaultRegions": false,
    "Regions": {
      "901": {
        "RegionID": 901,
        "RegionCode": "myderp",
        "RegionName": "myderp",
        "Nodes": [
          {
            "Name": "myderp",
            "RegionID": 901,
            "HostName": "frp.top",
            "DERPPort": 6083,
            "InsecureForTests": true
          }
        ]
      }
    }
  }

```

### window网络重置

```bash
netsh winsock reset
```

### install java

```bash
wget -qO - https://adoptopenjdk.jfrog.io/adoptopenjdk/api/gpg/key/public | sudo apt-key add -
sudo add-apt-repository --yes https://mirrors.tuna.tsinghua.edu.cn/AdoptOpenJDK/deb
sudo apt-get update
```

### powershell(cmd)美化

* [starship](https://github.com/starship/starship)

```bash
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

```vim
--add-opens=java.base/jdk.internal.org.objectweb.asm=ALL-UNNAMED
--add-opens=java.base/jdk.internal.org.objectweb.asm.tree=ALL-UNNAMED
-javaagent:D:\Program Files\JetBrains\idea\active-agt-idea.jar
```

* https://idea.hacks.tools/
* https://3.jetbra.in/

[idea](https://herouu.github.io/idea/idea.zip)

### navicat破解

[navicat破解](https://herouu.github.io/navicat/navicat-16.1.15.zip)

### 设计模式

<https://design-patterns.readthedocs.io/zh-cn/latest/index.html>

<https://refactoringguru.cn/>

### vagrant

### 绘图

* kroki.io
* draw.io
* excalidraw 草图
* [graph-easy](https://github.com/ironcamel/Graph-Easy.git)

### github

* 免费的IP查询API  
  `https://github.com/ihmily/ip-info-api/blob/main/README.md`

