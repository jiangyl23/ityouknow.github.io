---
layout:     post
title:      linux服务器安装MySQL-5.7
no-post-nav: true
category: life
tags: [life]
excerpt: mysql详细安装步骤
---
1、下载tar包，这里使用wget从官网下载
wget https://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.22-linux-glibc2.12-x86_64.tar.gz
2、将mysql安装到/usr/local/mysql下

# 解压

tar -xvf mysql-5.7.22-linux-glibc2.12-x86_64.tar.gz

# 移动

mv mysql-5.7.22-linux-glibc2.12-x86_64 /usr/local/

# 重命名

mv /usr/local/mysql-5.7.22-linux-glibc2.12-x86_64 /usr/local/mysql

3、新建data目录

mkdir /usr/local/mysql/data

4、新建mysql用户、mysql用户组

# mysql用户组

groupadd mysql

# mysql用户

useradd mysql -g mysql

5、将/usr/local/mysql的所有者及所属组改为mysql

chown -R mysql.mysql /usr/local/mysql

6、初始化配置
/usr/local/mysql/bin/mysqld --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data --initialize


# 如果出现以下错误：


usr/local/mysql/bin/mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory

执行下面 
yum install -y libaio

Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
Resolving Dependencies
--> Running transaction check
---> Package libaio.x86_64 0:0.3.109-13.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

================================================================================
 Package         Arch            Version                    Repository     Size
================================================================================
Installing:
 libaio          x86_64          0.3.109-13.el7             base           24 k

Transaction Summary
================================================================================
Install  1 Package

Total download size: 24 k
Installed size: 38 k
Downloading packages:
libaio-0.3.109-13.el7.x86_64.rpm                           |  24 kB   00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : libaio-0.3.109-13.el7.x86_64                                 1/1 
  Verifying  : libaio-0.3.109-13.el7.x86_64                                 1/1 

Installed:
  libaio.x86_64 0:0.3.109-13.el7                                                

Complete!

继续执行初始化配置
usr/local/mysql/bin/mysqld --user=mysql --basedir=/usr/local/mysql/ --datadir=/usr/local/mysql/data --initialize

2018-11-16T08:01:18.486398Z 0 [Warning] TIMESTAMP with implicit DEFAULT value is deprecated. Please use --explicit_defaults_for_timestamp server option (see documentation for more details).
2018-11-16T08:01:19.587502Z 0 [Warning] InnoDB: New log files created, LSN=45790
2018-11-16T08:01:19.712224Z 0 [Warning] InnoDB: Creating foreign key constraint system tables.
2018-11-16T08:01:19.777256Z 0 [Warning] No existing UUID has been found, so we assume that this is the first time that this server has been started. Generating a new UUID: cd30ca9d-e975-11e8-92d0-00163e0ebdbe.
2018-11-16T08:01:19.779704Z 0 [Warning] Gtid table is not ready to be used. Table 'mysql.gtid_executed' cannot be opened.
2018-11-16T08:01:19.780244Z 1 [Note] A temporary password is generated for root@localhost: ,sa1fz1vTpB*

初始化配置成功

7、修改 etc/my.cnf配置

用vi  vi etc/my.cnf按i输入修改

datadir=/usr/local/mysql/data
basedir=/usr/local/mysql
socket=/tmp/mysql.sock
user=mysql
#端口
port=3306 
#编码格式
character-set-server=utf8
#取消密码验证
skip-grant-tables
# Disabling symbolic-links is recommended to prevent assorted security risks
symbolic-links=0
# Settings user and group are ignored when systemd is used.
# If you need to run mysqld under a different user or group,
# customize your systemd unit file for mariadb according to the
# instructions in http://fedoraproject.org/wiki/Systemd

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d

修改完成 wq保存退出。


8、开启服务

# 将mysql加入服务

cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysql

# 开机自启

chkconfig mysql on

# 开启

service mysql start

9、设置密码

# 登录（由于/etc/my.cnf中设置了取消密码验证，所以此处密码任意）

/usr/local/mysql/bin/mysql -u root -p

# 操作mysql数据库

>>use mysql;

# 修改密码

>>update user set authentication_string=password('你的密码') where user='root';

>>flush privileges;

>>exit;

10、将/etc/my.cnf中的skip-grant-tables删除

11、登录再次设置密码（不知道为啥如果不再次设置密码就操作不了数据库了）

/usr/local/mysql/bin/mysql -u root -p

 >>ALTER USER 'root'@'localhost' IDENTIFIED BY '修改后的密码';

>>exit;


