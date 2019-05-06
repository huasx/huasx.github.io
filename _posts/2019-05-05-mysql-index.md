---
layout:     post
title:      聚簇索引 b-tree
subtitle:   浅谈聚簇索引和非聚簇索引
            
date:       2019-05-05
author:     Ricky
header-img: img/5780743b45040.jpg
catalog: true
tags:
    - MySQL
    - 索引
    - B Tree
---

### 概念

> MySQL就普遍使用B+tree实现其索引结构

#### MyISAM的是非聚簇索引
> B+Tree的叶子节点上的data，并不是数据本身，而是数据存放的地址。
> 主索引和辅助索引没啥区别，只是主索引中的key一定得是唯一的。这里的索引都是非聚簇索引。


#### InnoDB使用的是聚簇索引
> InnoDB的数据文件本身就是索引文件，B+Tree的叶子节点上的data就是数据本身，key为主键，这是聚簇索引。
> 聚簇索引，叶子节点上的data是主键(所以聚簇索引的key，不能过长)。
> 将主键组织到一棵B+树中，而行数据就储存在叶子节点上，若使用"where id = 14"这样的条件查找主键，则按照B+树的检索算法即可查找到对应的叶节点，之后获得行数据。
> 若对Name列进行条件搜索，则需要两个步骤：第一步在辅助索引B+树中检索Name，到达其叶子节点获取对应的主键。第二步使用主键在主索引B+树种再执行一次B+树检索操作，最终到达叶子节点即可获取整行数据。


### [原文参考](https://blog.csdn.net/lisuyibmd/article/details/53004848)
