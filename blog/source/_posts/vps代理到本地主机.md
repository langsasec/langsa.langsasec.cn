---
title: VPS代理到本地主机
tags: 
- 代理
- VPS
---

## 准备工作

1. vps或云服务器
2. putty：SSH远程连接工具
3. ProxySwitchyOmega插件
4. Proxifier：好用强大的Socket代理工具

<!--more-->

## 操作步骤

### Putty连接(Xshell类似)

##### **1.打开putty输入vps或云服务器连接地址和端口，如下图**

![image-20220822224926751](https://i0.hdslb.com/bfs/album/d23f0548cf337560fe46b5574d6d8597de0d6f70.png)

##### 2.选择X11下面的那个选项，下文为中文版所以翻译为通道

![image-20220822225149285](https://i0.hdslb.com/bfs/album/15a02116fed151d9b4f494701147ac565244dd8b.png)

##### 3.输入端口，点击添加

![image-20220822225328810](https://i0.hdslb.com/bfs/album/7a16bea5cb2a1d903d5858563f72df471d015045.png)

![image-20220822225455402](https://i0.hdslb.com/bfs/album/102197146b1c20e4ca2816e7b5c7af411813ab90.png)

##### 4.点击打开或open输入账户密码连接即可

### 浏览器代理配置（仅浏览器）

##### 新建情景模式配置如下

![image-20220822230248939](https://i0.hdslb.com/bfs/album/40e90c39d7f12695b48b1b3b9f6b0dee6b22299d.png)

### Proxifier代理配置（全局）

##### 1.代理服务器配置

![image-20220822225732688](https://i0.hdslb.com/bfs/album/3473d24504d652ad8aadb954fb3dda56dc1edfe9.png)

##### 2.代理规则配置

![image-20220822225851735](https://i0.hdslb.com/bfs/album/eb2b3c660e67dfca8bf3252844213be5a93595d0.png)