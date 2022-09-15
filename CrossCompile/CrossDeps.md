# 交叉编译解决方案

```
sudo apt-get install debootstrap qemu-user-static schroot

sudo qemu-debootstrap --arch=arm64 `lsb_release -s -c` arm64_ubuntu http://mirrors.tuna.tsinghua.edu.cn/ubuntu-ports

sudo chroot /home/haoming/test/arm64_ubuntu /bin/bash

```


Ref: http://logan.tw/posts/2017/01/21/introduction-to-qemu-debootstrap/

