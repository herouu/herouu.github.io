### fnm

```
# window
scoop install fnm

# linux

curl -fsSL https://fnm.vercel.app/install | bash

# 使用不了访问
curl -fsSL https://herouu.github.io/shell/fnm_install.sh | bash

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

### .npmrc文件
```
registry=https://registry.npmmirror.com

disturl=https://registry.npmmirror.com/-/binary/node/
# node-sass预编译二进制文件下载地址
sass_binary_site=https://registry.npmmirror.com/-/binary/node-sass

# sharp预编译共享库, 截止2022-09-20 sharp@0.31.0的预编译共享库并未同步到镜像, 入安装失败可切换到sharp@0.30.7使用
sharp_libvips_binary_host=https://registry.npmmirror.com/-/binary/sharp-libvips
python_mirror=https://registry.npmmirror.com/-/binary/python/
electron_mirror=https://registry.npmmirror.com/-/binary/electron/
electron_builder_binaries_mirror=https://registry.npmmirror.com/-/binary/electron-builder-binaries/

# 无特殊配置参考{pkg-name}_binary_host_mirror={mirror}
canvas_binary_host_mirror=https://registry.npmmirror.com/-/binary/canvas
node_sqlite3_binary_host_mirror=https://registry.npmmirror.com/-/binary/sqlite3
better_sqlite3_binary_host_mirror=https://registry.npmmirror.com/-/binary/better-sqlite3

```