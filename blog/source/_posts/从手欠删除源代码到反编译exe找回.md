---
title: 从手欠删除源代码到反编译exe找回
tags:
- edusrc 
- 反编译
---



## 前言

之前发的EDU查询辅助工具，只用Pyinstaller打包了exe，有MAC的小伙伴想要运行，但是我觉得屎山代码没必要开源，在某一天我将项目源码永久删除，恢复之后乱码，所以没办法满足小伙伴的需求。后面想着找个机会能不能反编译一下，反正代码超简单。

## 项目介绍

### 项目地址

> https://github.com/langsasec/edusrc

![image-20221117234238848](https://i0.hdslb.com/bfs/album/7835aebacb585f4993254174a51bd8fa7afbc138.png)

这是之前闲的没事用Flask开发的一个超轻量的网站，主要方便用于EDU_SRC查询具体学校和信息搜集用，最开始是包含Sqlite数据库文件的，后来觉得就两张表，索性把数据查出来放到Python列表里更方便。最后只有一个后端文件main.py和静态资源。

<!--more-->

## 准备工作

### 所用软件

环境：Python3

1. edusrc.exe(已用Pyinstaller打包好Flask的exe)
2. X-ways(16进制编辑器即可，我用的X-ways，windows用WinHex，Mac用010 editor)
3. reverse_pyexe(pyinstaller打包的exe逆向还原项目)
4. Pyc反编译工具(在线或uncompyle6等其他方法)

## edusrc.exe→main.pyc

### 执行命令

将需要反编译的`exe`和`pyinstxtractor.py`放到同一个目录下直接运行

![image-20221117220830287](https://i0.hdslb.com/bfs/album/83ff9660aeea4847f42083fa16ee89de18bddf29.png)

```sh
python pyinstxtractor.py edusrc.exe
```

![image-20221117222110116](https://i0.hdslb.com/bfs/album/6dff15a25c0fa4c5dc75c08014cced4d534bf798.png)

生成edusrc.exe_extracted文件夹

![image-20221117221133702](https://i0.hdslb.com/bfs/album/8e67cd3ad30ac215a144de7e61be843e7e12f2c9.png)

包含的静态资源已经反编译出来

![image-20221117221642035](https://i0.hdslb.com/bfs/album/4957471b141d3e90d7c3bd6c32d68711d46fd1e6.png)

### 十六进制构造main.pyc

#### 原理

`pyinstaller`在打包的时候，会将pyc文件的前`8`个字节清除，所以后期需要自己添加上去，前四个字节为`python`编译的版本，后四个字节为时间戳。（四个字节的`magic number`、四个字节的`timestamp`）
所以在这里可以通过`struct`文件来获取其中的信息。

找到如下两个文件：main和struct

![image-20221117221811664](https://i0.hdslb.com/bfs/album/aec9aff90cb4199fe82c1acecef550efbf6768ac.png)

用16进制编辑器打开这两个文件，并新建一个main.pyc，共同打开，我这里用X-ways代替。

![image-20221117223339450](https://i0.hdslb.com/bfs/album/ef52792d2fea0ea060d35a67f0288d319516cb5b.png)

**main.pyc=struct第一行+main**

struct

![image-20221117223513401](https://i0.hdslb.com/bfs/album/f75249cf47b1a46e78ed27536de303494e08f0b1.png)

main

![image-20221117223602881](https://i0.hdslb.com/bfs/album/f92301c401d2c303d79fe0d4c96a80f622d5f9ef.png)

main.pyc

![image-20221117223704367](https://i0.hdslb.com/bfs/album/0522b49dfe5450bd3833fd8b538b2ea1593b63fc.png)

## main.pyc→main.py

方法一：uncompyle6

[工具推荐——又有三个Burp插件](http://mp.weixin.qq.com/s?__biz=MzI1ODM1MjUxMQ==&mid=2247490032&idx=1&sn=a07d1e85881b483c998e9438c23fa568&chksm=ea082de0dd7fa4f60045816c27924cd49225f54c90491f0e7b72b3062f052e73d813737a7710#rd)

```
pip3 install uncompyle6
uncompyle6 main.pyc
```

我本地是Python3.10，所以不支持，如果要用这个尽量用Python3.8

![image-20221117224147467](https://i0.hdslb.com/bfs/album/4226f331aa2c949653cb50e6a57e0382066b46ca.png)

方法二：在线pyc反编译

地址：https://tool.lu/pyc

![image-20221117224513652](https://i0.hdslb.com/bfs/album/f6cc209385e65bb942fe9a941c617aad0fb4e895.png)

成功拿到main.py源码

![image-20221117224734204](https://i0.hdslb.com/bfs/album/35bfbc34b1fc496d0b837f51f130aa0e56aac152.png)

## 一些问题

1. 直接反编译出来的代码可能会和最开始自己写的有一部分出入。

2. 部分代码因为逻辑问题可能出现Bug，经过我的调试已解决。

   例如：

   Flask中的app路由装饰器代码被修改使得程序无法运行。

   Flask中的渲染模块`render_template`的参数格式也被修改

3. 反编译后需要做少量的调试才能正常运行，代码量巨大时估计会很头疼。

## Reference

> https://github.com/MrWQ/reverse_pyinstaller

