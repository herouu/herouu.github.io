## window

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
```

### gitea maven私服

* settings.xml

```xml
<servers>
    <server>
      <id>gitea</id>
      <configuration>
        <httpHeaders>
          <property>
            <name>Authorization</name>
            <value>token {access_token}</value>
          </property>
        </httpHeaders>
      </configuration>
    </server>
  </servers>

```

* pom.xml

```xml
    <repositories>
        <repository>
            <id>gitea</id>
            <url>https://home.herouu.top/git/api/packages/herouu/maven</url>
        </repository>
    </repositories>
    <distributionManagement>
        <repository>
            <id>gitea</id>
            <url>https://home.herouu.top/git/api/packages/herouu/maven</url>
        </repository>
        <snapshotRepository>
            <id>gitea</id>
            <url>https://home.herouu.top/git/api/packages/herouu/maven</url>
        </snapshotRepository>
    </distributionManagement>
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

* 获取依赖源码及文档

```bash
mvn dependency:sources -DdownloadSources=true -DdownloadJavadocs=true
```

### PyPI换源

```bash
pip config set global.extra-index-url "https://mirrors.tuna.tsinghua.edu.cn/pypi/web/simple"

```

### github加速

```vim
# hostname
199.232.4.133 raw.githubusercontent.com
140.82.113.4 github.com

# 加速网站

https://gitmirror.com/
https://mirror.ghproxy.com/
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
# 国内使用scoop
https://gitee.com/glsnames/scoop-installer
https://github.com/lzwme/scoop-proxy-cn

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

```bash
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

[idea](https://herouu.github.io/idea/idea.zip)

### navicat破解

[navicat破解](https://herouu.github.io/navicat/navicat-16.1.15.zip)

### 设计模式

<https://design-patterns.readthedocs.io/zh-cn/latest/index.html>

<https://refactoringguru.cn/>
