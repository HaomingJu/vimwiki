在使用boost时, 在ubuntu16.04 LTS以及ubuntu18.04 LTS两个系统上由于boost版本不一致导致使用方式有不同, 特此记录

**boost::placeholders::_1的问题**

*错误示范:*
```shell
error: 'boost::placeholders' has not been declared
```

*原因:*

```
# on ubuntu16.04 LTS

dpkg -s libboost-all-dev | grep Version

Version: 1.58.0.1ubuntu1

# on ubuntu18.04 LTS
dpkg -s libboost-all-dev | grep Version

Version: 1.65.1.0ubuntu1
```

在两版本之间的_1的差距在于是否有namespace包裹, 更改发生在 [补丁](./code/0001-Move-placeholders-to-namespace-boost-placeholders.patch)
```
commit      db56733e4ed2125944b89e01cf36a9e451dd36f5
Author:     Peter Dimov <pdimov@pdimov.com>
AuthorDate: Wed May 27 01:29:50 2015 +0300
Commit:     Peter Dimov <pdimov@pdimov.com>
CommitDate: Wed May 27 01:29:50 2015 +0300
```


兼容性调用, 可以参考
```
#include <boost/bind/bind.hpp> // using boost::placeholders::_1;

void func(boost::placeholders::_1, boost::placeholders::_2) // ERROR
void func(_1, _2); // RIGHT

```
