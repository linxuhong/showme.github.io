MySQl 5.7源代码安装 使用tips

====

### 1、安装基础软件

```
 apt install make cmake gcc g++ perl bison libaio-dev libncurses5 libncurses5-dev libnuma-dev

```
### 2、安装基础软件
 >创建用户及组

 ```java
   usermod -a -G mysql mysql
   useradd mysql
   useradd -g mysql  mysql
```


### 3、安装基础软件
>创建mysql安装目录

```java
   mkdir -p /usr/local/mysql
   mkdir -p /usr/local/mysql/data
   mkdir -p /var/run/mysqld
   chown -R mysql:mysql  /usr/local/mysql

   cd /usr/local/mysql
   chgrp -R mysql .
   chgrp -R mysql .
 ```

### 4、下载boos
```java
   wget https://sourceforge.net/projects/boost/files/boost/1.59.0/boost_1_59_0.tar.gz
```

### 5、安装boos

```java

   tar xzvf boost_1_59_0.tar.gz
   cd boost_1_59_0
   ./bootstrap.sh
   ./b2 install
   cd /usr/local/

 ```

### 6、编译安装MySQL

 ```java

   wget http://mirrors.sohu.com/mysql/MySQL-5.7/mysql-5.7.23.tar.gz

   tar -xvf mysql-5.7.23.tar.gz
   cd mysql-5.7.23

   cmake . -DBUILD_CONFIG=mysql_release -DCPACK_MONOLITHIC_INSTALL=ON
          -DCMAKE_INSTALL_PREFIX=/usr/local/mysql -DDEFAULT_CHARSET=utf8
          -DDEFAULT_COLLATION=utf8_general_ci -DMYSQLX_TCP_PORT=33060
          -DMYSQL_UNIX_ADDR=/usr/local/mysql/mysql.sock
          -DMYSQL_TCP_PORT=3306
           -DMYSQLX_UNIX_ADDR=/usr/local/mysql/mysqlx.sock
           -DMYSQL_DATADIR=/usr/local/mysql/data
           -DSYSCONFDIR=/usr/local/mysql/etc
           -DENABLE_DOWNLOADS=ON -DWITH_BOOST=system
   make
   make install

 ```

### 7、启动验证

 ```java
   support-files/mysql.server  start

   cp support-files/mysql.server /etc/init.d/mysql.server
   mysql -u root -p

   vi /etc/my.cnf
 ```


###8、启动验证

 ```java
   support-files/mysql.server  start

   cp support-files/mysql.server /etc/init.d/mysql.server
   mysql -u root -p

   vi /etc/my.cnf
 ```

###8、让mysql可以支持其他机器连接
cd /user/local/mysql/bin
 ./mysql -uroot -p

+-----------+---------------+
| host      | user          |
+-----------+---------------+
| %         | magic         |
| %         | root          |
| localhost | mysql.session |
| localhost | mysql.sys     |
+-----------+---------------+

```java
use mysql;
select user,host from user;
update user set host ='%' where user ='root';
```

> mysql5.7安装后root 的默认密码不为空，请注意输出记住