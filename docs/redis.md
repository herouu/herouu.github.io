#### redis运维命令

#### redis迁移

* 工具 redisShake

使用: <https://github.com/tair-opensource/RedisShake/wiki>

#### 缓存一致性

并发场景下，更新数据库，基于订阅binlog的方式,利用消息队列,更新redis缓存,可以避免缓存命中率的问题

[【缓存数据一致性探究】](https://mp.weixin.qq.com/s/OWuP66WxpciBAgm2mptUxw)

[【浅谈缓存最终一致性的解决方案】](https://cloud.tencent.com/developer/article/1932934)

[【干货 | 携程最终一致和强一致性缓存实践】](https://mp.weixin.qq.com/s/E-chAZyHtaZOdA19mW59-Q)

#### redis降级

[【高并发系统设计（二十七）：【降级熔断】如何屏蔽非核心系统故障的影响？】](https://www.cnblogs.com/wt645631686/p/13871667.html)

#### redis sql

* redis trino

<https://github.com/redis-field-engineering/redis-sql-trino>

#### 一致性hash

* 一致性hash

参考： <https://learnku.com/articles/75296>

* 带虚拟节点的一致性hash

参考：<https://cloud.tencent.com/developer/article/1095164>
