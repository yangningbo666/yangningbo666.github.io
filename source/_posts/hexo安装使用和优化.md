---
title: hexo安装使用和优化
categories: tools
tags: [Hexo, Blogs, Github]
comments: true
keywords: Hexo, Blog, GitHub
description: 使用Hexo在GitHub上搭建个人博客
date: 2017-01-010 13:00:00
---

本文主要使用GitHub pages + hexo 来搭建个人博客，作日常学习使用。在这里只提主要步骤，不提具体过程，需要的话自行谷歌。

---
安装与配置

# 安装
## 1.git
## 2.Node.js
    进入官网[官网链接](https://nodejs.org/en/)
    下载安装包，直接点击安装就可以了 
## 3.hexo
这一步在命令行下做

    npm install -g hexo-cli
    cd *your direction*
    hexo init
    npm install
    hexo server
    - Hexo
    - Blogs
    - Github
打开浏览器输入 http://0.0.0.0:4000/ 出现hexo说明成功
接下来是修改主题，你也可以去下别的主题很多，自行谷歌

    cd themes
    git clone https://github.com/litten/hexo-theme-next.git
然后打开Hexo文件夹下面的_config.yml文件，修改里面的theme为next
重新打开server，浏览器输入http://0.0.0.0:4000/
查看主题。
# GitHub配置
## 1.仓库
创建一个仓库名称为yangningbocn.github.io
这里要把yangningbocn换成你自己的GitHub用户名
## 2.配置SSH

# 配置hexo
## 1.配置hexo目录下的_config.yml文件
    # Deployment
    ## Docs: http://hexo.io/docs/deployment.html
    deploy:
      type: git
      repository: https://github.com:yangningbocn/yangningbocn.github.io.git
      branch: master
    name: Ningbo Yang
    emai: yangningbocn@gmail.com
## 2.测试
cd到hexo的根目录下

    hexo clean
    hexo g
    hexo d

---

优化
# git安装
    npm install hexo-deployer-git --save

# 迁移
支持很多种迁移方式，我采用的是RSS的，其他可以看官网
[hexo迁移官网](https://hexo.io/zh-cn/docs/migration.html)
    
    npm install hexo-migrator-rss --save
    hexo migrate rss <source>

# 图片插入
    npm install https://github.com/CodeFalling/hexo-asset-image --save
然后把图片放入对应文章的配套文件夹下，比如1.png








