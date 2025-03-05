title: Cookie/Base64/HTTP头部注入
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-03-15 22:30:00
---
### Cookie注入
#### 表现
常见于&emsp;.asp?id=xx&emsp;之类的带参数的URL

#### 前置知识
cookie是一些数据信息，类型为“小型文本文件”，存储于电脑上的文本文件中。
cookie是服务器创建后返回给游览器的。游览器只进行了保存。
 一般cookie是以键值对进行表示的(key-value)
```
cookie常用属性的解释：
       Name： cookie的名字
       Value： cooke的值
       Path： 定义了Web站点上可以访问该Cookie的目录
       Expires： 表示cookie的过期时间，也就是有效值
       Size： 表示cookie的大小
```
cookie有2种存储方式，会话性（关浏览器就清除）和持久性（保留到有效期结束）

#### 原理
ASP脚本中的request对象，被用于从用户那里获取信息。
Request对象的使用方法：request.\[集合名称]（参数名称）
但是它同时允许提交时省略集合名称
按QueryString、Form、Cookies、ServerVariables的顺序来获取数据的
所以，当未指明使用request对象的具体方法进行获取且未过滤语句时存在注入。

#### 开始注入
（1）寻找.asp?id=xx这样的页面
（2）将“id=xx”删掉，看页面是否正常。不正常则说明参数在数据传递中启直接作用
（3）清空网址，输入
```
javascript:alert(document.cookie=“id=”+escape(“xx”));

document.cookie:表示当前浏览器中的cookie变量
alert():弹出一个对话框
escape():对字符串进行编码
```
&emsp;&emsp;按Enter键后弹出一个对话框，内容是“id=xx”
&emsp;&emsp;然后重新输入原来URL回车
&emsp;&emsp;如果显示正常，说明是用Request(“id”)方式获取数据
（4）判断是否存在漏洞:将SQL判断语句带入，并重复第三步
```
javascript:alert(document.cookie=“id=”+escape(“xx and 1=1”));
javascript:alert(document.cookie=“id=”+escape(“xx and 1=2”));

两次回显不一样则说明存在注入漏洞，并可以进行cookie注入
```
（5）代入SQL语句
```
javascript:alert(document.cookie="id="+escape("xx order by 2"));

javascript:alert(document.cookie="id="+escape("284 union select 1,…… from xx"));
```

### Base64注入
#### 原理
程序对GET的传参进行接收，并且对其进行解码，然后再放入查询语句中。
这时候我们可以编码，然后程序执行的时候，会把我们所编码的注入语句解码，再拼接到了原本程序要执行的代码中

#### 讲解
Base64注入实质上并不是一个全新的注入手段，而是一种对数据处理的方法。
Base64注入可以发生在cookie注入中，只要有
```
$id = base64_decode($_GET['id']);
```
这个函数语句，就会发生Base64的解码，甚至可以加解密成其他的格式
此时就需要一个个试或者进行白盒测试了。

### 最后总结一下HTTP头部注入
原理:后台开发人员为了验证客户端HTTP Header（比如常用的Cookie验证等）或者通过HTTP Header头信息获取客户端的一些信息（例如：User-Agent、Accept字段等），会对客户端HTTP Header 进行获取并使用SQL语句进行处理，如果此时没有足够的安全考虑，就可能导致基于HTTP Header的注入漏洞

使用HTTP头部注入漏洞的前提条件:
（1）能够对请求头消息进行修改
（2）修改的请求头信息能够带入数据库执行
（3）数据库没有对输入的请求头做过滤

常见方法:
Cookie注入
User-Agent注入
Referer注入
XFF注入（X-Forwarded-For）

如果要详细了解请自行查询。