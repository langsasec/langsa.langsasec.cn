---

title: SSRF-----服务器端请求伪造

---

### 什么是SSRF

CSRF(跨站/客户端请求伪造) => 浏览器因为Js偷偷的发送数据包

SSRF(服务器端请求伪造) => 服务器因为你的传参偷偷发送数据包

服务器端请求伪造（SSRF）是指攻击者能够从易受攻击的Web应用程序发送精心设计的请求的对其他网站进行攻击。(利用一个可发起网络请求的服务当作跳板来攻击其他服务)

攻击者能够利用目标帮助攻击者访问其他想要攻击的目标

![image-20211221170846248](https://s2.loli.net/2021/12/21/WwQrai1R3FM7eLI.png)A让B帮忙访问C

<!--more-->

**用一种简单的方法 -攻击者要求服务器为他访问URL**

我们传参然后服务器去访问。但翻译功能本就如此，并不是漏洞。

下面是来SSRF简单介绍图

![image-20211221165156101](https://s2.loli.net/2021/12/21/BW9hjogRtewxrzf.png)
		



### 如何挖掘SSRF漏洞

1. 能够对外发起网络请求的地方，就可能存在SSRF漏洞。(POST或GET)

   eg:http://example.com/index.php?page=about.php

2. 从远程服务器请求资源。

3. 数据库内置功能。（MSSQL）OpenRowset函数操作远程数据库（操作内网的数据库）

   https://www.cnblogs.com/yechanglv/p/6923147.html

### 危害

![image-20211221183408451](https://s2.loli.net/2021/12/21/EGcZAJz8gU4MaI1.png)

1. ​		访问内网(外紧内松)  （要能访问内网能访问本机）

2. ​		隐藏攻击者（当做跳板机来躲避追踪）

3. ​		攻击目标本机（dict:// file:// 读取文件）

4. ​		有些网站对127.0.0.1的访问是不设防的 （WAF不会拦截你的SSRF攻击）

   漏洞案例：http://cn-sec.com/archives/3606.html
   					 http://cn-sec.com/archives/8563.html

   扩展：

   ①file协议读本地文件。

   ②dict 协议可以加载一个 tcp 端口的提供的服务所返回的部分数据。

   dict 协议参考：https://www.cnblogs.com/Node-Sans-Blog/p/15118018.html

   ③redis未授权访问：redis 缓存数据库（默认没有密码 外网访问不到）

   ④gopher协议： 可以发起TCP数据流

   gopher协议具体操作：https://www.langsa.xyz/index.php/2021/12/21/gopher/

### 绕过手法

​		127.0.0.1 内网地址

​		127.0.0.1:81 当前存在SSRF漏洞主机的81端口

​		域名:81  这个域名对应ip的81端口

1. http://www.baidu.com@127.0.0.1

2. 纯数字ip地址

    http://www.baidu.com：

   http://112.80.248.73 

   http://1884354633

   http://0016024174111

   http://0x7050f849

### 靶场作业

由于靶场带有敏感信息，完整文章请邮件联系我或者关注公众号留言