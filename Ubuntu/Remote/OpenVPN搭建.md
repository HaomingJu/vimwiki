# 部署
## 服务端 Server

```
wget -O openvpn.sh https://get.vpnsetup.net/ovpn
sudo bash openvpn.sh --auto

# 生成配置文件client.ovpn
```

## 客户端 Client

下载openvpn客户端, 加载`client.ovpn`进行连接


# 配置
## 内网IP绕过VPN代理
诸如内网ip段为`10.31.0.0`, 则可以在`client.ovpn`中添加配置

```
route 10.31.0.0 255.255.0.0 net_gateway

# 即IP段10.31.0.0的网络, 均使用默认(不走openvpn)网关
```

https://github.com/hwdsl2/openvpn-install/blob/master/README-zh.md
