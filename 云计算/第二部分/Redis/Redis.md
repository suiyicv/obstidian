一.Redis的作用

应用场景：缓存服务器，提升业务的访问速度
![[Excalidraw/Drawing 2024-06-03 09.22.32.excalidraw.md#^group=C7a_EcWABUnj2SxQYMY1S|700]]
1.根据热点数据进行缓存
2.缓存网站上的静态资源
3.每份缓存数据都要设置一个合理的过期时间

核心关注点：缓存命中率
如果缓存命中率很低，不但不会提升业务的访问速度，反而会影响前端业务

NOSQL数据库
非关系型数据库
memcahed mongodb redis

redis特性
基于内存存储数据
以key-value键值对的方式存储数据
支持数据持久化存储（rdb数据文件，aof日志 ）
支持多实例，主从复制，分片集群，哨兵集群


redis安装

安装依赖环境
yum -y install gcc


https://download.redis.io/releases/redis-5.0.12.tar.gz

安装redis包









