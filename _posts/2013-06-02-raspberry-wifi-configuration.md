---
title:  使用 USB 无线网卡让树莓派连接 WiFi
date:   2013-06-02 18:29:11  +0800
---

在没有 GUI 的情况下，让树莓派连接 WiFi 是一件比较麻烦的事情，通过查询一些资料以及动手实践，终于将自己的树莓派连接到了家里的 WiFi，并记录下了这一过程。

## 准备

1. 一个已经配置好系统并且可以控制的树莓派
2. USB 无线网卡（WLAN Adapter）
3. 一个 DHCP 路由器
3. 你处在需要连接的 WiFi 范围内（废话咯）

## 先更新。。

如果是第一次更新，时间可能会比较漫长：

    sudo apt-get update
    sudo apt-get upgrade

## 插入 USB 无线网卡

更新完后，就可以插入 USB 无线网卡了。不过更建议关机后再插 USB 无线网卡再开机，因为自己有过直接插 USB 无线网卡造成死机的蛋疼经历。

监测是否被树莓派响应：

    lsusb

举个例子，我的 USB 无线网卡：

    Bus 001 Device 004: ID 0bda:8176 Realtek Semiconductor Corp. RTL8188CUS 802.11n WLAN Adapter

正常情况下这个 USB 无线网卡应该就可以使用了，如果还不放心的话可以用 `lsmod`（查看 8192cu 是否在列表里）和 `iwconfig`（确认 wlan0 是否在线）

## 配置无需密码验证的公用 WiFi

编辑 Interfaces：

    sudo vim /etc/network/interfaces

写入/修改为：

    auto lo
    
    iface lo inet loopback
    iface eth0 inet dhcp
    
    auto wlan0
    iface wlan0 inet dhcp
    wireless-essid YOUR_SSID
    wireless-mode managed # Generally is "managed"

## 配置需要密码验证的 WPA WiFi

编辑 Interfaces：

    sudo vim /etc/network/interfaces

写入/修改为：

    auto lo
 
    iface lo inet loopback
    iface eth0 inet dhcp
 
    allow-hotplug wlan0
    auto wlan0
    iface wlan0 inet dhcp
    wpa-roam /etc/wpa_supplicant/wpa_supplicant.conf

配置 WPA：

    sudo vim /etc/wpa_supplicant/wpa_supplicant.conf

写入/修改为：

    network={
        ssid=YOUR_SSID
        proto=RSN
        key_mgmt=WPA-PSK
        pairwise=CCMP TKIP
        group=CCMP TKIP
        psk=YOUR_WIFI_PASSWORD
    }

## 重启系统

重启系统，拔掉网线，就可以在 192.168.1.1 里看见一个叫 raspberrypi 的设备连接上了这个无线网络。

需要注意的是，连接 WiFi 网络时，FDX，LNK，100 这 3 个灯是不会亮的。

## 参考

* [How To: WiFi on your Raspberry PI](http://pingbin.com/2012/12/setup-wifi-raspberry-pi/)
* [Raspberry Pi - Installing the Edimax EW-7811Un USB WiFi Adapter (WiFiPi)](http://www.savagehomeautomation.com/projects/raspberry-pi-installing-the-edimax-ew-7811un-usb-wifi-adapte.html)
