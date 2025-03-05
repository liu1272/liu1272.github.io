title: ODBC数据库
author: liuGuobin
tags:
  - 附录
categories:
  - 附录
date: 2022-11-19 02:49:00
---
##### 此章节是拓展阅读，选择性食用
概念:ODBC 是一种应用程序编程接口(API)，使我们有能力连接到某个数据源。

#### 创建 ODBC 连接
通过一个 ODBC 连接，可以连接到网络中的任何计算机上的任何数据库。
创建到达 MS Access 数据库的 ODBC 连接的方法:
1.在控制面板中打开管理工具图标。
2.双击其中的数据源(ODBC)图标。
3.选择系统 DSN 选项卡。
4.点击系统 DSN 选项卡中的添加。
5.选择Microsoft Access Driver。点击完成。
6.在下一个界面，点击选择来定位数据库。
7.为数据库起一个数据源名(DSN)。
8.点击确定。

注意:必须在您的网站所在的计算机上完成这个配置。
如果您的计算机上正在运行 Internet 信息服务(IIS)，上面的指令将会生效。
但是如果您的网站位于远程服务器，您必须拥有对该服务器的物理访问权限，或者请您的主机提供商为您建立 DSN。

#### 连接 ODBC
```
实例
下面的实例创建了到达名为 link 的 DSN 的连接，没有用户名和密码。然后创建并执行一条 SQL 语句：
$conn=odbc_connect('link','','');           //数据源名、用户名、密码以及可选的指针类型
$sql="SELECT * FROM customers";
$rs=odbc_exec($conn,$sql);                  //执行 SQL 语句
```

#### 取回记录
从结果集中返回记录。如果能够返回行，则函数返回 true，否则返回 false。
```
该函数有两个参数：ODBC 结果标识符和可选的行号:
odbc_fetch_row ( resource $result_id [, int $row_number = 1 ] )
```

#### 从记录中取回字段
从记录中读取字段。该函数有两个参数:ODBC 结果标识符和字段编号或名称。
```
$compname=odbc_result($rs,1);                             //从记录中返回第一个字段的值
$compname=odbc_result($rs,"CompanyName");                 //返回名为 "CompanyName" 的字段的值
```

#### 关闭 ODBC 连接
 odbc_close($conn); 
 
#### 全过程示例
```
 <?php
 $conn=odbc_connect('northwind','','');            //连接 ODBC
 if (!$conn)                                       //验证链接
 {exit("Connection Failed: " . $conn);}            //失败的回显
 $sql="SELECT * FROM customers";                   //创建链接
 $rs=odbc_exec($conn,$sql);                        //执行 SQL 语句
 if (!$rs)
 {exit("Error in SQL");}                           //判断是否连接正常
 echo "<table><tr>";
 echo "<th>Companyname</th>";                      //回显
 echo "<th>Contactname</th></tr>";
 while (odbc_fetch_row($rs))                       //取回记录
 {
 $compname=odbc_result($rs,"CompanyName");         //从记录中取回字段compname
 $conname=odbc_result($rs,"ContactName");          //从记录中取回字段compname
 echo "<tr><td>$compname</td>"; 
 echo "<td>$conname</td></tr>";
 }
 odbc_close($conn);                                //关闭 ODBC 连接
 echo "</table>";
 ?>
```