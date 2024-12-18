# Server

1. 安装rsync: 
```
sudo apt install -y rsync
```

3. 修改服务端配置
```
@file: /etc/rsyncd.conf

uid = haoming
gid = haoming
use chroot = false

max connections = 4
# 欢迎文件的路径，非必须
#motd file = /etc/rsync/rsyncd.motd

# pid文件路径
pid file = /var/run/rsyncd.pid

# 剔除某些文件或目录，不同步
exclude = lost+found/

# 设置超时时间
timeout = 900
ignore nonreadable = yes

# 设置不需要压缩的文件
dont compress   = *.gz *.tgz *.zip *.z *.Z *.rpm *.deb *.bz2

# 模块，可以配置多个，使用如: sate@172.18.50.125::125to110
[demo]
path = /home/haoming/code/demo
exclude = .git/ .gitignore
```

4. 启动服务
```
# 杀死服务
sudo kill -9 `cat /var/run/rsyncd.pid`

# 重启服务
sudo rsync --daemon
```

# Client

1. 安装rsync: 
```
sudo apt install -y rsync
```

2. 同步脚本
```
rsync -avzh \
    --exclude build_aarch64_orin \
    --delete \
    rsync://10.31.1.164/localization ./localization
```
