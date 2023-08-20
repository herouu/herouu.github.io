### 安装

* docker安装

```bash
bash <(curl -sSL https://linuxmirrors.cn/docker.sh)
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
        "https://mirror.ccs.tencentyun.com",
        "https://docker.nju.edu.cn"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```

参考: <https://gist.github.com/y0ngb1n/7e8f16af3242c7815e7ca2f0833d3ea6>

* 清空null镜像

```bash
docker image prune -f
```

* 跨主机传递image

```bash
docker save -o image.tar image/tag:1.0.0
scp image.tar root@192.168.0.xxx:image.tar
docker load -i image.tar  
```

* docker-proxy端口占用无法重启

```text
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
```

* v2rayA(<https://v2raya.org/>)
* serjs/go-socks5-proxy(<https://github.com/serjs/socks5-server>)
* v2rayPi(<https://github.com/twotreesus/V2RayPi>)
* calibre-web(<https://hub.docker.com/r/johngong/calibre-web>)

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
