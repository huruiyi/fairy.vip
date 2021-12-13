---
title: ubuntu 20.04 中重裝8.0.27-0ubuntu0.20.04.1
tags:
 - Mysql
 - Ubuntu
categories:
- [Linux]
---



# ubuntu 20.04 中重裝8.0.27-0ubuntu0.20.04.1

## 卸载	


```yaml
sudo apt purge mysql-*

sudo rm -rf /etc/mysql/ /var/lib/mysql

sudo apt autoremove

sudo apt autoclean
```

## 重装

```yaml
sudo apt-get install mysql-server -y

sudo apt install mysql-client  -y

sudo apt install libmysqlclient-dev -y
```

##  验证是否安装mysql成功

```yaml
sudo netstat -tap | grep mysql 

tcp        0      0 localhost:33060         0.0.0.0:*               LISTEN      3282/mysqld         
tcp6       0      0 [::]:mysql              [::]:*                  LISTEN      3282/mysqld    
```

 本地连接mysql，通常输入 mysql -uroot -p 来登录root账号，

但在这里，由于之前安装时候并没有设置root用户的密码，是无法登录的

我们需要用 sudo cat /etc/mysql/debian.cnf 来查找默认用户名密码

用client下面的user 和 password来登录  mysql -udebian-sys-maint -p

成功登录，这样我们可以来设置一个root密码。

```yaml
mysql>use mysql;
mysql>flush privileges;
mysql>ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '你的密码';
mysql>flush privileges;
```

远程连接mysql, 查看权限表

```yaml
mysql> use mysql;
mysql> select host, user, authentication_string, plugin from user; 
```

添加远程访问账号

```yaml
mysql> create user 'test'@'%' identified by '你自己的mysql密码';
mysql> grant all privileges on *.* to 'test'@'%';
mysql> ALTER USER 'test'@'%' IDENTIFIED WITH mysql_native_password BY 'test';
mysql> flush privileges;
```

mysql配置文件中把 127.0.0.1 那行注释掉

```yaml
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf

注释掉bind-address

#bind-address = 127.0.0.1
```

重启mysql服务

```yaml
sudo service mysql restart
```