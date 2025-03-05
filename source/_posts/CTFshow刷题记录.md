title: CTFshow刷题记录
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-04-07 19:51:00
---
#### 本文会一直更新，不会分开多个文章记载刷题记录
##### 文章只记载最简单的最重要的思路
##### 信息泄露
既然是信息泄露，自然不会很难，基本上都是工具一把嗦。
```
F12
robots.txt
js
index.php~
抓包
index.phps
www.zip
.git
.svn
.index.php.swp
.index.php.bak
.index.php.swo
.index.php.swn
.DS_Store
.hg
邮箱，电话号码泄露
技术文档
editor编辑器
探针
backup.sql
/db/db.mdb
抓包返回状态
```
##### 爆破(BP)
爆破简单的用BP完全足够了，难一点的写个Python脚本。
```
Payload set         ---->  custom iterator(自定义迭代器)
需要进行base64编码  ---->  payload processing 进行编码设置
取消Palyload Encoding编码,因为在进行base64加密的时候在最后可能存在 == 会影响base64加密的结果
有多重目录的url可以用number爆破出动态地址，同时记得改一下页面index.php
```
##### 命令执行
请查看下一篇文章。