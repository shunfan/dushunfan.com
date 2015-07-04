---
title:  如何为新的 Debian VPS 做好基础的安全保障
date:   2014-02-01 19:41:51 +0800
---

当你刚刚购买了一个 Debian VPS，我想第一步要做的就是做好安全保障，确保服务器能抵抗低级的攻击。接下来就是我在第一时间“安全化”我的 Debian 的步骤。

当你第一次用 SSH 进入你的 VPS 的时候，一般是以 root 身份，所以第一步我们要修改 root 密码：

    passwd

接下来更新系统以及安装 vim：

    apt-get update
    apt-get upgrade
    apt-get install vim

## 创建新的用户代替 root

root = 最高权限，即使是自己使用 root，也会导致误删重要的系统文件等问题，所以要第一时间改为使用权限较低的用户，一些有关系统的配置使用 sudo 来完成。

创建新的用户以及 .ssh 文件夹：

    adduser deploy
    mkdir /home/deploy/.ssh
    chmod 700 /home/deploy/.ssh

## 通过公钥验证来代替密码验证

打开自己电脑的终端：

    ssh-keygen

这会在系统用户文件夹（这里就用 USER 代替）USER/.ssh 生成 id_rsa 和 id_rsa.pub 文件。

用编辑器打开 id_rsa.pub 并复制所有内容。

回到服务器终端：

    vim /home/deploy/.ssh/authorized_keys

将内容粘贴入 authorized_keys 这个文件。

更新权限：

    chmod 400 /home/deploy/.ssh/authorized_keys
    chown deploy:deploy /home/deploy -R

修改新用户的密码：

    passwd deploy

密码尽量和 root 密码不同；为了防止被彩虹表破解，务必要使用复杂的密码。

给新用户 sudo 的权限：

    visudo

增加一段：

    root    ALL=(ALL) ALL
    deploy  ALL=(ALL) ALL

既然已经可以通过公钥验证身份，以及新用户拥有了 sudo 权限，那么我们就可以让 root 这个高权限用户“休眠”了：

    vim /etc/ssh/sshd_config

修改为：

    PermitRootLogin no
    PasswordAuthentication no

重启 SSH：

    service ssh restart

## 配置防火墙

注意：防火墙的配置将会影响到未来的一些服务，例如 BOINC，shadowsocks，Minecraft 等，到时候需要修改防火墙配置使这些服务能够正常运行。

创建一个新的文件 iptables.firewall.rules：

    vim /etc/iptables.firewall.rules

将以下内容复制到 iptables.firewall.rules 中：

    *filter

    #  Allow all loopback (lo0) traffic and drop all traffic to 127/8 that doesn't use lo0
    -A INPUT -i lo -j ACCEPT
    -A INPUT -d 127.0.0.0/8 -j REJECT

    #  Accept all established inbound connections
    -A INPUT -m state --state ESTABLISHED,RELATED -j ACCEPT

    #  Allow all outbound traffic - you can modify this to only allow certain traffic
    -A OUTPUT -j ACCEPT

    #  Allow HTTP and HTTPS connections from anywhere (the normal ports for websites and SSL).
    -A INPUT -p tcp --dport 80 -j ACCEPT
    -A INPUT -p tcp --dport 443 -j ACCEPT

    #  Allow SSH connections
    #
    #  The -dport number should be the same port number you set in sshd_config
    #
    -A INPUT -p tcp -m state --state NEW --dport 22 -j ACCEPT

    #  Allow ping
    -A INPUT -p icmp -j ACCEPT

    #  Log iptables denied calls
    -A INPUT -m limit --limit 5/min -j LOG --log-prefix "iptables denied: " --log-level 7

    #  Drop all other inbound - default deny unless explicitly allowed policy
    -A INPUT -j DROP
    -A FORWARD -j DROP

    COMMIT

我自己添加的一些配置：（可以根据自己未来要添加的服务选择性参考）

    #  Allow Minecraft
    -A INPUT -p tcp --dport 25565 -j ACCEPT

    #  Allow shadowsocks
    -A INPUT -p tcp --dport 8388 -j ACCEPT

    #  Allow BOINC
    -A INPUT -p tcp --sport 31416 --dport 31416 -j ACCEPT

激活这个防火墙配置：

    iptables-restore < /etc/iptables.firewall.rules

为了确保重启后防火墙依然能自动启动，新建一个文件：

    vim /etc/network/if-pre-up.d/firewall

在文件中写入如下内容：

    #!/bin/sh
    /sbin/iptables-restore < /etc/iptables.firewall.rules

这样开机后就会自动激活防火墙配置。

## 安装与配置 Fail2ban

为了防止机器强力破解 root 密码，所以就要做到像 Web 网站一样多次输入密码错误 Ban IP 的做法：

    apt-get install fail2ban

编辑配置文件：

    vim /etc/fail2ban/jail.conf
    
可以自由修改一下数据：

    ignoreip = 127.0.0.1/8 （白名单）
    bantime = 600 （ban 的时间，单位是秒）
    maxretry = 3（许可的尝试次数）

退出 root:

    logout

至此，基础的安全保障就这样完成了。

## 参考

* [My First 5 Minutes On A Server; Or, Essential Security for Linux Servers](http://plusbryan.com/my-first-5-minutes-on-a-server-or-essential-security-for-linux-servers)
* [Securing Your Server – Linode Library](https://library.linode.com/securing-your-server)