### 安装

* docker安装

```bash
bash <(curl -sSL https://linuxmirrors.cn/docker.sh)
```
!>
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http:
//%2Fvar%2Frun%2Fdocker.sock/v1.49/containers/json": dial unix /var/run/docker.sock: connect: permission denied

>[!TIP]上述问题解决

* 添加docker用户组

```bash
# 方式1: 添加用户至docker组
sudo usermod -a -G docker $USER
sudo gpasswd -a $USER docker

[注意]: docker用户组添加后需要关闭当前shell验证

# 查询当前用户所在的group
groups $USER

# 从group中移出用户
sudo gpasswd -d $USER docker
sudo deluser $USER docker


# 方式2(不推荐):
sudo chmod a+rw /var/run/docker.sock
```

* docker添加国内镜像

```bash
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://docker.rainbond.cc",
        "https://dockerproxy.cn"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

参考: <https://gist.github.com/y0ngb1n/7e8f16af3242c7815e7ca2f0833d3ea6>

* 删除停止的容器

```bash
docker container prune
```

* 清空null镜像

```bash
docker image prune -f

docker rmi $(docker images -f "dangling=true" -q)

```

* 跨主机传递image

```bash
docker save -o image.tar image/tag:1.0.0
scp image.tar root@192.168.0.xxx:image.tar
docker load -i image.tar  
```

* 宿主机、容器cp

将主机/www/runoob目录拷贝到容器96f7f14e99ab的/www目录下。

```bash
docker cp /www/runoob 96f7f14e99ab:/www/
```

将主机/www/runoob目录拷贝到容器96f7f14e99ab中，目录重命名为www。

```bash
docker cp /www/runoob 96f7f14e99ab:/www
```

将容器96f7f14e99ab的/www目录拷贝到主机的/tmp目录中。

```bash
docker cp  96f7f14e99ab:/www /tmp/
```

* docker build

```bash
docker build --build-arg="APP_FILE=jrebel-server.upx" -f Dockerfile -t jrebel-server:latest .
```

* docker stats

```bash
docker stats --no-stream happy_rhodes wizardly_tu
```

* docker images

```bash

# 正则匹配
docker images --filter=reference='*/jrebel*'
```

* docker-proxy端口占用无法重启

```log
[root@localhost ~]# ps -ef | grep docker-proxy
root      1329   987  0 11:56 ?        00:00:00 [docker-proxy] <defunct>
root      1333   987  0 11:56 ?        00:00:00 [docker-proxy] <defunct>
root      1345   987  0 11:56 ?        00:00:00 [docker-proxy] <defunct>
root      1349   987  0 11:56 ?        00:00:00 [docker-proxy] <defunct>
root      6820  1764  0 12:38 pts/0    00:00:00 grep --color=auto docker-proxy

systemctl restart docker
```

### 常用镜像

* portainer安装

```bash
docker volume create portainer_data
docker run -d -p 8100:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:2.17.1

https://mirror.ghproxy.com/https://raw.githubusercontent.com/portainer/templates/master/templates-2.0.json
```

* v2rayA(<https://v2raya.org/>)
* serjs/go-socks5-proxy(<https://github.com/serjs/socks5-server>)

```bash
docker run -d --restart=always --name socks5 -p 1090:9090 -e PROXY_PORT=9090 serjs/go-socks5-proxy
```

* v2rayPi(<https://github.com/twotreesus/V2RayPi>)
* calibre-web(<https://hub.docker.com/r/johngong/calibre-web>)
* gost

```bash
docker run -p 1080:1080 -it -d --restart=always --name gost gogost/gost -L socks5://:1080
```

```bash
docker run -d \
 --name=calibre-web \
 -p 8083:8083 \
 -v /home/herouu/data/calibre/config:/config \
 -v /home/herouu/data/calibre/library:/library \
 johngong/calibre-web:latest
```

* gitea

```yaml
version: "3"

networks:
  gitea:
    external: false

services:
  server:
    image: gitea/gitea:1.19.0
    container_name: gitea
    environment:
      - USER_UID=1000
      - USER_GID=1000
    restart: always
    networks:
      - gitea
    volumes:
      - ./gitea:/data
      - /etc/timezone:/etc/timezone:ro
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "3000:3000"
      - "222:22"
```

* nginx

```bash
 docker run --name nginx -p 80:80 -v /etc/nginx:/etc/nginx -v /var/log/nginx:/var/log/nginx -d nginx
```

* nginx proxy manager

```yaml
version: '3.8'
services:
  app:
    image: 'chishin/nginx-proxy-manager-zh:release'
    restart: unless-stopped
    environment:
      - TZ=Asia/Shanghai
    ports:
      - '80:80'
      - '81:81'
      - '443:443'
    volumes:
      - /root/docker/nginx/data:/data
      - /root/docker/nginx/letsencrypt:/etc/letsencrypt
      - /root/app:/root/app
```

* jrebel

```bash
 docker run -d -p 8081:8081 qinjiangbo/jrebel-server
```
