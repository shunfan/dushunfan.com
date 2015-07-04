---
title:  CentOS 6 下使用 Nginx，Gunicorn 以及 Supervisor 部署 Flask 应用
date:   2013-03-17 18:32:55  +0800
---

Flask 应用的部署刚开始确实有点伤脑筋，主要还是自己第一次接触这个领域，对一些模式只有略知一二。上个星期花费了很多时间才部署完毕，今天真巧有空发一下自己的笔记：（非 root 用户记得加 sudo）

## 安装基础环境

    yum install python python-devel libxml2 libxml2-devel python-setuptools zlib-devel wget openssl-devel pcre pcre-devel sudo gcc make autoconf automake

## 安装pip

[获取最新版的 pip](https://pypi.python.org/pypi/pip)

    wget https://pypi.python.org/packages/source/p/pip/pip-1.3.1.tar.gz
    tar -xzvf pip-1.3.1.tar.gz
    cd pip-1.3.1
    python setup.py install

## 安装 Virtualenv

    cd ~
    pip install virtualenv

在 /projects 目录下新建一个 Project：

    virtualenv /projects/project
    source /projects/project/bin/activate

## 安装 Flask

    pip install Flask

在 /projects/project 目录下新建一个 project.py，内容是:

{% highlight python %}
from flask import Flask
app = Flask(__name__)

@app.route('/')
def index():
    return "Hello world!"

if __name__ == '__main__':
    app.run()
{% endhighlight %}

## 安装 Gunicorn 和 Supervisor

    pip install gunicorn
    easy_install supervisor

### 配置 Supervisor

    vim /etc/supervisord.conf

在里面写入：（1234 可以更改为你喜欢的端口，只要和其他的端口不重合就行。root 也可改为其他用户）

    [program:project]
    command = /projects/project/bin/python /projects/project/bin/gunicorn -b 127.0.0.1:1234 project:app
    directory = /projects/project/
    user = root
    autostart = true
    autorestart = true
    stderr_logfile = /projects/project/logs/stderr.log
    stdout_logfile = /projects/project/logs/stdout.log

新建 logs 文件夹：

    cd /projects/project
    mkdir logs

### 开启 Supervisor

    supervisord -c /etc/supervisord.conf
    supervisorctl reread
    supervisorctl update
    supervisorctl start project

需要注意的是，每次更新 /etc/supervisord.conf 都要使用reread和update的指令。

如果开启失败，可以查看 logs，整改后重启：

    supervisorctl restart project

## 安装 Nginx

    cd ~
    rpm http://nginx.org/packages/centos/6/noarch/RPMS/nginx-release-centos-6-0.el6.ngx.noarch.rpm
    yum install nginx
    /etc/init.d/nginx start
    chkconfig --add nginx
    chkconfig nginx on

### 配置 Nginx

新建 conf：

    vim /etc/nginx/conf.d/project.conf

写入：（如果端口不是 1234，注意下面的端口需要修改）

{% highlight nginx %}
server {
    listen 80;
    server_name yourdomain.com;
    
    access_log /projects/project/logs/access.log;
    error_log /projects/project/logs/error.log;
    
    location / {
        proxy_set_header X-Forward-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        if (!-f $request_filename) {
            proxy_pass http://127.0.0.1:1234;
        }
    }
    
    location ~.*(js|css|png|gif|jpg|mp3|ogg)$ {
        root /projects/project/;
    }
}
{% endhighlight %}

### 重启 Nginx

    service nginx reload

现在打开网址，应该就可以看到小小的“Hello world!”了。

## 参考

* [How to Run Flask Applications with Nginx Using Gunicorn](http://www.onurguzel.com/how-to-run-flask-applications-with-nginx-using-gunicorn/)
* [Managing Gunicorn Processes With Supervisor](http://www.onurguzel.com/managing-gunicorn-processes-with-supervisor/)
