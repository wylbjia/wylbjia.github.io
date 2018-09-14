---
layout:     post
title:      Uwsgi
subtitle:   Uwsgi 转发 Nginx
date:       2018-9-14
author:     Wylb
header-img: img/post-bg-desk.jpg
catalog: 	 true
tags:
    - Uwsgi
    - Django
    - Nginx
---
#### Uwsgi

​	首先弄清下面几个概念：

-  WSGI：全称是 Web Server Gateway Interface，WSTI 不是服务器，python 模块，框架，API 或者任何软件。只是一种规范，描述 。
- uwsgi：与WSGI一样，是uWSGI服务器的独占通信协议，用于定义传输信息的类型(type of information)。每一个uwsgi packet前4byte为传输信息类型的描述，与WSGI协议是两种东西，据说该协议是fcgi协议的10倍快。 
- uWSGI:是一个全功能的HTTP服务器，实现了 WSGI协议，uwsgi协议，http

​	Django 使用 runserver 可以使我们的 django 项目在本地运行起来，在生产环境部署 django ，需要考虑一些问题，比如静态文件处理、安全、效率等。这样用 Django 自带的服务器效果就不是很好了，我们需要使用 Nginx 通过 Uwsgi 把 Django 跑起来。

---

##### 安装 uwsgi

​	uwsgi 是 python 的一个模块，安装只需简单的 pip 命令。

```
$ pip install uwsgi
$ uwsgi --version
```

##### 测试 uwsgi 是否正常

​	新建 test.py 文件：

```
def application(env, start_response):
    start_response('200 OK', [('Content-Type','text/html')])
    return "Hello World"
```

```
$ uwsgi --http :8001 --wsgi-file test.py    ##打开浏览器 可以考到对应的效果
```

#####  Django项目

​	首先，我们要有一个可以运行的 Django 项目，如果你不知道怎么运行 Django 项目，本博主近期会推出一个介绍 Django的博客，希望大家可以继续关注。

系统环境

---

- Centos 7.2

- python 2.7

- Django 1.11

- Nginx 1.12

- uwsgi 2.0.17

- IP 192.168.1.211

  已经安装好的 Django 服务器位于 /usr/web 目录下，项目结构如下图所示：

```
web
├── db.sqlite3
├── deploy
│   ├── admin.py
│   ├── admin.pyc
│   ├── __init__.py
│   ├── __init__.pyc
│   ├── migrations
│   │   ├── __init__.py
│   │   └── __init__.pyc
│   ├── models.py
│   ├── models.pyc
│   ├── tests.py
│   ├── views.py
│   └── views.pyc
├── html
│   └── table.html
├── manage.py
├── static
└── web
    ├── __init__.py
    ├── __init__.pyc
    ├── settings.py
    ├── settings.pyc
    ├── urls.py
    ├── urls.pyc
    ├── wsgi.py
    └── wsgi.pyc
```

​	我们在 html 文件 文件夹新建了一个 table.html 文件，下面我们通过  Django 的 runserver 命令来启动这个服务器。

```
$web python manage.py runserver 0:7000     
```

​	我们打开浏览器，输入 192.168.1.211:7000/table.html，如下图我们可以看到页面可以正常显示，如果这一步你都做不出来，建议先去补习一下 Django 方面的知识，接下来我们将用 Uwsgi 启动这个项目。

![](E:\文档\Django\Django.png)



####  Uwsgi 启动

 #### 命令启动

- 进入 Django 项目

  ```
  $web uwsgi --http 192.168.1.211:8080 --file web/wsgi.py --static-map=/static=static
  ```

  - http ：指定端口
  - file ：指定文件
  - static-map ：静态文件

  我们打开浏览器，如下图所示，可以看到也是可以正常启动的。

  ![](E:\文档\Django\uwsgi.png)

  

#### 配置文件启动

- 在django项目同级目录创建script目录，用于存放配置脚本等等 

- 进入/script目录，创建一个uwsgi.ini文件 

  ```
  # uwsig使用配置文件启动
  [uwsgi]
  # 项目目录
  chdir=/usr/web/
  # 指定项目的application
  module=web.wsgi:application
  # 指定sock的文件路径       
  socket=/usr/web/script/uwsgi.sock
  # 进程个数       
  workers=5
  pidfile=/usr/web/script/uwsgi.pid
  # 指定IP端口       
  http=192.168.1.211:8080
  # 指定静态文件
  static-map=/static=/usr/web/static
  # 启动uwsgi的用户名和用户组
  uid=root
  gid=root
  # 启用主进程
  master=true
  # 自动移除unix Socket和pid文件当服务停止的时候
  vacuum=true
  # 序列化接受的内容，如果可能的话
  thunder-lock=true
  # 启用线程
  enable-threads=true
  # 设置自中断时间
  harakiri=30
  # 设置缓冲
  post-buffering=4096
  # 设置日志目录
  daemonize=/usr/web/script/uwsgi.log
  ```

- ```
  $ uwsgi --ini uwsgi.ini
  [uWSGI] getting INI configuration from uwsgi.ini
  [uwsgi-static] added mapping for /static => /usr/web/static
  ```

- uwsgi 的日志文件会生成在相应文件夹

### Nginx 配置

​	接下来的工作是我们的最后一步，我们要把 Nginx 与上述服务器 对接起来，其实很简单，只需要修改配置文件就好了。如下图所示，我们添加location。

```
        location ^~ /table {
            include  uwsgi_params;
            uwsgi_read_timeout 30;
            uwsgi_pass unix:/usr/web/script/uwsgi.sock;
        }
        location /static/ {
        	alias /usr/web/static/;
        	index index.html index.htm;
        }
```



​	然后重启 Nginx，接下来打开 Nginx 对应页面，我们就可以看到我们的页面啦。

```
$ ./sbin nginx -t
$ ./sbin nginx -s reload
```































​	











