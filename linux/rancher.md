### rancher高可用安装

* k3s安装

```bash
curl -sfL https://rancher-mirror.rancher.cn/k3s/k3s-install.sh | INSTALL_K3S_MIRROR=cn INSTALL_K3S_VERSION=v1.24.10+k3s1 sh -s -
```

* helm3.2.4安装

```bash
# helm国内镜像
wget https://mirrors.huaweicloud.com/helm/v3.2.4/helm-v3.2.4-linux-amd64.tar.gz
mkdir -p helm && tar -zxvf helm-v3.2.4-linux-amd64.tar.gz -C helm
mv helm/linux-amd64/helm /usr/local/sbin/helm
```

* rancher安装

```bash
kubectl create namespace cattle-system
kubectl create namespace cert-manager
# 如需删除
kubectl delete namespace cattle-system
kubectl delete namespace cert-manager 
```

```bash
helm repo add rancher-stable https://rancher-mirror.rancher.cn/server-charts/stable
helm repo add jetstack https://charts.jetstack.io
helm repo update
```

```bash
helm install \
  cert-manager jetstack/cert-manager \
  --namespace cert-manager \
  --create-namespace \
  --version v1.11.0 \
  --set installCRDs=true
```

```bash
helm install rancher rancher-stable/rancher \
 --version 2.7.1 \
 --namespace cattle-system \
 --set rancherImage=registry.cn-hangzhou.aliyuncs.com/rancher/rancher \
 --set hostname=rancher.herouu \
 --set bootstrapPassword=admin
```

* 遇到问题

1. (RHEL7/CentOS7: User namespaces disabled; add 'user_namespace.enable=1' to boot command line) (fail)

```bash
Optional Features:
- CONFIG_USER_NS: enabled
  (RHEL7/CentOS7: User namespaces disabled; add 'user_namespace.enable=1' to boot command line) (fail)
  
# 解决
grubby --args="user_namespace.enable=1" --update-kernel="$(grubby --default-kernel)"
reboot  
  
```

2. Error: Kubernetes cluster unreachable

```bash
helm list --all-namespaces
Error: Kubernetes cluster unreachable
# 添加环境变量解决
export KUBECONFIG=/etc/rancher/k3s/k3s.yaml
```

```bash
Linux加载环境变量的顺序如下：
/etc/environment
/etc/profile
/etc/bash.bashrc
/etc/profile.d/test.sh
~/.profile
~/.bashrc
```

3. 替换rancher local集群仓库地址

``` bash
https://git.rancher.io 
# to
https://github.com/rancher
https://gitee.com/rancher
```

4. rancher helm-operation  ImagePullBackOff  

```bash
# 设置镜像仓库
sudo tee /etc/rancher/k3s/registries.yaml <<-'EOF'
mirrors:
  docker.io:
    endpoint:
        - https://docker.m.daocloud.io
        - https://dockerproxy.com
        - https://docker.nju.edu.cn
EOF
systemctl restart k3s

```

5. iptables v1.8.2 fail

```bash
System:
- /sbin iptables v1.8.2 (nf_tables): should be older than v1.8.0, newer than v1.8.3, or in legacy mode (fail)
- swap: should be disabled
- routes: ok

sudo update-alternatives --config iptables
选择
/usr/sbin/iptables-legacy 
```

6. debian   Error: failed to create containerd

```bash
sudo apt install apparmor apparmor-utils
```

7. rancher忘记密码,重置admin密码

```bash
kubectl -n cattle-system exec $(kubectl -n cattle-system get pods -l app=rancher | grep '1/1' | head -1 | awk '{ print $1 }') -- reset-password
eg:
Wq3rv0jTt6UqKjNW0zoc
```

8. error: error loading config file "/etc/rancher/k3s/k3s.yaml": open /etc/rancher/k3s/k3s.yaml: permission denied

```bash
echo K3S_KUBECNFIG_MODE=\"644\" >> sudo /etc/systemd/system/k3s.service.env
# 注意双引号""
```

### rancher单机server安装

```bash
docker run -d --name=rancher --restart=unless-stopped \
  -p 8080:80 -p 8443:443 \
  -v /opt/rancher:/var/lib/rancher \
  -v /var/log/rancher/auditlog:/var/log/auditlog \
  -e CATTLE_TLS_MIN_VERSION="1.0" \
  --privileged \
  rancher/rancher:lastest
```

参考: <http://note.eta.pub/2023/03/09/rancher-v2-7-cluster/>

### 常用helm charts

* <https://artifacthub.io/>

* nacos

```bash
helm repo add ygqygq2 https://ygqygq2.github.io/charts/
helm install my-nacos --set replicaCount=3 ygqygq2/nacos --version 2.1.4
```
