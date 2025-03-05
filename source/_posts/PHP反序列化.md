title: PHP反序列化
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-04-16 00:12:00
---
#### 基础概念
在各类语言中，将对象的状态信息转换为可存储或可传输的过程就是序列化
序列化的逆过程就是便是反序列化，主要是为了方便对象传输。
为了完整安全地将对象保存到文件或数据库中，或者在网络上传输对象，就需要使用序列化。

不同类型数据序列化之后的形式:
```

$number = 34;
$float = 11.11111;
$str = 'user';
$bool = true;
$null = NULL;
$arr = array('a' => 10, 'b' => 200);
$test = new TEST('uu', true);
$test2 = new TEST('uu', true);
$test2->data = &$test2->data2;


i:34;
d:11.11111;
s:4:"user";
b:1;
N;
a:2:{s:1:"a";i:10;s:1:"b";i:200;}
O:4:"TEST":3:{s:4:"data";s:2:"uu";s:5:"data2";s:9:"dazzhuang";s:10:"TESTpass";b:1;}
O:4:"TEST":3:{s:4:"data";s:9:"dazzhuang";s:5:"data2";R:2;s:10:"TESTpass";b:1;}

只序列化成员函数，不序列化成员方法
私有属性的序列化加上类名并且在成员函数前后加上空(url编码的%00)
对象内调用对象时序列化后显示为套娃形式
```

反序列化之后:
```
只实例化new之后
object(test)#1 (3) {
  ["a"]=>                      // public
  string(6) "benben"
  ["b":protected]=>            // protected
  int(666)
  ["c":"test":private]=>       // private
  bool(false)
}

反序列化的过程只看序列化链，不管存不存在这个类
反序列化不触发成员方法，也不改变类的成员方法
```

**魔术方法是什么？**
是一个预定义好的，在特定情况下自动触发的方法
**魔术方法有什么作用？**
反序列化过程中，代码通过调用方法，触发代码执行
**魔术方法需要了解什么？**
触发时机、触发优先级、功能、参数、返回值

#### 漏洞成因
反序列化过程中，unserialize()接收的值 (字符串)可控
通过更改这个值 (字符串)，得到所需要的代码，即生成的对象的属性值

#### 魔术方法全解
```

（1）  __construct()
触发时机: 实例化对象后立即触发
功能: 提前清理不必要内容
参数: 非必要返回值
特点: 序列化和反序列化时不会触发

（2）  __destruct()
触发时机: 销毁对象时立即触发
功能: 关闭文件、释放结果集
参数: 不能带有任何参数
特点: 序列化时不会触发

（3）  __sleep()
触发时机: 序列化serialize()之前
功能: 返回需要被序列化存储的成员属性，删除不必要的属性
参数: 成员属性
返回值: 需要被序列化存储的成员属性

（4）  __wakeup()
触发时机: 反序列化unserialize()之前
功能: 重新建立数据库连接或执行其他初始化操作
参数: 不能带有任何参数
特点: 序列化时不会触发

（5）  __toString()
触发时机: 对象被当成字符串调用时
功能: 以字符串表示对象，可以自定义格式，常用于构造pop链
参数: 不能带有任何参数

（6）  __invoke()
触发时机: 尝试以调用函数的方式调用一个对象时
功能: 将一个对象像函数一样调用，在对象上下文中实现函数式编程
参数: 可以接受任意数量的参数

（7）  __call()
触发时机: 调用一个不存在的方法时
参数: 可传参
返回值: 调用的不存在的方法的名称和参数

（8）  __callStatic()
触发时机: 静态调用或调用成员常量时使用的方法不存在时
参数: 可传参
返回值: 调用的不存在的方法的名称和参数

（9）  __get()
触发时机: 访问不存在或不可访问的属性时
参数: 可传参
返回值:不存在的成员属性的名称和赋的值

（10）  __set()
触发时机: 给不存在或不可访问的属性赋值时
参数: 可传参
返回值: 不存在的成员属性的名称和赋的值

（11）  __isset()
触发时机: 对不可访问属性使用isset()或empty()时
参数: 可传参
返回值: 不存在的成员属性的名称

（12）  __unset()
触发时机: 对不可访问属性使用unset()时
参数: 可传参
返回值: 不存在的成员属性的名称

（13）  __clone()
触发时机: 当使用clone拷贝完成一个对象后，新对象会自动调用

（14）  __set_state()
触发时机: 使用var_export()函数导出一个对象时，或者使用eval()函数导入一个对象时
参数: 只有一个参数(数组)，包含对象的状态信息。键是对象的属性名，值是对象的属性值。
功能: 用于返回一个对象的状态数组
特点: 只能在类中定义，不能在对象中定义

（15）  __autoload()
触发时机: 使用未定义的类时，或者使用未加载的类时
功能: 根据类名动态加载对应的类文件，并定义对应的类
参数: 只有一个参数，即要加载的类名
特点: PHP 7.2.0及以上版本中，__autoload()魔术方法已被废弃，推荐使用spl_autoload_register()

（16）  __debugInfo()
触发时机: 在使用var_dump()函数打印对象时
功能: 被var_dump()函数输出一个数组，包含对象的调试信息
参数: 没有参数

```

