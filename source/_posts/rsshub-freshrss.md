---
title: RSSHub及FreshRSS搭建踩坑
date: 2022-01-14 02:31:08
tags:
  - RssHub
  - FreshRss
categories:
  - linux
---
## RSSHub搭建，使用docker Compose  

[官方文档][1]
### 安装 
下载docker-compose.yml  
```shell  
wget https://raw.githubusercontent.com/DIYgod/RSSHub/master/docker-compose.yml
```
创建 volume 持久化 Redis 缓存  
```shell  
$ docker volume create redis-data
```
启动  
```shell 
$ docker-compose up -d
```  
<!--more-->



浏览器访问 `ip:1200` 看到以下界面即部署完成
>如果是本地部署，则访问`localhost:1200`

![2016493769.png][2]
### docker-compose配置文件  
[配置文件文档][3]
```YML 
version: '3'

services:
    rsshub:
        image: diygod/rsshub
        restart: always
        ports:
            - '1200:1200' #监听端口
        environment:
            NODE_ENV: production #是否显示错误输出（production 关闭输出）
            CACHE_TYPE: redis #缓存类型
            REDIS_URL: 'redis://redis:6379/' # Redis 连接地址
            PUPPETEER_WS_ENDPOINT: 'ws://browserless:3000' #用于 puppeteer.connect 的浏览器 websocket 链接
        depends_on:
            - redis
            - browserless

    browserless:
        # See issue 6680
        image: browserless/chrome:1.43-chrome-stable
        restart: always
        ulimits:
          core:
            hard: 0
            soft: 0

    redis:
        image: redis:alpine
        restart: always
        volumes:
            - redis-data:/data

volumes:
    redis-data:
```




----------------------------

## FreshRSS搭建  

### 安装FreshRSS  

 在宝塔创建站点，选择数据库MySQL 

![786969596][4]  
>Tips：可以第一行随意填写一个域名，第二行填写ip:ports，建站完成后在网站设置中删除第一行的域名。
>这样可以实现单ip无域名建立多个网站，不浪费服务器资源  

 前往`/www/wwwroot/`目录，打开刚刚创建网站根目录，删除根目录中宝塔默认创建的所有文件（`.user.ini`可忽略）
从[FreshRSS官网][5]下载源码，并解压到根目录。 

 打开软件商店-PHP设置-禁用函数，找到`openlog`和`syslog`，点删除取消禁用
选择 安装扩展 ，安装`fileinfo`，耐心等待安装完成。
![2550650747.png][6]

 访问创建网站时设置的域名或ip地址，即可进入FreshRSS安装程序。  
根据网页提示设置用户名及密码，数据库地址填`localhost`本地数据库，数据库用户名、数据库名、数据库密码根据之前创建网站时的信息填写。 

### FreshRSS配置认证  

![3635047765.png][7]
登陆后在右上角齿轮中打开设置，点击认证
勾选 
![708798026.png][8]   

在左侧用户账户选项中，最下方的API管理中输入想要设定的api密码，点击钥匙图标保存并提交

![2344587124.png][9]  

下方的地址即是用于手机端RSS应用的地址  

----------------------------------  

## RSSHub chrome插件  

仓库地址[RssHub Radar][10] 

安装后打开插件设置，将`自定义 RSSHub 域名`更改为你的RSSHub域名  
在下方一键订阅中勾选`FreshRSS `，链接可在FreshRSS 设置-用户账户-认证口令下方找到  
![2622813880.png][11]  

![1061422794.png][12]   


>注意，如果未配置https，请务必不要选择https，否则会出现下面的情况![2873879450.png][13]

-----------------  

## 使用  

你可以找到任何一个RSSHub所支持的网站[支持路由列表][14]   
此时点击RssHub Radar插件，点击一键订阅，即可订阅当前网站


  [1]:https://docs.rsshub.app/install/#docker-compose-bu-shu 
  [2]:/images/2022-01-14/2016493769.png
  [3]:https://docs.rsshub.app/install/#pei-zhi 
  [4]:/images/2022-01-14/786969596.png
  [5]:https://www.freshrss.org/ 
  [6]: /images/2022-01-14/2550650747.png
  [7]: /images/2022-01-14/3635047765.png
  [8]: /images/2022-01-14/708798026.png
  [9]: /images/2022-01-14/2344587124.png
  [10]: https://github.com/DIYgod/RSSHub-Radar/
  [11]: /images/2022-01-14/2622813880.png
  [12]: /images/2022-01-14/1061422794.png
  [13]: /images/2022-01-14/2873879450.png
  [14]: https://docs.rsshub.app/social-media.html
