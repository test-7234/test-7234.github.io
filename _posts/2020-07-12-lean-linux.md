---
layout: post
title: Linux笔记之管理硬盘
categories: Linux
description: 在路由器上简单的管理硬盘
keywords: Linux
---
>这事源于一个薅羊毛来的sd卡，因为原来的主路由上是带sd卡的，这玩意薅回来也算是有用，但是主路由换了以后就没啥卵用了，早段时间闲的蛋疼就买了个读卡器回来换个路由接着用，从博通又回到了MTK的怀抱，但是经常性会出现挂载不上的问题，才写的这篇闲着蛋疼的教程

----------

理论上教程支持梅林、老毛子、mtk 等各类基于 Linux 系统，都是大同小异的

----------


因为种种原因，电脑不是随时能用到，手机上能用各类终端或者我自己用的是 JuiceSSH win上面可以用 Putty 教程上出现的截图均为 Putty 的

----------

# 1. 安装opkg

安装前要打开jffs、ssh
用过 openwrt 的肯定熟悉，但是梅林上因为有自带的软件中心，应该很少会在梅林上用这个，在梅林上一般都有都要自行安装opkg，可以尝试`opkg update`
![image.png](http://ww1.sinaimg.cn/large/006H9iLngy1ggog3peramj30ir0bt3yv.jpg)要是显示这样的就是已经安装了就可以直接跳过了，要是不是这样就接着看吧

先创建安装目录

    mkdir /jffs/opt
    ln -nsf /jffs/opt /tmp/opt

----------


安装opkg

    wget http://qnapware.zyxmon.org/binaries-armv7/installer/entware_install_arm.sh
    sh ./entware_install_arm.sh


之后就会自动安装一堆软件，一般来说用不了多久就可以搞定了，要是卡住的话可能要考虑科学上网或者重新执行试试看
![image.png](http://ww1.sinaimg.cn/large/006H9iLngy1ggogf3dvh8j30ir0btt9n.jpg)

![image.png](http://ww1.sinaimg.cn/large/006H9iLngy1ggog3peramj30ir0bt3yv.jpg)
按照步骤安装完了就是这样的，可以尝试`opkg update`看看有没有效果

到这里还差一步自动挂载就完成安装了

    cd /jffs/scripts
    vi post-mount

然后就对着下面的抄进去

    !/bin/sh
    ln -nsf /jffs/opt /tmp/opt

最后还要给权限

    chmod a+rx /jffs/scripts/post-mount

![image.png](http://ww1.sinaimg.cn/large/006H9iLngy1ggogx0npl4j30ir0btwfj.jpg)

对着就算是安装完了opkg

----------

 

# 2. 挂载

经常性出现的问题是插上去了无法识别，要是识别都无法识别在这里也大概率得不到解决，这里要先安装 fdisk
>fdisk是Linux上常用的硬盘管理工具,功能也很多，但是这里就不深入探索了

    opkg install fdisk

![image.png](http://ww1.sinaimg.cn/large/006H9iLngy1ggoh2jmwb3j30ir0btq3r.jpg)

安装完成就输入

    fdisk -l

![](leanote://file/getImage?fileId=5f0b0628ca7dfe3d2b000000)
 这个命令是用来查看分区的
 这里可以看到我的sd卡是32g的，容量转换啥的就不说了
 
 
 >到这里也可以说其实不能自动挂载上的很多时候都是U盘啥的自己的兼容问题，通常把这U盘插到电脑上是可以识别但是会提示修复啥啥啥的，单市场修复也没有啥用。挂载不上就只能死马当活马医了，先尝试自己手动挂载，挂载不上就只能格式化了，这也是没有办法的办法。
 
 

----------


 
 先尝试输入
 
    umount /dev/

 卸载已经挂载的U盘，要是提示

> No such file or directory

之类就是你原来就没挂载上，那问题也不大

接下来就是挂载了，先创建挂载目录

    mkdir /mnt/sda1
    
然后就是挂载到这个目录

    mount /dev/sda1 /mnt/sda1
![image.png](http://ww1.sinaimg.cn/large/006H9iLngy1ggoi8jahwfj30ir0btt9p.jpg)
    要是挂载上的话就可以用`cd  /mnt/sda1`看到U盘里面的东西，要是挂载不上显示啥`input/output error`的好像就只能格式化没其他啥法子了
    

# 3. 格式化
要是死活都挂载不上的还话就只能格式化了，格式化也是一句命令的事，这里也多嘴说一句，Linux是默认用户知道自己是在干什么的有时候一些命令可能会造成比较严重的后果，但是Linux是不会提示出高危行为啥啥啥的，可能事后会有补救的方式，但毕竟都是本该可以避免的事
**格式化**

     mkfs.ext4 /dev/sda1
    
![image.png](http://ww1.sinaimg.cn/large/006H9iLngy1ggoiqb9ovhj30ir0btgmn.jpg)
然后`Y`就完事了，之后就是等待格式化完成了，这里我就不截成功的图了
