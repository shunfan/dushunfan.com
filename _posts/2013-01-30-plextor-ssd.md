---
title:  为 2011 年末的 MacBook Pro 更换 SSD
date:   2013-01-30 19:58:31  +0800
---

上星期去香港 SAT2 考试，带回了一个 128 GB 的 M5P。昨天把它装上了自己的 MacBook Pro，因为是 Reinstall System，程序只有自己常用的几个，所以速度的提升没有完全感觉到，但是至少开机是没出现过菊花。早上特地写一篇日志来全程描述一下自己换 SSD 的经历。

1. 准备 Mountain Lion 的系统 U 盘

    首先参考：[如何制作 Mountain Lion 全新安装 U 盘？](http://www.guomii.com/posts/29782)

    小提示：最后拖拽到 source 的不是原 dmg ，而是打开 dmg 之后生成的非 dmg 文件。

2. 装备硬件

    准备十字和 T6 就行，淘宝上都有麦哲伦精工生产的十字和 T6 卖。

    拆卸过程详见：[Installing MacBook Pro 13" Unibody Late 2011 Hard Drive](http://www.ifixit.com/Guide/Installing+MacBook+Pro+13-Inch+Unibody+Late+2011+Hard+Drive/7656/1)

3. 用U盘安装系统

    开机后，当时不确定是按 option 还是 command + R 就进去了，不过我觉得 OS X 应该能智能识别 U 盘的存在。

    先打开 Disk Utility 将 SSD 分区，和之前 U 盘的分区一样的程序，不过记得分区后是自动格式化的，如果你已经将系统安装在了 SSD 里面，就不用这一步。

    然后就可以安装系统了。

4. 打开 Trim

    下载地址：[Trim Enabler](http://www.groths.org/?page_id=322)
