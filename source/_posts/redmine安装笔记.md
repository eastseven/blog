title: redmine安装笔记
date: 2015-11-08 10:44:27
tags:
 - nginx
 - centos
 - centos
categories: note
---

### 配置环境[操作系统]

  - 阿里云centos7 64位
  - 内存1024M
  - 带宽1Mbps
  
### 准备
安装必要的软件包
    
    yum install ruby ruby-devel 
    yum curl crul-devel gcc 
    yum libxml2 libxml2-devel 
    yum openssl openssl-devel 
    yum ImageMagick ImageMagick-devel
    
更换ruby安装源

	gem sources --add https://ruby.taobao.org/ --remove https://rubygems.org/
	
### 安装
1.下载 [redmine 3.1.1](http://www.redmine.org/releases/redmine-3.1.1.zip) [官方安装文档](http://www.redmine.org/projects/redmine/wiki/RedmineInstall)

2.解压
    
    unzip redmine-3.1.1.zip
    
3.配置数据库

    cp redmine-3.1.1/config/database.yml.example redmine-3.1.1/config/database.yml
    
  修改database.yml，填写redmine数据库连接信息，可以将test和development库的配置注释掉。
  
3.1 如果没有安装数据库

    yum install mariadb mariadb-server mariadb-devel mariadb-libs -y
    
    vim /etc/my.cnf
    
    # add follows within [mysqld] section
	[mysqld]
	character-set-server=utf8
	
	systemctl enable mariadb
	systemctl start mariadb
	
创建数据库及账号

    create user redmine identified by '替换成你自己密码';
	create database redmine character set utf8;
	grant all privileges on redmine.* to 'redmine'@'localhost' identified by '替换成你自己密码' with grant option;
	grant all privileges on redmine.* to 'redmine'@'%' identified by '替换成你自己密码' with grant option;
	flush privileges;
  
4.修改Gemfile源地址

    vim redmine-3.1.1/Gemfile
    source 'https://ruby.taobao.org'
    
5.开始安装

    gem install bundler
    bundle install --without development test  
    bundle exec rake generate_secret_token
    
    RAILS_ENV=production bundle exec rake db:migrate
	RAILS_ENV=production bundle exec rake redmine:load_default_data
	
6.启动测试

    bundle exec rails server webrick -e production
    curl http://localhost:3000
    
改方法只能在本机上访问redmine，如果需要其他机器访问redmine，需要安装Niginx

### Nginx
1.passenger的安装

    gem install passenger
    passenger-install-nginx-module
依照提示一步一步回车即可，需要注意的是，该命令安装的nginx是源码安装，所以你可以下载一个指定版本的nginx源码包或者使用命令默认的源码包来安装nignx，推荐自定义安装。[传送门](http://nginx.org/en/download.html)

如果安装中遇到编译nginx内存不足的问题，根据提示做如下操作：

    sudo dd if=/dev/zero of=/swap bs=1M count=1024
    sudo mkswap /swap
    sudo swapon /swap
    
安装成功后的nginx.conf配置，会在http中添加如下代码：

    http {
      ...
      passenger_root /usr/local/share/gems/gems/passenger-5.0.21;
      passenger_ruby /usr/bin/ruby;
      ...
    }
    
此时只需要配置一个redmine.conf即可，记得将此配置include到nginx.conf中

	server {
    	listen 80;
    	server_name 域名;
    	root REDMINE_PATH/public;
    	passenger_enabled on;
	}    

2.启动

    /your/path/nginx
    
3.关闭

    /your/path/nginx -s stop

4.重启    

    /your/path/nginx -s reload
	
### 参考
- [针对于新入门的开发者，如何在 Mac 下用 Nginx + Passenger 部署 Rails 的运行环境](https://ruby-china.org/wiki/mac-nginx-passenger-rails)
- [如何用nginx+passenger署Rails](http://www.worldhello.net/2010/06/09/1341.html)
