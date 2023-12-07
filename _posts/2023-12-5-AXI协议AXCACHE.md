---
layout:     post
title:      AXI协议之AxCACHE说明
subtitle:   
date:       2025-12-05
author:     Damon
header-img: img/post-bg-swift2.jpg
catalog: true
tags:
    - AXI
---

# AXI协议之AxCACHE说明

------

AXI总线中使用ARCACHE和AWCACHE两组信号支持系统级高速缓存以及其他性能提升组件（ performance enhancing components）。

## AXI3协议

ARCACHE[3:0]或AWCACHE[3:0]信号通过配置传输事务(transaction)的可缓冲（bufferable）、可缓存（cacheable）和可分配（allocate）属性来实现系统级缓存，用来提升整体系统性能。

- Bufferable (B) bit, ARCACHE[0] and AWCACHE[0]

B位为高表示中间互连组件或其他组件可以延迟事务(transaction)到达目的地。通常，该位只跟写事务有关。

- Cacheable (C) bit, ARCACHE[1] and AWCACHE[1]

当C位为高时，意味着最终目的地的事务不必与原始事务具有相同的特征。对于写操作来说，这意味着可以合并多个不同的写操作。对于读操作来说，这意味着可以预取一个位置，或者可以为多个读操作仅获取一次数据。要确定是否应将事务缓存，应与读取分配（RA）和写入分配（WA）位一起使用。

- Read Allocate (RA) bit, ARCACHE[2] and AWCACHE[2]

当RA位为高时，表示如果传输是读取操作并且在缓存中未命中，则应进行分配。如果C位为低，则RA位不能为高。 

- Write Allocate (WA) bit, ARCACHE[3] and AWCACHE[3]

当WA位为高时，表示如果传输是写操作并且在缓存中未命中，则应进行分配。如果C位为低，则WA位不能为高。

### ARCACHE[3:0]和AWCACHE[3:0]信号编码

图片位置

## AXI4协议变化

- AxCACHE[1]更改为Modifiable位。

- 对于Non-modifiable的事务（transactions），必须按序传输。

- 更新Read-allocate和Write-allocate的意义。

### AxCACHE[1], Modifiable

- Non-modifiable transactions
  
  - AxCACHE[1]为低表示事务是Non-modifiable事务
  
  - Non-modifiable事务不能被切割，也不能同其他的事务进行合并
  
  - Non-modifiable事务中的配置（如下图）不能被修改，AxCACHE的属性只能从Bufferable到Non-bufferable.
    图片位置

- Modifiable transactions
  
  - AxCACHE[1]为高表示事务是Modifiable事务，Modifiable类型的事务
  - 多笔事务可以合并成单笔事务。
  - 读事务可以执行预取操作。
  - 写事务可以访问更大的地址空间，通过WSTRB信号确保哪个数据有效。
  - 新生成的事务中AxADDR、AxSIZE、AxLEN、AxBURST信号可以被修改，AxLOCK和AxPROT信号不可更改。

## 参考

1. AMBAaxi.pdf
2. AXI4_specification.pdf
