# 渗透测试技术入门培训之SQL注入

## SQL注入概念和前期知识

**SQL 注入漏洞：**

最常见的漏洞之一，在 OWASP TOP Ten 中长期位居前三。SQL注入是发生于应用程序的数据库层的安全漏洞。具体表现在输入的字符串之中注入SQL指令，在设计不良的程序(Web系统,客户端)当中忽略了检查，那么这些注入进去的指令就会被数据库服务器认为是正常的SQL指令而运行，因此遭到数据库被篡改或服务器被攻击。

![image-20230302220725633](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022207730.png)

**Web 服务中的 SQL 数据库查询**

PHP、ASP、JSP 等脚本语言使 Web 应用可以动态处理 HTTP 请求，并提供数据库访问接口，以满足数据交互的需求。

![image-20230301124837209](https://raw.githubusercontent.com/countdracular/pic-md/main/202303011248346.png)

**通过以下例子逐步追踪涉及数据库交互的HTTP 请求响应工作原理**

在浏览器上点击链接，浏览器向Web服务器发送HTTP请求，用Burp抓包看到HTTP请求细节，此处是访问 /backend/content_detail.php 并用Get方法传递了参数id=1。

![image-20230302220849209](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022208257.png)

![image-20230302220919887](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022209930.png)

在浏览器上看到Web服务器响应的结果，在Burp中可以看到响应包的细节。

![image-20230302220936244](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022209290.png)

![image-20230302220943002](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022209042.png)![image-20230302220950145](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022209188.png)

在Web服务器查看 /backend/content_detail.php ，浏览器用Get方法传递的参数 id=1 在PHP代码中被赋值给变量 $id，然后拼接出 SQL 语句 select title, content from contents where id=1; 后发往MySQL 数据库进行查询。

![image-20230302221107601](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022211649.png)

查看 db.php 和 config.php 两个PHP文件，看到关于数据库连接的配置信息。PHP 以用户名密码root/root 登录本地数据库，并访问其中的 news 库。

![image-20230302221130253](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022211301.png)

![image-20230302221136543](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022211588.png)

连接mysql数据库，执行前面所说的 SQL 语句，查询结果与浏览器显示一致。

![image-20230302221158719](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022211787.png)

经过以上对浏览器操作的逻辑追踪，我们再来理解这张图。现在请思考：对于普通用户、攻击者/渗透测试人员，哪些部分是可控的？

![image-20230302221234225](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022212300.png)

可以修改 HTTP GET 参数id的值控制数据库执行的 SQL 语句。



在 HTTP GET 参数 id 中注入 SQL 语句，适当构造后可以拖取数据库内容。

![image-20230302221301192](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022213243.png)

![image-20230302221305379](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022213418.png)

常用 SQL 语法：
show databases;
show columns from 表1;
select user(); select database(); select version(); select @@basedir; select @@datadir;
select 字段1, 字段2, ... from 表1 union select 字段1, 字段2, ... from 表2;

**练习环境搭建**
在Kali 虚拟机里启动靶机 docker 容器：docker start b1d6b0fc9e9c
进入容器命令行：docker exec -it b1d6 /bin/bash

![image-20230302221417584](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022214637.png)

进入 MySQL： mysql

![image-20230302221436233](https://raw.githubusercontent.com/countdracular/pic-md/main/202303022214280.png)



























## SQL注入基本方法

## 绕过过滤

## 实操练习
