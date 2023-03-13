---
title: mysql知识梳理
date: 2022-11-02 18:29:44
categories: j2ee
tags: 
    - mysql
description: mysql数据库很久没用了，最近在整理j2ee的知识又重新使用了mysql，打算把自己在使用过程中掌握的知识整理成档。
---

## 前期准备

### 安装mysql
1. 下载安装包：[官网下载地址](https://dev.mysql.com/downloads/mysql/)。
2. 双击安装包就可以进行安装。务必当场记住root的密码，我当初没记住后面记不起来了各种碰壁。（如果不幸忘记了密码，下面有鞋解决办法）
3. 安装完在system preference中就会出现mysql服务，自己可以手动开启和关闭。
如果手动关闭不了可以重启电脑或者查杀进程，查杀进程命令如下
```
ps aux |grep mysql

kill [进程id]
```

### 忘记root密码怎么办
1. 关闭mysql 服务。
2. 以管理员权限开启安全模式访问，步骤如下：
```
1. 获取管理员权限
sudo su
2. 查找mysql安装目录
which mysql
3. 进入mysql的bin目录
cd [你的mysql目录]/bin/
4. 开启安全访问
./mysqld_safe --skip-grant-tables &
5. 安全访问成功之后会展示如下信息，否则大概率是服务没有关闭
mysqld_safe Logging to '/usr/local/mysql/data/lyqdeMacBook-Pro.local.err'.
mysqld_safe Starting mysqld daemon with databases from /usr/local/mysql/data
```
3. 更新密码。
```
1. 新开一个terminal，进入mysql的bin目录
2. 免密登录mysql
mysql -u root -p // 不用输入密码直接回车2次
3. 更换至mysql数据库
use mysql;
4. 刷新数据
flush privileges;
5. 更新root密码
set password fot root@localhost = '12345678'; // 早期版本（应该是8以前）
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码'; // 新版命令
6. 再次刷新数据
flush privileges;
```

## mysql 语法知识

### 连接数据库语句
1. 默认连接本机数据库
```
mysql -u root -p
```
2. 连接远端数据库
```
mysql -h 192.168.1.134 -u root -p
```

### 数据库定义语句（DDL：Data Denifition Language）
1. 数据库定义语句
- 创建数据库
```
-- windows 下需改写成 create database db_test charset=utf8
create database db_test character set=utf8;
```
- 查看数据库
```
show databases;
```
- 选择数据库
```
use db_test;
```
- 删除数据库
```
drop database db_test;
```
2. 表定义语句
- 创建表
```
create table tb_test(
  id int,
  name char(10),
  phone char(11)
);
```
- 查看数据的所有表
```
show tables;
```
- 删除表
```
drop table tb_test;
```
- 表的约束设置
```
create table tb_test(
  id int [primary key], // 主键约束
  name char(10) [not null], // 非空约束
  phone char(11) [unique], // 唯一约束
  email char(50), // 字符串长度约束
  sex int [default 1], // 默认约束
  depart_id int, // 类型约束
  CONSTRAINT `emp_dept_fk` FOREIGN KEY (`depart_id`) REFERENCES `tb_dept` (`id`) 
  ON DELETE NO ACTION 
  ON UPDATE CASCADE, // 外键约束
  primary key (id, name) // 复合主键（复合主键要求对应的字段不能为空）
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8; // mysql只有InnoDB引擎支持外键设置。
```
- 重设表主键约束
```
-- 删除原来主键
alter table tb_emp drop primary key;
-- 添加主键
alter table tb_emp add pripary key id;
```
- 重新设置表字段约束
```
alter table tb_test modify name char(20) default null;
```
- 重新设置表的外键约束
```
-- 删除外键
alter table tb_emp drop foreign key emp_dept_fk;
-- 删除外键索引
alter table tb_emp drop index emp_dept_fk;
-- 设置外键
alter table tb_emp add constraint emp_dept_fk foreign key (dept_id) references tb_dept (id);

```
3. 其他数据库定义语句
```
-- 展示所有数据
show databases;
-- 展示所有表（请先使用use [数据库名]来选定数据库）
show tables;
-- 展示表的创建语句
show create table [表名];
-- 展示表中各列的信息
desc [表名];
```

### 数据库操作语句（DML：Data Manipulation Language）
1. insert 语句
```
insert into tb_dept(id, name, address) values (1, '研发部', '杭州');
```
2. delete 语句
```
delete from tb_emp where id = 1;
```
3. update 语句
```
update tb_emp set name = 'weihh', age = 18 where id = 1;
```

### 数据库查询语句（DML：Data Query Language）
1. 查询表所有数据
```
select * from tb_emp;
```
2. 条件查询
```
select name as 名字, age as 年龄 from tb_emp where name = 'weihh' and age = 18;
```
3. 模糊查询
```
select * from tb_emp where name like '%weihh%';
select * from tb_emp where name like 'weihh%';
select * from tb_emp where name like '%weihh';
```
4. 范围查询
```
select * from tb_emp where age between 18 and 35;
select * from tb_emp where age in (18, 20, 30, 35);
```
5. 查询结果排序
```
select * from tb_emp order by id desc, age asc;
```
6. 聚合函数查询
```
-- count 函数（推荐使用count(1)代替count(*)提高效率，count(colunm)会过滤null值导致计数和count(1)不一致）
select count(1) from tb_emp where age > 18;
-- max 函数
select max(age) from tb_emp;
-- min 函数
select min(age) from tb_emp;
-- avg 函数
select ave(age) from tb_emp;
-- sum 函数
select sum(age) from tb_emp;
```
7. 查询分组
```
select count(1) from tb_emp group by dept_id;
```
8. 分页查询
```
select * from tb_emp limit 0, 4;
```
9. inner join：内连接
组合两个表中相关联的记录，也就是返回两个表的交集部分。（连接查询会返回两个表中的所有查询字段）
```
select * from tb_a a inner join tb_b b on a.id = b.a_id;
```
10. left join：左连接
根据两个表中相关联的记录，返回左表中所有记录+右表中相关联的记录（右表中不足的部分null补充）
```
select * from tb_a a left join tb_b b on a.id = b.a_id;
```
11. right join：右连接
根据两个表中相关联的记录，返回右表中所有记录+左表中相关联的记录（左表中不足的部分null补充）
```
select * from tb_a a right join tb_b b on a.id = b.a_id;
```
12. union all：全连接
返回union all两边的合集。（要求查询2边的列数相同，如果列名不同则以第一个查询语句为准）
```
(select id, name from tb_a) union all (select name, address from tb_b);
```

### 事务控制语句（TCL：Trasactional Control Languag）
1. 提交事务
```
begin;
insert into tb_dept(id, name, address) values(3, 'development', 'HangZhou');
insert into tb_emp(name, age) values('whh', 21,3);
commit;
```
2. 设置保存点
```
savepoint s1;
```
3. 回滚
```
rollback to s1;
```
4. 改变事物提交模式
```
-- 禁止自动提交
set autocommit = 0;
-- 开启自动提交
set autocommit = 1;
```

### 数据库控制语句（DCL：Data Control Language）
用来管理数据库用户、控制数据库的访问权限
1. 管理用户
- 查询数据库的用户信息
```
-- 连接数据库后选择mysql数据库
use mysql;
-- 查询用户表信息
select Host, User from user;
```
- 创建用户
```
create user '用户名'@'主机名' identified by '密码';
```
- 删除用户
```
drop user '用户名'@'主机名';
```
2. 设置权限
- 查看权限
```
show grants for '用户名'@'主机名';
```
- 赋予权限
```
grant 权限列表 on 数据库.表名 to '用户名'@'主机名';
grant SELECT, INSERT, UPDATE, DELETE, CREATE, DROP on *.* to 'weihh'@'localhost';
```
- 撤销权限
```
revoke 权限列表 on 数据库.表名 from '用户名'@'主机名';
```
