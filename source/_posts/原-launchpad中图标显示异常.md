---
title: launchpad中图标显示异常
categories: tools 
tags:
    - launchpad
    - 图标异常
    - ubuntu
comments: true
keywords: luanchpad, 图标异常, icon error, Ubuntu
description: 在launchpad中图标异常，之后修复图标到正常情况
date: 2017-12-28 10:08:16
---

系统:　Ubuntu14.04

问题描述:Ubuntu的launchpad(开始菜单)中图标显示错误，不能正常显示图标。

解决方案：

打开终端

<pre name="code" class="plain">cd /usr/share/applications/</pre><pre name="code" class="plain">ls</pre>然后找到你的图标<pre name="code" class="plain">sudo vim *.desktop</pre><span style="white-space:pre">	</span>*为你的app的name

<pre name="code" class="plain">[Desktop Entry]
Name=Youdao Dict
Name[zh_CN]=有道词典
Name[zh_TW]=有道詞典
Exec=youdao-dict %f
<u><span style="color:#ff0000;">Icon</span></u>=/usr/share/youdao-dict/app/assets/youdao-dict.png
Terminal=false
X-MultipleArgs=false
Type=Application
Encoding=UTF-8
Categories=Application;Utility;Dictionary;
StartupNotify=false</pre>把Icon后面的路径改为你的图片的路径

<span style="white-space:pre">	</span><pre name="code" class="html">sudo reboot</pre>

<div>作者：ynb19930428发表于2017/12/28 10:08:16[ 原文链接 ](http://blog.csdn.net/ynb19930428/article/details/78918910)</div><div> 阅读：52评论：0[查看评论 ](http://blog.csdn.net/ynb19930428/article/details/78918910#commentstarget =) </div>