之前探究了DiD的runner模式, 但是对于如何再该模式下利用CI构建Docker镜像, 需要对runner进行特殊配置

在'/srv/gitlab-runner/config/config.toml'文件中有如下配置:

'''
[[runners]]
  name = "Global trunk docker runner"
  url = "http://192.168.3.148"
  token = "gqDyK9j_cETv7yCe9gcP"
  executor = "docker"
  output_limit = 32768
  limit = 1
  [runners.custom_build_dir]
  [runners.cache]
    Insecure = false
    [runners.cache.s3]
    [runners.cache.gcs]
    [runners.cache.azure]
  [runners.docker]
    tls_verify = false                                                          # 不需要特权
    image = "docker:20.10.16"                                                   # 需要该镜像docker:20.10.16
    privileged = false
    disable_entrypoint_overwrite = false
    oom_kill_disable = false
    disable_cache = false
    shm_size = 0
    extra_hosts = ["git-rd.trunk.tech:192.168.3.148"]
    pull_policy = ["if-not-present"]
    volumes = ["/var/run/docker.sock:/var/run/docker.sock", "/cache"]           # 采用Use Docker socket binding的方式
'''

ref: https://docs.gitlab.com/ee/ci/docker/using_docker_build.html#use-docker-socket-binding



**.gitlab-ci.yml模板:**

'''
stages:
  - build

image: 192.168.3.248:8081/docker/docker:20.10.16

# 登录docker服务器
before_script:
  - echo "${JFROG_PASSWORD}" | docker login 192.168.3.248:8081 --username ${JFROG_USER} --password-stdin

1804_amd64:
  stage: build
  tags:
    - amd64
    - docker
  only:
    - tags
    
  script:
    - cd ipc/development/Ubuntu18.04/ros/
    - docker build . -f Dockerfile_1804_amd64 -t 192.168.3.248:8081/docker/trunk/port:18.04_amd64_${CI_COMMIT_TAG}
    - docker push 192.168.3.248:8081/docker/trunk/port:18.04_amd64_${CI_COMMIT_TAG}
'''


