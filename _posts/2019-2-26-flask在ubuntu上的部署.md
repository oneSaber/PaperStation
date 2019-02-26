---
layout: post
title: "flask在ubuntu上的部署"
date: 2019-2-26 19:00：00 +0300
categories:
- flask,Python
tags:
- flask,Python
---
在使用flask-script 的情况下，最为蛋疼的一点其实是uwsgi并不是像预期的那样运行，本来写在

```if __name__ == "__main__"```

下面的set_route 函数没法正常运行，只好写在manager.py 外面

在经过极其蛋疼的在Ubuntu上安装python3.7以及各种flask 和用到的库后（这里的一个坑是关于openssl的蛋疼问题，python 依赖了这个C库，在编译python之前最好确认你们的相关依赖是否已经安装完毕。

在python安装完成之后，使用

```pip install uwsgi``` 

安装uwsgi作为flask 的容器，同时使用nginx作为反向代理

### 1. 配置uwsgi

在项目目录下建立新的ini文件, config.ini 作为uwsgi的配置文件，然后按照最简单的配置

```
[uwsgi]
master=true
wsgi-file = manage.py
callable = app
socket = :3031
chdir = /root/guochuang
stats = 0.0.0.0:9191
```

配置完成后使用 ```uwsgi config.ini``` 启动uwsgi

### 2.配置nginx

安装nginx 的简单办法就是 ```apt install nginx```
进入 /etc/nginx/conf.d
为你的项目建立一个conf 文件，例如：

```
server {
        listen       80;
        # 默认的web访问端口
        server_name  0.0.0.0;
        # 你的公网ip

        access_log  YourProjectDir/logs/access.log;
        # 服务器接收的请求日志
        # 需要在项目文件夹下创建
        # logs文件夹，下同。
        error_log   YourProjectDir/logs/error.log;
        # 错误日志

        location / {
            include        uwsgi_params;
            # 这里是导入的uwsgi配置
            uwsgi_pass     127.0.0.1:3031;
            # 需要和uwsgi的配置文件里socket项的地址
            # 相同,否则无法让uwsgi接收到请求。
            uwsgi_param UWSGI_PYHOME YourPythonEenv;
            # python的位置(如果有虚拟环境则是虚拟环境下)

            uwsgi_param UWSGI_CHDIR   YourProjectDir;
            # 项目根目录

            uwsgi_param UWSGI_SCRIPT manage:app;
            # 启动项目的主程序(在本地上运行
            # 这个主程序可以在flask内置的
            # 服务器上访问你的项目)
        }
}
```

然后注释掉 nginx.conf 中的 # include /etc/nginx/sites-enabled/*;就可以启动nginx 了，启动为 nginx