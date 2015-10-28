title: Nginx配置SSL
date: 2015-10-28 10:19:18
tags:
 - nginx
 - ssl
 - openssl
 - centos
categories: note
---

#### 1.安装OpenSSL
    yum install -y openssl
    
查看nginx是否支持ssl
	/usr/sbin/nginx -V
    
#### 2.证书生成
	openssl genrsa -des3 -out openssl.key 1024
	openssl req -new -x509 -key openssl.key -out openssl.crt -days 3650
	openssl rsa -in openssl.key -out openssl_nopass.key
	
#### 3.Nginx配置
	server {
      listen       80;
      listen       443 ssl;
      server_name  www.debug7.com;
      ssl on;
      ssl_certificate      /etc/nginx/conf.d/openssl.crt;
      ssl_certificate_key  /etc/nginx/conf.d/openssl_nopass.key;

      ssl_ciphers  HIGH:!aNULL:!MD5;
      ssl_prefer_server_ciphers   on;

      location / {
          root   /usr/share/nginx/html;
          index  index.html index.htm;
      }
	}
	
#### 4.参考
* [5分钟完成nginx ssl配置](http://nassir.iteye.com/blog/1983667)
* [基于OpenSSL自建CA和颁发SSL证书](http://segmentfault.com/a/1190000002569859)
* [NGINX 配置 SSL 证书 + 搭建 HTTPS 网站教程](https://s.how/nginx-ssl/)
* [基于 OpenSSL 的 CA 建立及证书签发](http://rhythm-zju.blog.163.com/blog/static/310042008015115718637/)