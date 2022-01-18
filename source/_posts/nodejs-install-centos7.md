---
title: CentOS7中nodejs的安装
date: 2020-02-04 02:31:08
updated: 2020-04-30 17:31:00
tags:
  - nodejs
  - centos7
categories:
  - linux
---
> 若使用 yum install nodejs npm -y 安装，安装后的 nodejs 版本为 6.17.1，版本低，部分应用运行会报错。
<!--more-->


如果已经使用`yum install nodejs npm -y`安装，则需要先卸载当前 nodejs

[](#1-卸载当前nodejs  "1.卸载当前nodejs")1\. 卸载当前 nodejs

----------------------------------------------

```shell  

yum remove nodejs npm -y

```

[](#2-删除下面目录下node相关内容  "2.删除下面目录下node相关内容")2\. 删除下面目录下 node 相关内容

--------------------------------------------------------------

```shell  

/usr/local/lib

/usr/local/include

/usr/local/bin

```

[](#3-下载nodejs  "3.下载nodejs")3\. 下载 nodejs

----------------------------------------

目前官网最新的 nodejs 为 12.14.1

```shell  

wget https://nodejs.org/dist/v12.14.1/node-v12.14.1-linux-x64.tar.xz

```

[](#4-解压  "4.解压")4\. 解压

---------------------

```shell  

xz -d node-v12.14.1-linux-x64.tar.xz

tar -xf node-v12.14.1-linux-x64.tar

```

[](#5-确认nodejs的路径  "5.确认nodejs的路径")5\. 确认 nodejs 的路径

--------------------------------------------------

上述下载位置为`~/node-v12.14.1-linux-x64`

[](#6-创建软链接  "6.创建软链接")6\. 创建软链接

------------------------------

⚠️必须给全路径

```shell  

ln -s ~/node-12.14.1-linux-x64/bin/node /usr/bin/node

ln -s ~/node-v12.14.1-linux-x64/bin/npm /usr/bin/npm

```

[](#7-测试nodejs安装  "7.测试nodejs安装")7\. 测试 nodejs 安装

-----------------------------------------------

```shell  

node -v

npm -v

```

正确输出版本号则安装完毕
