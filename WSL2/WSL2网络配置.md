## 1. 宿主机防火墙策列

在安装好WSL2之后, 宿主机需要开启防火墙.

WSL2之后网络策列发生了修改, 在WSL2中是不能Ping通宿主机的,需要开启防火墙策列

需要以**管理员权限**打开`PowerShell`, 执行命令:

```
New-NetFirewallRule -DisplayName "WSL" -Direction Inbound  -InterfaceAlias "vEthernet (WSL)"  -Action Allow
```

> Ref: https://github.com/microsoft/WSL/issues/4585

## 2. 获取宿主机IP

在WSL2中获取宿主机IP, 执行命令:

```
ip route show | grep -i default | awk '{ print $3}'
```

## 3. Ping通主机

在WSL2中Ping宿主机IP即可, 在执行完第一步防火墙策列修改之后即可ping通

## 4. 设置代理

目前习惯在`~/.zshrc`文件中自动配置代理
```
export HOST_IP=$(ip route show | grep -i default | awk '{ print $3}')
export http_proxy="http://$HOST_IP:7890"
export https_proxy="http://$HOST_IP:7890"
```
