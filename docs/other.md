## window <!-- {docsify-ignore-all} -->

### window系统优化

* chrome浏览器内存
>使用插件Tab Suspender

* vmmem
>Vmmem：是一个系统合成的虚拟进程，用于表示虚拟机小航的内存和CPU资源。 换句话说，您看到的Vmmem消耗大量内存和CPU资源，也就意味着虚拟机正在消耗大量的内存和CPU资源

* Antimalware Service Executable cpu占用高
>使用软件Windows Defender Service关闭Windows Defender

* thinkpad热键驱动
>[SIhotkey-r0yvu37w](https://herouu.github.io/soft/SIhotkey-r0yvu37w.exe)




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
```bash
sudo apt install libgraph-easy-perl
```

```
graph-easy <<< "[ShangHai | ShenZhen | BeiJing || a | b | c]"


+----------+----------+---------+
| ShangHai | ShenZhen | BeiJing |
+----------+----------+---------+
|    a     |     b    |    c    |
+----------+----------+---------+
```

### github

* 免费的IP查询API  
  `https://github.com/ihmily/ip-info-api/blob/main/README.md`

### 不同语言的hello world系统调用

#### c
```bash
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
0.00    0.000000           0         1           read
0.00    0.000000           0         1           write
0.00    0.000000           0         2           close
0.00    0.000000           0         3           fstat
0.00    0.000000           0         7           mmap
0.00    0.000000           0         3           mprotect
0.00    0.000000           0         1           munmap
0.00    0.000000           0         3           brk
0.00    0.000000           0         6           pread64
0.00    0.000000           0         1         1 access
0.00    0.000000           0         1           execve
0.00    0.000000           0         2         1 arch_prctl
0.00    0.000000           0         2           openat
------ ----------- ----------- --------- --------- ----------------
100.00    0.000000                    33         2 total
```
#### go
```bash
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
  0.00    0.000000           0         1           read
  0.00    0.000000           0         1           write
  0.00    0.000000           0         1           close
  0.00    0.000000           0        21           mmap
  0.00    0.000000           0       114           rt_sigaction
  0.00    0.000000           0         8           rt_sigprocmask
  0.00    0.000000           0         2           madvise
  0.00    0.000000           0         3           clone
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         3           fcntl
  0.00    0.000000           0         2           sigaltstack
  0.00    0.000000           0         1           arch_prctl
  0.00    0.000000           0         1           gettid
  0.00    0.000000           0         2           futex
  0.00    0.000000           0         1           sched_getaffinity
  0.00    0.000000           0         1           openat
------ ----------- ----------- --------- --------- ----------------
100.00    0.000000                   163           total
```
#### java native
```bash
------ ----------- ----------- --------- --------- ----------------
  0.00    0.000000           0        39           read
  0.00    0.000000           0         1           write
  0.00    0.000000           0        26           close
  0.00    0.000000           0        22           fstat
  0.00    0.000000           0         2           lseek
  0.00    0.000000           0        23           mmap
  0.00    0.000000           0         8           mprotect
  0.00    0.000000           0         3           munmap
  0.00    0.000000           0         3           brk
  0.00    0.000000           0         8           rt_sigaction
  0.00    0.000000           0         1           rt_sigprocmask
  0.00    0.000000           0         8           pread64
  0.00    0.000000           0         1         1 access
  0.00    0.000000           0         3           socket
  0.00    0.000000           0         1           setsockopt
  0.00    0.000000           0         1           clone
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         2           sysinfo
  0.00    0.000000           0         2         1 arch_prctl
  0.00    0.000000           0         2           gettid
  0.00    0.000000           0         1           futex
  0.00    0.000000           0         5           sched_getaffinity
  0.00    0.000000           0         2           getdents64
  0.00    0.000000           0         1           set_tid_address
  0.00    0.000000           0        24           openat
  0.00    0.000000           0         1           set_robust_list
  0.00    0.000000           0         5           prlimit64
------ ----------- ----------- --------- --------- ----------------
100.00    0.000000                   196         2 total
```
#### rust
```bash
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
  0.00    0.000000           0         8           read
  0.00    0.000000           0         1           write
  0.00    0.000000           0         6           close
  0.00    0.000000           0         6           fstat
  0.00    0.000000           0         1           poll
  0.00    0.000000           0        22           mmap
  0.00    0.000000           0         7           mprotect
  0.00    0.000000           0         2           munmap
  0.00    0.000000           0         3           brk
  0.00    0.000000           0         7           rt_sigaction
  0.00    0.000000           0         1           rt_sigprocmask
  0.00    0.000000           0         8           pread64
  0.00    0.000000           0         1         1 access
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         3           sigaltstack
  0.00    0.000000           0         2         1 arch_prctl
  0.00    0.000000           0         1           sched_getaffinity
  0.00    0.000000           0         1           set_tid_address
  0.00    0.000000           0         6           openat
  0.00    0.000000           0         1           set_robust_list
  0.00    0.000000           0         2           prlimit64
------ ----------- ----------- --------- --------- ----------------
100.00    0.000000                    90         2 total
```
#### zig
```bash
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
0.00    0.000000           0         1           write
0.00    0.000000           0         5           rt_sigaction
0.00    0.000000           0         1           execve
0.00    0.000000           0         1           arch_prctl
0.00    0.000000           0         2           prlimit64
------ ----------- ----------- --------- --------- ----------------
100.00    0.000000                    10           total
```

#### vlang
```bash
% time     seconds  usecs/call     calls    errors syscall
------ ----------- ----------- --------- --------- ----------------
 16.67    0.000031           3         8           read
 16.67    0.000031           5         6           mprotect
 15.05    0.000028           1        26           mmap
 13.98    0.000026           3         8           openat
  8.60    0.000016           8         2           munmap
  5.91    0.000011           1         9           rt_sigaction
  4.30    0.000008           1         8           close
  4.30    0.000008           1         8           fstat
  3.76    0.000007           7         1           write
  2.69    0.000005           1         3           brk
  2.15    0.000004           1         3           rt_sigprocmask
  1.61    0.000003           1         2           futex
  1.61    0.000003           1         2           prlimit64
  1.08    0.000002           2         1           sched_getaffinity
  1.08    0.000002           2         1           set_tid_address
  0.54    0.000001           1         1           set_robust_list
  0.00    0.000000           0         8           pread64
  0.00    0.000000           0         1         1 access
  0.00    0.000000           0         1           execve
  0.00    0.000000           0         2         1 arch_prctl
------ ----------- ----------- --------- --------- ----------------
100.00    0.000186                   101         2 total
```
结论: 单从系统调用次数上由小到大看zig> c> rust > vlang > go >java