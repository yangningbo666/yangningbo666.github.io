---
title: ssserver在vps服务器端自启动
categories: tools
tags: 
    - ssserver
    - shadowsocks
    - nohup
    - chacha20
comments: true
keywords: ssserver, shadowsocks, nohup, chacha20
description: ssserver在VPS服务器端自启动
date: 2018-01-16 19:41:17
---
（转载请注明作者和出处：https://yangningbocn.github.io 未经允许请勿用于商业用途）
系统环境:Ubuntu
操作要求：能够进行基本的linux操作


----------

说明：
nohup可以让你关闭终端之后，进程依旧在后台运行，如果用&，终端关闭之后进程也随之关闭。就是这样。

##命令：

```
1.安装pip
 apt-get install python-pip
 
2.安装shadowsocks
 pip install shadowsocks

3. 添加配置文件
sudo vim /etc/shadowsocks.json
    内容如下：
    {
    "server":"服务器的ip",
    "server_port":19175,
    "local_address":"127.0.0.1",
    "local_port":1080,
    "password":"密码",
    "timeout":300,
    "method":"aes-256-cfb",
    "fast_open":false
    }
    ：wq来保存退出

4.写脚本文件
sudo vim /root/shadowsocks.sh
    内容如下：
    #！/bin/bash
    #shadowsocks.sh
    ssserver -c /etc/shadowsocks.json
5.开机自启动
sudo vim /etc/rc.local
    在exit之前输入
    nohup bash /home/shadow.sh&
    保存退出
6.reboot

```


----------

加密方式改为"chacha20-ietf"
优点：据说加密更牛逼，网速也牛逼。然后并没有发现哪里牛逼了，感觉也就手机快一点点吧，这可能就是玄学吧。不管怎么先把过程写出来吧。
shadowsocks版本不能太低，2.x.x的版本没有chacha20、chacha20-ietf等加密方式，所以要重新装，也要装chacha20的依赖库libsodium。

这个时候要

```
1.升级pip
pip install --upgrade pip

2.升级安装工具
pip install -U setuptools

3.安装新的shadowsocks
pip install git+https://github.com/shadowsocks/shadowsocks.git@master

4.安装libsodium
    apt-get install build-essential
    wget https://download.libsodium.org/libsodium/releases/LATEST.tar.gz
    tar zxf LATEST.tar.gz
    cd libsodium*
    ./configure
    make && make install
    ldconfig

5.修改配置文件
sudo vim /etc/shadowsocks.json
    把加密方式改为：chacha20-ietf

5.
reboot
```