---
title:  Linode 下快速配置 CentOS 6
date:   2013-03-17 17:53:31  +0800
---

之前自己 Rebuild 多次，于是就写下了笔记，以后就方便了。不过这里是 CentOS 6 的基础配置，安全性没有考虑。

hostname 取名，eileen 替换为你喜欢的名字：

    echo "HOSTNAME=eileen" >> /etc/sysconfig/network
    hostname "eileen"

开启 vim 的彩色显示代码，行号标记和鼠标定位的功能：

    vim /etc/vimrc

在最后加上：

    syntax on
    set nu
    set mouse=a

作为完完全全的新手，需要掌握 vim 的最基本指令：

保存：control + c，输入 :wq（注意‘:’也要）
取消保存并退出：control + c，输入:q，如果退出失败，试试:q!

修改时区：（之后会有确认的步骤，输入 yes 就行了）

    cp -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

修改 hosts：

    vim /etc/hosts

修改为：

    127.0.0.1      localhost.localdomain localhost
    12.345.678.999 eileen.perry.com      eileen

这里的 12.345.678.999 更换为自己的IP地址，eileen.perry.com 可以直抄，也可以随意填写，和你要host的域名无关。

更新系统：

    yum update

修改 resolv.conf：

    vim /etc/resolv.conf

打开 Linode 控制面板里的 Remote Access，找到 DNS Resolvers，第一个和第二个地址对应 2 个 nameserver：

    domain members.linode.com
    search members.linode.com
    nameserver 12.345.678.9
    nameserver 98.765.432.1
    options rotate

静态 IP 设置：

    vim /etc/sysconfig/network-scripts/ifcfg-eth0

打开 Linode 控制面板里的 Remote Access，找到 Public IPs，前者填写在 IPADDR 中，后者填写在 NETMASK 中。Public IPs 的下面 Default Gateways 中的地址填写在 GATEWAY 中：

    DEVICE=eth0
    BOOTPROTO=none
    
    ONBOOT=yes
    
    IPADDR=12.345.678.999
    NETMASK=255.255.255.0
    GATEWAY=12.345.678.9

重启网络：

    service network restart

如果重启成功的话，那么基础配置就完成了。

## 参考

* [Getting Started](http://library.linode.com/getting-started)
* [Linode VPS 上 CentOS 6 安装 LAMP + phpMyAdmin 记录](http://cnzhx.net/blog/vps-centos-6-lamp-phpmyadmin/)
