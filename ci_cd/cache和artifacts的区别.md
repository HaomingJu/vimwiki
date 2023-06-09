
# cache和artifacts的区别

cache 指的是缓存, artifacts指的是工件

区别:

1. cache不一定命中, artifacts肯定命中
2. 重新安装时因为使用的是缓存, 所以很有可能不是最新的
3. 特别时开发环境, 如果每次都希望使用最新的更新, 应当删除cache, 使用artifacts
4. 如果使用Docker运行gitlab-runner, cache会生成一些临时容器, 不容易清理 `docker volume prune`

5. artifacts可以设置过期时间, 过期自动删除, cache不会自动清理
6. artifacts会先传到gitlab服务器上，然后需要时再重新下载, 可以在gitlab网页上浏览和下载


所以建议一般在进行CI时, 编译(cmake && make)放在一个stage中其他诸如上传/测试放在另一个stage中, 以artifacts连接

一个完美的[参考案例](./store/other/.gitlab-ci.yml)

```yaml
stages:
    - compile
    - upload

before_script:
  - export CMAKE_PREFIX_PATH=${CMAKE_PREFIX_PATH}:/opt/trunk30

compile_on_1804:
    image: ubuntu:amd64_18.04
    stage: compile
    artifacts:
      name: "rttr_artifacts_18.04"
      paths:
        - build/*.deb
      expire_in: '300'
      when: on_success
    tags:
      - infra
    script:
        - cmake -Bbuild . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/trunk30
        - cd build && make -j8 && make package

compile_on_1604:
    image: ubuntu:amd64_16.04
    stage: compile
    artifacts:
      name: "rttr_artifacts_16.04"
      paths:
        - build/*.deb
      expire_in: '300'
      when: on_success
    tags:
      - infra
    script:
        - cmake -Bbuild . -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/opt/trunk30
        - cd build && make -j8 && make package


upload_on_1804:
    image: ubuntu:amd64_18.04
    stage: upload
    dependencies:
      - compile_on_1804
    tags:
      - infra
    needs: ["compile_on_1804"]
    #only:
      #- master
    script:
        - curl -X POST -F file=@`ls build/*.deb` http://10.11.100.8:8083/api/files/rttr_bionic
        - curl -X POST http://10.11.100.8:8083/api/repos/infra_bionic/file/rttr_bionic
        - curl -X PUT http://10.11.100.8:8083/api/publish/trunk/bionic

upload_on_1604:
    image: ubuntu:amd64_16.04
    stage: upload
    dependencies:
      - compile_on_1604
    tags:
      - infra
    needs: ["compile_on_1604"]
    #only:
      #- master
    script:
        - curl -X POST -F file=@`ls build/*.deb` http://10.11.100.8:8083/api/files/rttr_xenial
        - curl -X POST http://10.11.100.8:8083/api/repos/infra_xenial/file/rttr_xenial
        - curl -X PUT http://10.11.100.8:8083/api/publish/trunk/xenial

```