#### POP链前置知识
**POP是什么？**
在反序列化中，我们能控制的数据就是对象中的属性值
POP链就是利用魔法方法在里面进行多次跳转然后获取敏感数据的种payload
**POC是什么？**
POC (Proof of concept)又称概念验证，可以理解成漏洞验证程序
POC是一段不完整的程序，仅仅是为了证明提出者的观点的一段代码
**如何分析POP链？**
最好使用反推法！
1.找到可利用的函数
2.根据参数的传递与调用反推出哪个变量需要是什么
3.分析判断有哪些魔术方法可以被利用以及如何被调用
**如何利用漏洞？**
![POP链构造例子](https://s2.loli.net/2023/04/16/wSFIaq7h4Ol32tg.png)
**构造如下:**
![POP链构造例子解析](https://s2.loli.net/2023/04/16/iSj1gtLHqTQaNsx.png)

#### 字符串逃逸——增多
字符串以`;}`结束，后面的不看
空（N）不算在内

![字符串增多例题01](https://s2.loli.net/2023/04/17/qd4L8MtOH2bPKQk.png)
![字符串增多例题02](https://s2.loli.net/2023/04/17/jS5ActwNfYMXhbx.png)

#### 字符串逃逸——减少
![字符串减少例题01](https://s2.loli.net/2023/04/18/71zLkKi9cTGwZho.png)
![字符串减少02例题](https://s2.loli.net/2023/04/18/KbeAu2j7RMiWCkT.png)

#### weakup魔术方法绕过
反序列化漏洞:
CVE-2016-7124
漏洞产生原因:
如果存在wakeup方法则反序列化前先调用wakeup方法
序列化字符串中表示对象属性个数的值大于真实的属性个数时会跳过wakeup()
如果同时还有正则表达式匹配可以用+6代替6
记得url编码

#### session反序列化
![前置知识](https://s2.loli.net/2023/04/18/SQVydNngi8rAEkm.png)
![PHP存储时](https://s2.loli.net/2023/04/18/mXDo6Qh9V4KF58E.png)
![PHP_serialize存储时](https://s2.loli.net/2023/04/18/bKH46heyWJgDrlw.png)
![PHP_binary存储时（少见）](https://s2.loli.net/2023/04/18/JRzB3V9TbdjMsUP.png)
![漏洞成因及其利用](https://s2.loli.net/2023/04/18/xcVBeOpRk5vQPG1.png)

#### phar反序列化
![phar反序列化前置知识](https://s2.loli.net/2023/04/18/uJm29U1KoICiXl3.png)
![phar文件结构](https://s2.loli.net/2023/04/18/ujNiJdK79YUWPyS.png)
![漏洞原理](https://s2.loli.net/2023/04/18/RDSGLEqZCg3szPv.png)
![漏洞利用条件](https://s2.loli.net/2023/04/18/ZsUD1jKMyVeimF4.png)
注:
（1）phar文件只看文件内容，不看后缀名
（2）使用时利用伪协议phar://文件名读取该文件

#### 其它反序列化链
找到该框架的版本以及其所用的序列化链
从网上找到POC直接利用就行

#### 使用条件竞争执行phar反序列化
```

import requests
import threading
import base64
url = ''
f = open('./phar.phar', 'rb')
data = f.read()
flag = False

def work1():
    requests.post(url=url+"?fn=a", data=data)

def work2():
    global flag
    r = requests.post(url=url+"?fn=phar://phar.phar/", data="")
    if "flag{" in r.text and flag is False:
        print(base64.b64encode(r.text.encode()))
        flag = True

while flag is False:
    a = threading.Thread(target=work1)
    b = threading.Thread(target=work2)
    a.start()
    b.start()

```






