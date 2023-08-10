### fnm

```
# window
scoop install fnm

# linux
curl -fsSL https://fnm.vercel.app/install | bash

#  编辑.bashrc
export FNM_NODE_DIST_MIRROR=https://npmmirror.com/mirrors/node/
eval "$(fnm env --use-on-cd)"
```

### nodejs

```bash
npm config set registry https://registry.npmmirror.com
npm config get registry
```

### pm2

```
# 安装
npm -g install pm2

# 启动进程监听
pm2 start "命令脚本" --name 名称

# 开机自启动
pm2 save
会将当前pm2所运行的应用保存在/root/.pm2/dump.pm2下，当开机重启时，运行pm2-root服务脚本，并且到/root/.pm2/dump.pm2下读取应用并启动

pm2 startup
即在/etc/init.d/目录下生成pm2-root的启动脚本，且自动将pm2-root设为服务。
可替代nohup命令
```