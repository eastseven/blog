title: install ecshop with php5.2
date: 2015-08-17 08:43:14
tags:
 - php
 - php5.2
 - ecshop
 - centos7
categories: guide
---

### 准备

* VirtualBox
* CentOS7
* PHP5.2.17 [源码下载地址](http://museum.php.net/php5/php-5.2.17.tar.gz) [PHP-FPM补丁下载地址](http://php-fpm.org/downloads/php-5.2.17-fpm-0.5.14.diff.gz)

安装ipconfig

    yum install net-tools

安装必要工具
    
    yum install wget vim openssh-server gcc

#### 1. MySQL
* [下载RPM](http://dev.mysql.com/downloads/repo/yum/)
* [安装RPM](http://dev.mysql.com/doc/mysql-yum-repo-quick-guide/en/)


1.1 安装
    
    yum install mysql mysql-devel
    
1.2 如果MySQL服务器是在本机

    yum install mysql-server

#### 2. Nginx

1.安装

    rpm -Uvh http://nginx.org/packages/centos/7/noarch/RPMS/nginx-release-centos-7-0.el7.ngx.noarch.rpm
    yum install nginx
    systemctl start nginx.service

#### 3. 编译PHP5.2.12

3.1 安装依赖包

	yum install libxml2 libxml2-devel gd gd-devel autoconf
    yum install libjpeg libjpeg-devel libpng libpng-devel
    yum install openssl openssl-devel


3.2 如果configure的时候还是报找不到libjpeg或者libpng，则需如下操作

    ln -s /usr/lib64/libjpeg.so /usr/lib/
    ln -s /usr/lib64/libpng.so /usr/lib/
    ln -s /usr/lib64/mysql /usr/lib/mysql

3.3 打补丁，不然编译要报错，需要在php源码目录内执行下列操作

    yum install patch
    wget https://mail.gnome.org/archives/xml/2012-August/txtbgxGXAvz4N.txt
    patch -p0 -b <txtbgxGXAvz4N.txt
    gzip -cd php-5.2.17-fpm-0.5.14.diff.gz | patch -d php-5.2.17 -p1

3.4 编译

    ./configure --with-gd --with-mysql=/usr/bin/mysql --enable-fastcgi --enable-fpm --with-jpeg-dir=/usr/lib --with-openssl
    make
    make install

4.5 后续操作

    修改php.ini，short_open_tag = On
    修改php.ini，cgi.fix_pathinfo=1
    关闭防火墙：systemctl stop firewalld
    修改php-fpm.conf，取消<value name="user">nobody</value>及<value name="group">nobody</value>的注释，否则启动php-fpm会失败
    配置nginx的php
4.5.1 nginx 403问题		
如果权限和配置都设置正确的情况下，还是报403，可能是SELinux开启的问题，关掉即可 [传送门](https://www.centos.org/docs/5/html/5.1/Deployment_Guide/sec-sel-enable-disable.html)
