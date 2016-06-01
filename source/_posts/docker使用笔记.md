title: docker使用笔记
date: 2016-02-29 08:20:49
tags:
---
### 1.安装 Docker
##### *** [官方文档](https://docs.docker.com/engine/installation/linux/centos/) ***
1.添加源

```
sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF
```
1.1 通过脚本添加源

```
curl -fsSL https://get.docker.com/ | sh
```

1.2 通过daocloud安装

```
curl -sSL https://get.daocloud.io/docker | sh
```

2.安装

```
sudo yum install docker-engine
```

安装中可能遇到的错误信息

```
Transaction check error:
  file /usr/lib/systemd/system/blk-availability.service from install of device-mapper-7:1.02.107-5.el7_2.1.x86_64 conflicts with file from package lvm2-7:2.02.105-14.el7.x86_64
  file /usr/sbin/blkdeactivate from install of device-mapper-7:1.02.107-5.el7_2.1.x86_64 conflicts with file from package lvm2-7:2.02.105-14.el7.x86_64
  file /usr/share/man/man8/blkdeactivate.8.gz from install of device-mapper-7:1.02.107-5.el7_2.1.x86_64 conflicts with file from package lvm2-7:2.02.105-14.el7.x86_64
```

由于lvm2包冲突造成上述错误，解决办法，卸载lvm2包，再重新安装docker

```
rpm -qa|grep lvm2

lvm2-2.02.105-14.el7.x86_64
lvm2-libs-2.02.105-14.el7.x86_64
```

```
sudo rpm -e lvm2-libs-2.02.105-14.el7.x86_64
```

3.启动

```
sudo service docker start
```

### 2.安装 Docker Compose
##### *** [官方文档](https://docs.docker.com/compose/install/) ***

##### 2.1 脚本安装
```
$ curl -L https://github.com/docker/compose/releases/download/1.6.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
$ chmod +x /usr/local/bin/docker-compose
$ docker-compose --version
```
##### 2.2 python pip 安装
```
$ yum search python-pip
```

如果没有找到，只有手动安装pip，[传送门](https://pip.pypa.io/en/stable/installing/)

```
$ wget https://bootstrap.pypa.io/get-pip.py
$ python get-pip.py
```
```
$ pip install docker-compose
```

### 3.更换 Docker 镜像存储位置
Docker 镜像的默认存放路径是/var/lib/docker/。在阿里云中，默认系统盘只提供了20G存储空间，如果在系统中运行多个container 且镜像较大，20G空间显然不够用。这样就需要修改存放路径。

```
$ mkdir /data/docker
$ systemctl stop docker
$ rsync -aXS /var/lib/docker/  /data/docker/
$ vim /etc/fstab
	# 加入 /data/docker /var/lib/docker none bind 0 0
$ mount -a
$ systemctl start docker
```

### 4.安装Docker Registry [官方推荐](https://hub.docker.com/_/registry/)

```
$ docker run \
         -e SETTINGS_FLAVOR=s3 \
         -e AWS_BUCKET=acme-docker \
         -e STORAGE_PATH=/registry \
         -e AWS_KEY=AKIAHSHB43HS3J92MXZ \
         -e AWS_SECRET=xdDowwlK7TJajV1Y7EoOZrmuPEJlHYcNP2k4j49T \
         -e SEARCH_BACKEND=sqlalchemy \
         -p 5000:5000 \
         registry
```

