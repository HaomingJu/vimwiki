目前gitlab runner的运行方式一般推荐docker-in-docker的方式.

即有宿主机的情况下: gitlab-runner运行在docker中, 且在执行pipeline时再启动docker-in-docker

1. 环境部署

```shell
# 宿主机安装docker周边
sudo apt install -y docker*

# 拉取runner镜像
docker run -d --name CIRunner \
    ¦   --restart always \
    ¦   -v /srv/gitlab-runner/config:/etc/gitlab-runner \
    ¦   -v /var/run/docker.sock:/var/run/docker.sock \
    ¦   gitlab/gitlab-runner:latest

# 创建gitlab-runner实例
docker run -d --name CIRunner \
        --restart always \
        -v /srv/gitlab-runner/config:/etc/gitlab-runner \
        -v /var/run/docker.sock:/var/run/docker.sock \
        gitlab/gitlab-runner:latest
```

至此创建了一个名为`CIRunner`的runner实例


2. 注册并配置runner

```shell
# 注册, executor选择docker
sudo docker exec -it CIRunner /bin/bash -c 'gitlab-runner register'

# 健康检测
sudo docker exec -it runner /bin/bash -c 'gitlab-runner health-check'

# 配置文件, 参考 [runner_config](runner_config.md)
sudo vim /srv/gitlab-runner/config/config.toml 
```

3. 注销runner

```
docker exec -it CIRunner /bin/bash -c 'gitlab-runner unregister --url <gitlab_url> --token <runner_token>'

# docker exec -it CIRunner /bin/bash -c 'gitlab-runner unregister --url http://192.168.3.148 --token CV_JWc5EYrwZkhSdJy-Y'
```


4. 关闭服务
