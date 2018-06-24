# Zookeeper和Dubbo微服务架构学习日记

## 1. Zookeeper 简介

- Zookeeper是一个中间件，为我们的分布式组件提供服务注册和协调等工作
- 作用于分布式系统，可以为大数据提供良好的服务支撑
- 支持java，提供java的客户端api

## 2. 什么是分布式系统？

1. 很多台计算机组成一个整体，一致对外提供服务，并且可以协同处理用户的同一请求
2. 内部的每台计算机都可以相互通信（rest/rpc）
3. 客户端到服务端的一次请求到响应结果都会历经多态计算机的处理

### 2.1 分布式系统图解

![1528642236334](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528642236334.png)

![1528642369404](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528642369404.png)

## 3. Zookeeper的特性

1. 一致性：数据一致性，数据按照顺序分批次入库
2. 原子性：事务要么成功，要么失败，不会局部化
3. 单一性：客户端连接到集群中的任意一个zk节点，数据都是一致的
4. 可靠性：每次对zk的操作状态都会保存到服务端
5. 实时性：客户端可以读取到zk服务端的最新数据

## 4. linux 安装java环境（参考老韩的linux教程）

## 5. 安装zk并配置环境

![1528645722841](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528645722841.png)

## 6. zk的主要目录结构

![1528644797407](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528644797407.png)

![1528644925923](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528644925923.png)

![1528645051793](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528645051793.png)

## 7. 配置并启动zk

### 7.1 zk的配置文件详解

![1528645285234](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528645285234.png)

![1528645462822](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528645462822.png)

### 7.2 详细配置

![1528645845045](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528645845045.png)

![1528645880285](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528645880285.png)

![1528645942121](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528645942121.png)

![1528646034585](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528646034585.png)

![1528646071062](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528646071062.png)

## 8. zk的基本数据模型（重点）

![1528646279920](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528646279920.png)

![1528646288829](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528646288829.png)

![1528646430795](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528646430795.png)

![1528646443288](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528646443288.png)

![1528646528839](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528646528839.png)

## 9. 启动zk客户端连接到zk服务端

![1528646834153](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528646834153.png)

![1528646944325](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528646944325.png)

## 10. zk的作用体现（重点）

![1528647302433](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528647302433.png)

![1528647312978](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528647312978.png)

![1528647332195](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528647332195.png)

![1528647343318](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528647343318.png)

![1528647389639](C:\File\Typora\Zookeeper和Dubbo微服务架构学习日记\Zookeeper和Dubbo微服务架构学习日记.assets\1528647389639.png)









