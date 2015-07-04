---
title:  重拾树莓派笔记
date:   2014-03-02 12:45:58  +0800
---

自己的树莓派自去年暑假使用过一次后就吃灰到现在了，最近又是有些时间鼓捣新鲜事物，于是拾起了树莓派并且 Rebuild 了下。

## 重写 SD 卡

总是有种喜新厌旧的感觉，并不是想用以前配置过的 SD 卡以及系统，于是还是重新在官网下载了最新的 [Raspbian](http://www.raspberrypi.org/downloads)，使用 [Pi Filler](http://ivanx.com/raspberrypi/) 重写了 SD 卡。

## 初始配置

使用 [之前记录的方法](http://dushunfan.com/2013/06/02/how-to-set-up-raspberry-pi-without-monitor/) 用 SSH 成功链接上了在同一局域网内的树莓派：

    sudo raspi-config

安装 Vim：

    sudo apt-get install vim

修改 Debian 源：

    sudo vim /etc/apt/sources.list

修改为例如清华大学的 Debian 源：

    deb http://mirrors.tuna.tsinghua.edu.cn/raspbian/raspbian/ wheezy main contrib non-free rpi

更新包：

    sudo apt-get update
    sudo apt-get upgrade

## 网络

我的树莓派同时还安装了无线网卡，所以接下来我要让树莓派每次启动时自动连接我家的 WiFi。

安装 Python pip：

    sudo apt-get install python-pip
    sudo pip install pip --upgrade

修改 PyPI 源：

    mkdir .pip
    vim ~/.pip/pip.conf

写入以下内容：

    [global]
    index-url = http://pypi.tuna.tsinghua.edu.cn/simple

安装 wifi：

    sudo pip install wifi

搜索附近的 WiFi：

    wifi scan
    sudo wifi add WiFi_NAME

因为自动连接已知网络的过程并不是一项服务，所以我直接把他放在了 Crontab 里：

    sudo crontab -e

在尾行写入：

    @reboot /usr/local/bin/wifi autoconnect

值得注意的是，在这里必须使用完整的 wifi binary 路径，而不是 wifi autoconnect。