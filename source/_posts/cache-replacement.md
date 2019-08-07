---
title: cache-replacement
date: 2018-03-29 14:56:23
tags:
---

## 查看缓存

- free/top 命令

- cache memory

  - 命令：`cat /proc/meminfo`
  - 读写文件，为了提高读写性能与速度，会将文件在内存中进行缓存，这部分内存就是Cache Memory(缓存内存).即使你的程序运行结束后，Cache Memory也不会自动释放

- 手动释放cache

  ```shell
  ## To free pagecache:
  echo 1 > /proc/sys/vm/drop_caches
  ## To free dentries and inodes:
  echo 2 > /proc/sys/vm/drop_caches
  ## To free pagecache, dentries and inodes:
  echo 3 > /proc/sys/vm/drop_caches
  ```

- 查看cached里面的内容 linux-ftools

  ```shell
  #!/bin/bash
  
  tar xf linux-ftools.tar.gz -C ./
  cd linux-ftools/ && ./configure && make && make install
  ```

eg:查看/tmp 目录下缓存了哪些文件, 命令：

`
linux-fincore --pages=false --summarize --only-cached /tmp/*
`

## 缓存

## 缓存命中

## cache miss（缓存没命中）

把缓存中的就对象踢出，把新的对象加入缓存池。（替换策略，最优的替代策略就是想把缓存中最没用的条目给踢出去，但是未来是不能够被预知的，所以这种策略是不可能实现的）

## 缓存替换算法

### FIFO

先进先出

### LFU

最近最少使用算法

`
如果一个数据在最近一段时间内使用次数很少，那么在将来一段时间内被使用的可能性也很小
`

利用一个数组存储 数据项，用hashmap存储每个数据项在数组中对应的位置，然后为每个数据项设计一个访问频次，当数据项被命中时，访问频次自增，在淘汰的时候淘汰访问频次最少的数据

### LRU

最近最久未使用

`
如果一个数据在最近一段时间没有被访问到，那么在将来它被访问的可能性也很小
`

当需要插入新的数据项的时候，如果新数据项在链表中存在（一般称为命中），则把该节点移到链表头部，如果不存在，则新建一个节点，放到链表头部，若缓存满了，则把链表最后一个节点删除即可。在访问数据的时候，如果数据项在链表中存在，则把该节点移到链表头部，否则返回-1。这样一来在链表尾部的节点就是最近最久未访问的数据项

### LRU vs LFU

- LRU的淘汰规则是基于访问时间
- LFU是基于访问次数

`set(2,2),set(1,1),get(2),get(1),get(2),set(3,3),set(4,4)`

LFU 置换(3,3)
LRU 置换(1,1)