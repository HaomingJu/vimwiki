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

一个完美的[参考案例](./code/gitlab-ci)
