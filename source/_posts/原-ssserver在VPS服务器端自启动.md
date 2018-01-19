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

系统环境:Ubuntu 

操作要求：能够进行基本的linux操作

* * *

说明： 

nohup可以让你关闭终端之后，进程依旧在后台运行，如果用&amp;，终端关闭之后进程也随之关闭。就是这样。

## 命令：

    <span class="hljs-number">1</span>.安装pip
     apt-get install python-pip

    <span class="hljs-number">2</span>.安装shadowsocks
     pip install shadowsocks

    <span class="hljs-number">3</span>. 添加配置文件
    <span class="hljs-built_in">sudo</span> vim /etc/shadowsocks.json
        内容如下：
        {
        <span class="hljs-string">"server"</span>:<span class="hljs-string">"服务器的ip"</span>,
        <span class="hljs-string">"server_port"</span>:<span class="hljs-number">19175</span>,
        <span class="hljs-string">"local_address"</span>:<span class="hljs-string">"127.0.0.1"</span>,
        <span class="hljs-string">"local_port"</span>:<span class="hljs-number">1080</span>,
        <span class="hljs-string">"password"</span>:<span class="hljs-string">"密码"</span>,
        <span class="hljs-string">"timeout"</span>:<span class="hljs-number">300</span>,
        <span class="hljs-string">"method"</span>:<span class="hljs-string">"aes-256-cfb"</span>,
        <span class="hljs-string">"fast_open"</span>:<span class="hljs-literal">false</span>
        }
        ：wq来保存退出

    <span class="hljs-number">4</span>.写脚本文件
    <span class="hljs-built_in">sudo</span> vim /root/shadowsocks.sh
        内容如下：
        <span class="hljs-comment">#！/bin/bash</span>
        <span class="hljs-comment">#shadowsocks.sh</span>
        ssserver -c /etc/shadowsocks.json
    <span class="hljs-number">5</span>.开机自启动
    <span class="hljs-built_in">sudo</span> vim /etc/rc.local
        在<span class="hljs-keyword">exit</span>之前输入
        nohup bash /home/shadow.sh&amp;
        保存退出
    <span class="hljs-number">6</span>.reboot
    `</pre>

    * * *

    加密方式改为”chacha20-ietf” 

    优点：据说加密更牛逼，网速也牛逼。然后并没有发现哪里牛逼了，感觉也就手机快一点点吧，这可能就是玄学吧。不管怎么先把过程写出来吧。 

    shadowsocks版本不能太低，2.x.x的版本没有chacha20、chacha20-ietf等加密方式，所以要重新装，也要装chacha20的依赖库libsodium。

    这个时候要

    <pre class="prettyprint">`<span class="hljs-number">1.</span>升级pip
    pip install --upgrade pip

    <span class="hljs-number">2.</span>升级安装工具
    pip install -U setuptools

    <span class="hljs-number">3.</span>安装新的shadowsocks
    pip install git+https://github<span class="hljs-preprocessor">.com</span>/shadowsocks/shadowsocks<span class="hljs-preprocessor">.git</span>@master

    <span class="hljs-number">4.</span>安装libsodium
        apt-get install build-essential
        wget https://download<span class="hljs-preprocessor">.libsodium</span><span class="hljs-preprocessor">.org</span>/libsodium/releases/LATEST<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
        tar zxf LATEST<span class="hljs-preprocessor">.tar</span><span class="hljs-preprocessor">.gz</span>
        cd libsodium*
        ./configure
        make &amp;&amp; make install
        ldconfig

    <span class="hljs-number">5.</span>修改配置文件
    sudo vim /etc/shadowsocks<span class="hljs-preprocessor">.json</span>
        把加密方式改为：chacha20-ietf

    <span class="hljs-number">5.</span>
    reboot
<div>作者：ynb19930428发表于2018/1/16 19:41:17[ 原文链接 ](http://blog.csdn.net/ynb19930428/article/details/79078362)</div><div> 阅读：11评论：0[查看评论 ](http://blog.csdn.net/ynb19930428/article/details/79078362#commentstarget =) </div>