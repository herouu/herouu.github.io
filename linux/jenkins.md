### 安装

```bash
 docker run -d \
-v /var/jenkins_home:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
-v /usr/bin/docker:/usr/bin/docker \
-v /etc/localtime:/etc/localtime \
-p 10340:8080 -p 10341:50000 \
jenkins/jenkins:lts-jdk11
```

### 插件

* Docker Pipeline
* Extended Choice Parameter

### pipeline

```bash

```
