---
title: Crawlab爬虫Docker部署流程
date: 2020-02-04 01:31:08
updated: 2020-04-30 16:31:00
tags:
  - docker
  - crawlab
  - python
categories:
  - linux
---

> 系统版本：CentOS 7.4 本文的系统命令都是在 root 账号下执行的，假如非 root 账号执行，提示没有权限，可在命令前加 sudo

系统版本：CentOS 7.4

本文的系统命令都是在 root 账号下执行的，假如非 root 账号执行，提示没有权限，可在命令前加`sudo`
<!--more-->

[](#一、更换yum源 "一、更换yum源")一、更换 yum 源
----------------------------------

1. 备份当前源

```shell  
cd /etc/yum.repos.d/
cp /CentOS-Base.repo /CentOS-Base-repo.bak
```

2. 下载阿里源 repo 文件

```shell  
wget http://mirrors.aliyun.com/repo/Centos-7.repo
```

3. 清理旧包

```shell  
yum clean all
```

4. 将阿里源设置为默认源

```shell  
mv Centos-7.repo CentOS-Base.repo
```

5. 生成阿里源缓存，更新 yum 源

```shell  
yum makecache
yum update
```

[](#二、安装docker "二、安装docker")二、安装 docker
---------------------------------------

1. 安装所需软件包 yum-util

```shell  
yum -y install yum-utils
```

2. 设置 yum 源

```shell  
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

3. 列出仓库中 docker 版本

```shell  
yum list docker-ce  --showduplicates |sort -r
```

![](/images/2020-02-04/1580793806750.jpg)  
4. 安装指定版本, 当前 centos 可用为 18.03.1.ce-1.el7.centos

```shell  
yum install docker-ce-18.03.1.ce-1.el7.centos
```

中途会有二次确认，按 Y 即可  
![](/images/2020-02-04/1580794235441.jpg)  
安装完成后输入`docker version`可查看当前版本  
![](/images/2020-02-04/1580794385803.jpg)

[](#三、安装python3 "三、安装python3")三、安装 python3
------------------------------------------

CentOS 7 自带 python 版本为 python2.7  
![](/images/2020-02-04/1580794502393.jpg)  
1. 安装依赖包

```shell  
yum -y groupinstall "Development tools"
yum -y install zlib-devel bzip2-devel openssl-devel ncurses-devel sqlite-devel readline-devel tk-devel gdbm-devel db4-devel libpcap-devel xz-devel
```

python3.7 以上版本，需要额外安装

```shell  
yum install libffi-devel -y
```

2. 下载 Python3 安装包 (当前为 3.7.6)

```shell  
wget https://www.python.org/ftp/python/3.7.6/Python-3.7.6.tar.xz
```

3. 新建 python3 存放目录

```shell  
mkdir /usr/local/python3
```

4. 安装 Python3

```shell  
tar -xvJf  Python-3.7.6.tar.xz
cd Python-3.7.6
./configure --prefix=/usr/local/python3
make && make install
```

5. 创建软链接

```shell  
ln -s /usr/local/python3/bin/python3 /usr/bin/python3
ln -s /usr/local/python3/bin/pip3 /usr/bin/pip3
```

6. 查看 python 版本  
`python3`  
![](/images/2020-02-04/1580795985639.jpg)

[](#三、Crawlab的安装 "三、Crawlab的安装")三、Crawlab 的安装
---------------------------------------------

1. 更新 yum

```shell  
yum update
```

2. 配置 docker 镜像源，提升速度  
创建 /etc/docker/daemon.json

```shell  
vi /etc/docker/daemon.json
```

按`i`开启编辑模式，在其中输入如下内容  

```shell  
{
"registry-mirrors": ["https://registry.docker-cn.com"]
}
```

`:wq`保存退出  
如果保存出错`"/etc/docker/daemon.json" E212: Can't open file for writing`  
那么手动创建`docker`文件夹后再创建`daemon.json`文件

```shell  
cd /etc
mkdir docker
cd /etc/docker
vim daemon.json
```

3. 下载 Crawlab 镜像  
启动 docker

```shell  
systemctl start docker
```

下载镜像

```shell  
docker pull tikazyq/crawlab:latest
```

![](/images/2020-02-04/1580801321843.jpg)  
4. 安装 docker-compose  
下载 Docker Compose 的当前稳定版本

```shell  
curl -L "https://github.com/docker/compose/releases/download/1.25.3/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
```

将可执行权限应用于二进制文件

```shell  
chmod +x /usr/local/bin/docker-compose
```

测试安装

```shell  
docker-compose --version
```

![](/images/2020-02-04/1580803627759.jpg)  
5. 查找 docker-compose.yml 文件位置

```shell  
find / -name "docker-compose.yml*"
```

![](/images/2020-02-04/1580803721033.jpg)

```shell  
cd /var/lib/docker/overlay2/XXXX/diff/app/
```

此处`XXXX`替换为你的路径  
6. 配置 docker-compose.yml 文件  
参考[官方文档](https://docs.crawlab.cn/Installation/Docker.html)  
7. 启动 Crawlab

```shell  
docker-compose up -d   #默认为守护进程模式，容器以后台方式运行
```

第一次启动会下载安装数据库 Redis 和 Mongodb  
![](/images/2020-02-04/1580806111415.jpg)  
8. 访问 Crawlab  
在浏览器中输入 `http://你的ip:8080`即可看到 Crawlab 界面  
⚠️在服务器防火墙放行 8080 端口  
![](/images/2020-02-04/1580808975234.jpg)  
9. 再启动  
更新后

```shell  
# 关闭并删除 Docker 容器
docker-compose down

# 拉取最新镜像
docker pull tikazyq/crawlab:latest

# 启动 Docker 容器
docker-compose up -d
```

重启后  
查找 docker-compose.yml 文件位置  
cd 到 docker-compose.yml 所在文件目录下  
启动

```shell  
docker-compose up -d
```

参考文档：  
1.[https://blog.csdn.net/kai402458953/article/details/100035672](https://blog.csdn.net/kai402458953/article/details/100035672)  
2.[https://blog.csdn.net/kai402458953/article/details/100026109](https://blog.csdn.net/kai402458953/article/details/100026109)  
3.[https://www.zhihu.com/question/277141716](https://www.zhihu.com/question/277141716)  
官方文档:  
[https://docs.crawlab.cn/Installation/Docker.html](https://docs.crawlab.cn/Installation/Docker.html)  
项目地址：  
[https://github.com/crawlab-team/crawlab](https://github.com/crawlab-team/crawlab)