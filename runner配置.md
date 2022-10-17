gitlab runner的运行方式一般采用Docker-in-Docker的方式, 即在注册(register)时执行器(executor)选择docker

针对runner的配置, 可以通过在宿主机上更改配置文件再重启(restart)gitlab-runner服务来实现.

`sudo vim /etc/gitlab-runner/config.toml`

对于其中的常用参数进行记录

```
concurrent = 4                          # 并发数, 每个runner可以同时执行的任务数
check_interval = 0

[session_server]
  session_timeout = 1800

[[runners]]
  name = "CI runner for build jian"
  url = "http://192.168.3.148/"
  token = "JhC2x4L4YqCZD4czida9"
  executor = "docker"
  output_limit = 32768                  # 日志输出大小限制, 单位: 字节
  limit = 1                             # 当前runner同一时刻最多执行多少个任务
  [runners.custom_build_dir]
  [runners.cache]
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false
    image = "ubuntu:18.04_base"
    cpus = "8"                          # 限制runner可以使用的CPU最大个数
    memory = "16g"                      # 限制内存使用
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    volumes = ["/cache"]
    extra_hosts = ["git-rd.trunk.tech:192.168.3.148"]       # 追加DNS解析
    pull_policy = ["if-not-present"]                        # 镜像拉取策略: 优先本地发现, 否则从远程拉取
    shm_size = 0
```
