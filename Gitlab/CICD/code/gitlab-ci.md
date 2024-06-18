> file: .gitlab-ci.yml

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
        - curl -X PUT -H 'Content-Type: application/json' --data '{"ForceOverwrite":true}' http://10.11.100.8:8083/api/publish/trunk/bionic

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
        - curl -X PUT -H 'Content-Type: application/json' --data '{"ForceOverwrite":true}' http://10.11.100.8:8083/api/publish/trunk/xenial
```
