title: "Mysql数据库常用命令"

date: 2020-07-08 16:47:27

description: 

categories: 编程基础 - 数据库

tags: [Mysql,数据库]

---

本章主要是记录一些Mysql数据库常用的命令，方便日后使用的时候查阅。

<!--more-->

#### Mac安装Mysql数据库

##### 通过brew方式安装

1. 安装mysql

	> brew install mysql

2. 启动mysql服务

	> mysql.server start
	
##### 官网下载安装

1. 下载地址：[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)

![](/img/编程数据-Mysql数据库常用命令/20200708165841.jpg)

2. 下载完成后直接双击安装，然后点击Mac的系统偏好设置，能看到如下的图标：

![](/img/编程数据-Mysql数据库常用命令/20200708170149.jpg)

3. 点击MySQL图标，进行数据库相关配置和启动：

![](/img/编程数据-Mysql数据库常用命令/20200708170417.jpg)

![](/img/编程数据-Mysql数据库常用命令/20200708170427.jpg)

#### Mysql数据库简单使用

> 访问数据库：/usr/local/mysql/bin/mysql -u root -p
> 创建数据库：create database pbngen;
> 删除数据库：drop database pbngen;
> 查看数据库：show databases;
> 切换数据库：use pbngen;

![](/img/编程数据-Mysql数据库常用命令/mysql命令.png)

#### 相关链接

菜鸟教程：[https://www.runoob.com/mysql/mysql-tutorial.html](https://www.runoob.com/mysql/mysql-tutorial.html)







