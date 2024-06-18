> Boost Version: 1.78.0


# 三种无锁结构

boost::lockfree::queue (a lock-free multi-producer/multi-consumer queue), 无锁队列, 适用于**多生产者-多消费者**

boost::lockfree::stack (a lock-free multi-producer/multi-consumer stack), 无锁栈, 适用于**多生产者-多消费者**

boost::lockfree::spsc_queue (a wait-free single-producer/single-consumer queue), 无锁队列, 适用于**单生产者-单消费者**




# 参考

* https://www.boost.org/doc/libs/1_78_0/doc/html/lockfree.html
