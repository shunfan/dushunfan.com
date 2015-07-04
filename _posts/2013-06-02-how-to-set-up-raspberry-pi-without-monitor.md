---
title:  如何使用 SSH 来配置树莓派
date:   2013-06-02 16:18:49  +0800
---

Raspberry 到货后有点茫然。一是因为自己没有买视频线，再说没有可用的显示屏，二是因为暂时还不知道有什么其他办法来控制树莓派。今天觉得用路由器的 DHCP + SSH 是个可行的办法，所以摸索了起来。

## 找到 Raspberry Pi 的 IP 地址

将树莓派通过网线连接路由器 LAN 端口，然后用浏览器打开 192.168.1.1，就会出现路由器后台。通常路由器都有 DHCP 功能，在 DHCP 客户端列表上找到 raspberrypi 的 IP 地址，然后用 SSH 连接。

    ssh pi@123.456.7.999

初始密码就是 raspberry

## 配置 Raspberry Pi：

进入系统后开始配置树莓派：

    sudo raspi-config

我自己是改了下时区，由于暂时不需要 GUI 的支持，所以把 Memory split 调到了 16 MB，并且 disable 了 Boot to desktop，顺便改了下密码。

速速更新：

    sudo apt-get update
    sudo apt-get upgrade

## 参考

* [How To Set Up Raspberry Pi Without Monitor (DHCP, ya you know me!)](http://n00blab.com/how-to-set-up-raspberry-pi-without-monitor/)
