---
title: linux 基本操作命令
date: 2018-10-10 11:16:43
tags: [linux]
categories: linux
---
Linux ,免费开源，多用户多任务系统。基于Linux有多个版本的衍生。RedHat、Ubuntu、Debian 安装VMware 或 VirtualBox 虚拟机。

### 常用指令

> ls 显示文件或目录
> mkdir 创建目录
> cd   切换目录
> touch 创建空文件 
> echo  创建带有内容的文件
> cat   查看文件内容 
> cp 拷贝
> mv  移动或重命令 
> rm  删除文件  
    -r 递归删除，可删除子目录及文件
    -f         强制删除
> find              在文件系统中搜索某文件
> rmdir           删除空目录
> pwd              显示当前目录
> scp -r dist/* root@域名:/目录  //上传本地文件至lunux服务器

<!-- more -->

### vim使用

vim三种模式：命令模式、插入模式、编辑模式。使用ESC或i或：来切换模式。

命令模式下：

:q                      退出

:q!                     强制退出

:wq                   保存并退出




### 文件权限管理

三种基本权限

R           读         数值表示为4

W          写         数值表示为2

X          可执行     数值表示为1

### 系统管理命令

> stat              显示指定文件的详细信息，比ls更详细

> who               显示在线登陆用户

> whoami            显示当前操作用户

> hostname          显示主机名

> uname             显示系统信息

> du                查看目录大小 du -h /home带有单位显示目录信息

> df                查看磁盘大小 df -h 带有单位显示磁盘信息

> ifconfig          查看网络情况

> ping              测试网络连通

> netstat          显示网络状态信息

> clear            清屏

> alias             对命令重命名 如：alias showmeit="ps -aux" ，另外解除使用unaliax showmeit

> top               动态显示当前耗费资源最多进程信息

> ps                显示瞬间进程状态 ps -aux

> kill              杀死进程，可以先用ps 或 top命令查看进程的id，然后再用kill命令杀死进程。                                                                                                                                                                                                                                                                                                                                                                                             
### 压缩解压缩命令   

liunx服务器上默认没有安装zip命令，所以使用时需安装：
> apt-get install zip 或  yum install zip

linux安装unzip命令：
> apt-get install unzip 或  yum install unzip

压缩  服务器上当前目录为xxx.zip文件

> zip -r xxx.zip 

解压  服务器上当前目录为xxx.zip文件

> unzip -r xxx.zip 



压缩

tar -cvf jpg.tar *.jpg //将目录里所有jpg文件打包成jpg.tar 

tar -czf jpg.tar.gz *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz

tar -cjf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2

tar -cZf jpg.tar.Z *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z



解压

tar -xvf file.tar //解压 tar包

tar -xzvf file.tar.gz //解压tar.gz

tar -xjvf file.tar.bz2   //解压 tar.bz2

tar -xZvf file.tar.Z   //解压tar.Z

tar -cvf jpg.tar *.jpg //将目录里所有jpg文件打包成jpg.tar 

tar -czf jpg.tar.gz *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz

 tar -cjf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2

tar -cZf jpg.tar.Z *.jpg   //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z



### 上传本地文件命令

登录 linux系统
```
ssh root@yaochenyang.xin
```

上传本地文件
```
scp -r 本地文件/* root@域名:/目录

```


