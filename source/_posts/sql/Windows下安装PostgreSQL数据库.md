---
title: Windows下安装PostgreSQL数据库
sticky: false
categories:
- [SQL]
---

[TOC]

## windows安装PostgreSQL数据库

#### 第一步,初始化数据目录:

```shell
.\initdb.exe -D D:\pgsql\data
```

#### 第二步,注册服务:

```shell
.\pg_ctl.exe register -N postgresql16 -D D:\pgsql\data
```

#### 第四步,打开服务:

```shell
net start postgresql16
```

#### 第五步,创建用户:

```shell
 .\createuser.exe -s -r postgres
```

#### 第六步,修改密码

```shell
 .\psql.exe -U postgres
psql (16.2)
输入 "help" 来获取帮助信息.

postgres=# ALTER USER postgres WITH PASSWORD 'NewPassword!!!!';
ALTER ROLE
```

