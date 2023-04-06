---
layout: post
title: Redis 学习笔记
date: 2023-04-05 20:28 +0800
categories:
- 课程
- Redis
tags:
- Redis
---



Redis 启动（已经设置好可以后台启动）：

```bash
redis-server /opt/homebrew/etc/redis.conf
```

客户端访问：

```bash
redis-cli
```

客户端关闭：

```bash
redis-cli shutdown
```



kill 关闭：

```bash
ps -ef | grep redis	
kill -9 进程号
```



 ## 常用五大数据类型

### Redis Key

- keys *：查看当前库所有 key
- exists key：判断某个 key 是否存在
- type key：查看 key 是什么类型
- del key：删除指定的 key 数据
- unlink key：根据 value 选择非阻塞删除
- expire key 10：设置 key 的过期时间为 10 秒
- ttl key：查看 key 还有多少秒过期，-1 表示永不过期，-2 表示已过期
- set key value：设置 key value
- get key：查询对应键值
- strlen key：获得 key 对应值的长度
- setnx key value：只有在 key 不存在时，设置 key 的值
- incr key
- decr key：是原子操作，不会被线程调度机制打断



### 字符串 String

String 是二进制安全的，一个 Redis 中字符串 value 最多可以是 512 MB。

String 的数据结构是简单动态字符串，当字符串长度小于 1M 时，扩容都是加倍扩容；超过 1M 扩容只会多扩 1M。

- mset key1 value1 key2 value2....：同时设置一个/多个 key-value 对
- mget key1 key2 key3...：同时获取一个/多个 value
- msetnx key1 value1 key2 value2：同时设置一个/多个 key-value，仅当所给定的 key 不存在
- setex key 10 value：设置键值 10 秒过期
- getset：设置了新值同时获得旧值



### 列表 List

### 集合 Set

### 哈希 Hash

### 有序集合 Zset

## 写在最后

感谢你在茫茫人海中找到我🕵🏼

<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js"></script>

<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.3.1/css/all.css" integrity="sha384-mzrmE5qonljUremFsqc01SB46JvROS7bZs3IO2EmfFsd15uHvIt+Y8vEf7N7fWAU" crossorigin="anonymous">

<span id="busuanzi_container_page_pv">🎉你是第 <span id="busuanzi_value_page_pv"><i class="fa fa-spinner fa-spin"></i>  </span> 个读者

㊗️ 你平安喜乐，顺遂无忧！

希望你读完有所收获～

🥂🥂🥂 
