### command

* docker安装

```bash
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

```
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
docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:2.17.1
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

```
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

```
 docker run --name nginx -p 80:80 -v /etc/nginx:/etc/nginx -v /var/log/nginx:/var/log/nginx -d nginx
```
