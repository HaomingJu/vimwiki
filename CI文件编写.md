
gitlab runner注册配置完成之后还涉及到两个方面, **镜像编写** 和 **.gitlab-ci.yml文件**

镜像请参考: `https://github.com/HaomingJu/DockerServer`

在使用过程中, 遇到了 `cache` 功能偶尔失效, 两个job之间无法同步cache的问题, 推荐使用 `artifacts` 和 `dependencies`

记录一个案例, 在两个镜像ubuntu16.04和ubuntu18.04下同步进行编译上传

```yaml
[stages](stages):
    - compile
    - upload

before_script:
  - apt update
  - source /opt/ros/*/setup.bash
  - apt install -y protobuf-compiler libprotobuf-dev ying libyaml-cpp-dev
  - export CMAKE_PREFIX_PATH=${CMAKE_PREFIX_PATH}:/opt/trunk30

compile_on_1804:
    image: ubuntu:amd64_18.04
    stage: compile
    cache:
      key: $CI_COMMIT_SHA-1804
      paths:
        - build/*.deb
      policy: push
    tags:
      - infra
    script:
        - cmake -Bbuild . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/trunk30
        - cd build && make && make package

compile_on_1604:
    image: ubuntu:amd64_16.04
    stage: compile
    cache:
      key: $CI_COMMIT_SHA-1604
      paths:
        - build/*.deb
      policy: push
    tags:
      - infra
    script:
        - cmake -Bbuild . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/trunk30
        - cd build && make && make package


upload_on_1804:
    image: ubuntu:amd64_18.04
    stage: upload
    cache:
      key: $CI_COMMIT_SHA-1804
      paths:
        - build/*.deb
      policy: pull
    tags:
      - infra
    needs: ["compile_on_1804"]
    only:
      - master
    script:
        - curl -X POST -F file=@`ls build/*.deb` http://10.11.100.8:8083/api/files/datahook_bionic
        - curl -X POST http://10.11.100.8:8083/api/repos/infra_bionic/file/datahook_bionic
        - curl -X PUT http://10.11.100.8:8083/api/publish/trunk/bionic

upload_on_1604:
    image: ubuntu:amd64_16.04
    stage: upload
    cache:
      key: $CI_COMMIT_SHA-1604
      paths:
        - build/*.deb
      policy: pull
    tags:
      - infra
    needs: ["compile_on_1604"]
    only:
      - master
    script:
        - curl -X POST -F file=@`ls build/*.deb` http://10.11.100.8:8083/api/files/datahook_xenial
        - curl -X POST http://10.11.100.8:8083/api/repos/infra_xenial/file/datahook_xenial
        - curl -X PUT http://10.11.100.8:8083/api/publish/trunk/xenial
```

> 关于artifacts和dependencies的使用之后进行探究

注意: 若两个并行job同时拉取代码, 需要将"Setting -> CI/CD -> General pipelines -> Git strategy for pipelines" 由`git fetch` 改为 `git clone`
