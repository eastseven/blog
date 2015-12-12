title: 基于docker的开发环境部署
date: 2015-12-12 09:56:14
tags:
 - docker
 - gitlab
 - redmine
 - nexus oss
 - jenkins
 - openldap
categories: note
---

## 前言

本文使用docker及daocloud部署一套适合个人及小团队开发用的环境.

由于gitlab,redmine等工具安装过程繁琐复杂,不便于小白用户维护,所以选用docker进行安装部署。

但是由于众所周知的原因,docker镜像的下载速度非常慢,所以选用[DaoCloud](https://www.daocloud.io)来加速docker镜像的下载.


## 部署环境
 - 阿里云CentOS7

### 1.安装 [Docker](https://www.docker.com/)

- 1.1 到[DaoCloud](https://www.daocloud.io)注册账号;成功后入下图:
![](/images/daocloud_01.png)

- 1.2 在[我的集群]中点击[添加主机],选择我已有一台主机,[CentOS];依照指示信息操作即可.
![](/images/daocloud_02.png)

- 1.3 安装成功后如下图:
![](/images/daocloud_03.png)

### 2.[GitLab](https://about.gitlab.com/)

- 2.1 到[镜像仓库],选择gitlab镜像.选择[sameersbn/gitlab]镜像,点击部署.经过多次部署试验,sameersbn大神制作的镜像比官方镜像好用百倍.
![](/images/daocloud_04.png)

- 2.2 部署
![](/images/daocloud_05.png)

- 2.3 配置,依照[配置文档](https://github.com/sameersbn/docker-gitlab/blob/master/README.md)进行配置.
![](/images/daocloud_06.png)

- 2.4 部署成功后,如下图:
![](/images/daocloud_07.png)

- 2.5 Script,对于不熟悉docker的同学,初次部署会遇到各种问题,提供如下脚本,方便部署.
[GitLab Docker 部署脚本](https://gist.github.com/eastseven/a95741a9772afddfe85c)

### 3.[Redmine](http://www.redmine.org/)

- 3.1 到[镜像仓库],选择redmine镜像.依然选择sameersbn制作的镜像.
![](/images/daocloud_08.png)

- 3.2 部署,配置都跟GitLab的一样.[配置文档](https://github.com/sameersbn/docker-redmine/blob/master/README.md)

- 3.3 Script
[Redmine Docker 部署脚本](https://gist.github.com/eastseven/13f9d96377183633fedf)

### 4.[Nexus OSS](http://www.sonatype.org/nexus/go/)

- 4.1 到[镜像仓库],选择nexus镜像.官方镜像sonatype/nexus即可.
- 4.2 Script
[Nexus OSS Docker 部署脚本](https://gist.github.com/eastseven/52794bf32941bcc6d2ae)

### 5.[Jenkins CI](https://jenkins-ci.org/)

- 5.1 [官方镜像](https://hub.docker.com/_/jenkins/) 有问题,没有安装成功.

### 6.[Open LDAP](http://www.openldap.org/)

- 6.1 [安装文档](http://www.server-world.info/en/note?os=CentOS_7&p=openldap&f=1) 此文档已经相当详细,照着配即可.

## 整合配置

上述工具安装部署完毕后，需要在宿主机上安装一个Nginx,然后配置域名,将docker应用跟域名做转发.

假设你的域名是example.com,那么可以给每个应用分配一个子域名.

- gitlab.example.com
- redmine.example.com
- maven.example.com
- ci.example.com
- ldap.example.com
- mail.example.com

nginx配置示例:

		server {
			listen 80;
			server_name gitlab.example.com;
			
			location / {
				proxy_pass http://127.0.0.1:10080;
			}
			
		}
		
重启nginx即可