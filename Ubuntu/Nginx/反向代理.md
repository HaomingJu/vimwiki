nginx反向代理实现端口转发

适用场景: 内网域名解析, 容灾, 软件源代理

硬件说明:

* 192.168.3.128 服务器, 内置DNS解析服务
* 10.11.1.15 服务器, 内置Nginx服务

应用简述:

普通电脑内设置DNS解析服务edit: `/etc/resolv.conf`
```
nameserver 192.168.3.128
```

192.168.3.128内DNS解析条目`/etc/dnsmasq.hosts`为:
```
10.11.1.15      jfrog.trunk.tech
10.11.1.15      mirrors.trunk.tech
10.11.1.15      docker.trunk.tech
```
10.11.1.15内设置nginx反向代理`/etc/nginx/conf.d/trunk.conf`:
```
server {
	listen 80;
	server_name docker.trunk.tech;
	location / {
		proxy_pass http://192.168.3.248:8081;
		index index.html index.htm;
	}
}

server {
	listen 80;
	server_name mirrors.trunk.tech;
	location / {
		proxy_pass http://mirrors.aliyun.com;
		index index.html index.htm;
	}
	location /infra {
		proxy_pass http://192.168.3.248:8081/artifactory/infra;
		index index.html index.htm;
 	}

	location /infra-ports {
		proxy_pass http://192.168.3.248:8081/artifactory/infra-ports;
		index index.html index.htm;
 	}
}

```


应用:

若用户访问`mirros.trunk.tech`则由DNS解析至`10.11.1.15`的`80`端口, 而部署在`10.11.1.15`上的`nginx`则将请求转发至`http://mirrors.aliyun.com`

若用户访问`mirros.trunk.tech/infra`则由DNS解析至`10.11.1.15`的80端口, 而部署在`10.11.1.15`上的`nginx`则将请求转发至`http://192.168.3.248:8081/artifactory/infra`



依据以上过程原理 可以用nginx反向代理阿里云的ubuntu软件源, 且在阿里云软件源失效时快速切换至清华源或其他源

```
deb http://mirrors.trunk.tech/ubuntu/ bionic main restricted universe multiverse
deb http://mirrors.trunk.tech/ubuntu/ bionic-updates main restricted universe multiverse
deb http://mirrors.trunk.tech/ubuntu/ bionic-backports main restricted universe multiverse
deb http://mirrors.trunk.tech/ubuntu/ bionic-security main restricted universe multiverse

deb http://mirrors.trunk.tech/ros/ubuntu bionic main
deb http://mirrors.trunk.tech/infra bionic main
```

还有域名访问, 可直接访问`http://docker.trunk.tech`等任意代理的域名(不需要端口号)
