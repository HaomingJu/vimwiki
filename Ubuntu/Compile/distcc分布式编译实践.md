# Server (10.31.3.7)

安装distcc
```
sudo apt install -y distcc
```

配置distcc, 编辑`/etc/default/distcc`

```
# file: /etc/default/distcc

# 开机启动
STARTDISTCC="true"

# 允许哪些IP访问，可以是一个IP，也可以是一个网段10.31.1.0/24
ALLOWEDNETS="10.31.1.164"

LISTENER="0.0.0.0"

NICE="10"

# 可用本地CPU核心数
JOBS="32"

ZEROCONF="false"

```

启动distccd
```
sudo /etc/init.d/distcc start
```

查看distccd状态
```
sudo /etc/init.d/distcc status
```


# Client (10.31.1.164)

安装distcc
```
sudo apt install -y distcc
```

将client登录公钥添加到server
```
ssh-copy-id -i ~/.ssh/id_rsa.pub haoming@10.31.3.7
```

配置distcc, 以ssh协议进行通讯,编辑`/etc/distcc/hosts`

```
# file: /etc/distcc/hosts
# 以ssh协议通讯，用户@IP/调用CPU核心数

haoming@10.31.3.7/32
```

配置环境变量
```
export CC = "distcc gcc"
export CXX = "distcc g++"
```

查看使用情况
```
distccmon-gnome
```
