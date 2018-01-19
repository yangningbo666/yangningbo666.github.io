---
title: 移动机器人中SW2URDF
categories: ros
tags: 
  - robot
  - sw2urdf
  - ros
commetents: true
keywords: ros, urdf, solidworks, sw2urdf
description: 建立urdf模型，并转化为xacro格式，用在gazebo和rviz中。
date: 2017-10-09 09:46:33
---

本博客只是个人学习记录使用，第一次写，瞎写写，内容比较粗糙。编写过程中，借鉴了古月居大神的smartcar以及其它一些大神的内容。 

硕士课题中的移动机器人的SolidWorks三维模型转换为ros中的urdf文件的步骤如下：

* * *

准备工作： 

    1\. 安装SolidWorks，亲测2014和2016都可以，别的应该也行。 

         可从ROS-WIKI上下载，下载地址为[sw2urdf](https://bitbucket.org/brawner/sw2urdf/raw/cde91b4f1a2f2c768da55471fbec04cb34edd554/INSTALL/Output/sw2urdfSetup.exe) 

     2\. 在SW2016中，在工具 - file - export as URDF

* * *

开始工作：

1.  打开模型，并执行export as URDF，稍等片刻可以看到一个选项卡
![初始状态](http://img.blog.csdn.net/20171009092034590?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
2.  在选项卡中，你会看到link name 和 Global Origin Coordinate System，改不改都可以，我这里使用的是缺省值。Link Component 这里要选中你的小车的底盘部分（小tips：可以框框选中所有的零部件，然后在点击轮子就会反选了，一开始不知道费了很大劲。）Number of child links 这里根据你轮子的个数选择，我使用的是4，因为我有四个轮。设置完成之后如下图所示：
![设置完成](http://img.blog.csdn.net/20171009092128142?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
3.  接下来点击最下方生成出来的Empty_Link，接下来的界面如下：
![这里写图片描述](http://img.blog.csdn.net/20171009092346570?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
4.  这里只修改Link name、joint name和Link Components。Link name 和 joint name只修改了名字，Link Components选择了轮子，完成图如下
![这里写图片描述](http://img.blog.csdn.net/20171009092756321?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
5.  第4步的步骤重复3遍，就可以完成其它3个轮子了。
![这里写图片描述](http://img.blog.csdn.net/20171009092855462?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

6.  以上步骤都完成之后，如下图所示
![这里写图片描述](http://img.blog.csdn.net/20171009093015299?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

7.  然后点击 Preview and Export..稍等片刻之后，如下图所示
![这里写图片描述](http://img.blog.csdn.net/20171009093334301?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
![这里写图片描述](http://img.blog.csdn.net/20171009094000560?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQveW5iMTk5MzA0Mjg=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
在这里可以把Joint Type 选为continous，我也不知道具体有啥用，知道的可以给我说一下。Origin中z轴位置要稍微改一下，因为我的轮子直径是8cm，我填写的为0.04，这样在rviz中显示的时候他就在地面上了。相应的base_link的visual中的z值也要相应的进行调整。
8.  然后点击next，finish，选择好路径，还有名字我这里沿用的古月大神的smartcar_description，不过要把后缀去掉，因为这里生成的是文件夹。
9.  这样就算完成了，放到ros中把代码进行修改，并转化为xacro格式就可以launch起来了。

* * *

代码如下：

    <span class="hljs-pi">&lt;?xml version="1.0"?&gt;</span>  
    <span class="hljs-tag">&lt;<span class="hljs-title">robot</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"smartcar"</span> <span class="hljs-attribute">xmlns:xacro</span>=<span class="hljs-value">"http://ros.org/wiki/xacro"</span>&gt;</span>  
      <span class="hljs-tag">&lt;<span class="hljs-title">xacro:property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"M_PI"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"3.14159"</span>/&gt;</span>    
      <span class="hljs-comment">&lt;!-- Macro for SmartCar body. Including Gazebo extensions, but does not include Kinect --&gt;</span>  
      <span class="hljs-tag">&lt;<span class="hljs-title">xacro:include</span> <span class="hljs-attribute">filename</span>=<span class="hljs-value">"$(find smartcar_description)/urdf/gazebo.urdf.xacro"</span>/&gt;</span>  
      <span class="hljs-tag">&lt;<span class="hljs-title">xacro:property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"base_x"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"0.33"</span> /&gt;</span>  
      <span class="hljs-tag">&lt;<span class="hljs-title">xacro:property</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"base_y"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"0.33"</span> /&gt;</span>  
      <span class="hljs-tag">&lt;<span class="hljs-title">xacro:macro</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"smartcar_body"</span>&gt;</span>  
        <span class="hljs-tag">&lt;<span class="hljs-title">link</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"base_footprint"</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">inertial</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mass</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"0.0001"</span> /&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">origin</span> <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">inertia</span> <span class="hljs-attribute">ixx</span>=<span class="hljs-value">"0.0001"</span> <span class="hljs-attribute">ixy</span>=<span class="hljs-value">"0.0"</span> <span class="hljs-attribute">ixz</span>=<span class="hljs-value">"0.0"</span>
                <span class="hljs-attribute">iyy</span>=<span class="hljs-value">"0.0001"</span> <span class="hljs-attribute">iyz</span>=<span class="hljs-value">"0.0"</span> 
                <span class="hljs-attribute">izz</span>=<span class="hljs-value">"0.0001"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">inertial</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">visual</span>&gt;</span>
              <span class="hljs-tag">&lt;<span class="hljs-title">origin</span> <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span> <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
              <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
                  <span class="hljs-tag">&lt;<span class="hljs-title">box</span> <span class="hljs-attribute">size</span>=<span class="hljs-value">"0.001 0.001 0.001"</span> /&gt;</span>
              <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">visual</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">link</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">joint</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"base_footprint_joint"</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"fixed"</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin</span> <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span> <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">parent</span> <span class="hljs-attribute">link</span>=<span class="hljs-value">"base_footprint"</span>/&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">child</span> <span class="hljs-attribute">link</span>=<span class="hljs-value">"base_link"</span> /&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">joint</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">link
    </span>    <span class="hljs-attribute">name</span>=<span class="hljs-value">"base_link"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">inertial</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0.0032273 -0.060593 1.3595E-05"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">mass
    </span>        <span class="hljs-attribute">value</span>=<span class="hljs-value">"4.2623"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">inertia
    </span>        <span class="hljs-attribute">ixx</span>=<span class="hljs-value">"0.04568"</span>
            <span class="hljs-attribute">ixy</span>=<span class="hljs-value">"-0.00094545"</span>
            <span class="hljs-attribute">ixz</span>=<span class="hljs-value">"-8.9814E-06"</span>
            <span class="hljs-attribute">iyy</span>=<span class="hljs-value">"0.11402"</span>
            <span class="hljs-attribute">iyz</span>=<span class="hljs-value">"5.3399E-06"</span>
            <span class="hljs-attribute">izz</span>=<span class="hljs-value">"0.11035"</span> /&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">inertial</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">visual</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0.23"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mesh
    </span>          <span class="hljs-attribute">filename</span>=<span class="hljs-value">"package://smartcar_description/meshes/base_link.STL"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">material
    </span>        <span class="hljs-attribute">name</span>=<span class="hljs-value">""</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">color
    </span>          <span class="hljs-attribute">rgba</span>=<span class="hljs-value">"1 1 1 1"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">visual</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">collision</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0.23"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mesh
    </span>          <span class="hljs-attribute">filename</span>=<span class="hljs-value">"package://smartcar_description/meshes/base_link.STL"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">collision</span>&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">link</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">link
    </span>    <span class="hljs-attribute">name</span>=<span class="hljs-value">"left_front_wheel"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">inertial</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 -1.98217916108356E-05 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">mass
    </span>        <span class="hljs-attribute">value</span>=<span class="hljs-value">"0.176438810476987"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">inertia
    </span>        <span class="hljs-attribute">ixx</span>=<span class="hljs-value">"8.8965933887671E-05"</span>
            <span class="hljs-attribute">ixy</span>=<span class="hljs-value">"-1.27636183461175E-23"</span>
            <span class="hljs-attribute">ixz</span>=<span class="hljs-value">"8.0831821149659E-23"</span>
            <span class="hljs-attribute">iyy</span>=<span class="hljs-value">"8.89898462049031E-05"</span>
            <span class="hljs-attribute">iyz</span>=<span class="hljs-value">"2.16519665002917E-23"</span>
            <span class="hljs-attribute">izz</span>=<span class="hljs-value">"0.000130905430632044"</span> /&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">inertial</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">visual</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mesh
    </span>          <span class="hljs-attribute">filename</span>=<span class="hljs-value">"package://smartcar_description/meshes/left_front_wheel.STL"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">material
    </span>        <span class="hljs-attribute">name</span>=<span class="hljs-value">""</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">color
    </span>          <span class="hljs-attribute">rgba</span>=<span class="hljs-value">"1 1 0 1"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">visual</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">collision</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mesh
    </span>          <span class="hljs-attribute">filename</span>=<span class="hljs-value">"package://smartcar_description/meshes/left_front_wheel.STL"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">collision</span>&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">link</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">joint
    </span>    <span class="hljs-attribute">name</span>=<span class="hljs-value">"left_front_wheel_joint"</span>
        <span class="hljs-attribute">type</span>=<span class="hljs-value">"continuous"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>      <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0.12875 0.1504 0.04"</span>
          <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"1.5708 0 0"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">parent
    </span>      <span class="hljs-attribute">link</span>=<span class="hljs-value">"base_link"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">child
    </span>      <span class="hljs-attribute">link</span>=<span class="hljs-value">"left_front_wheel"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">axis
    </span>      <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 1"</span> /&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">joint</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">link
    </span>    <span class="hljs-attribute">name</span>=<span class="hljs-value">"right_front_wheel"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">inertial</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 -1.98217916108079E-05 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">mass
    </span>        <span class="hljs-attribute">value</span>=<span class="hljs-value">"0.176438810476987"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">inertia
    </span>        <span class="hljs-attribute">ixx</span>=<span class="hljs-value">"8.8965933887671E-05"</span>
            <span class="hljs-attribute">ixy</span>=<span class="hljs-value">"-1.27636183461175E-23"</span>
            <span class="hljs-attribute">ixz</span>=<span class="hljs-value">"8.08318211496588E-23"</span>
            <span class="hljs-attribute">iyy</span>=<span class="hljs-value">"8.89898462049032E-05"</span>
            <span class="hljs-attribute">iyz</span>=<span class="hljs-value">"2.18596653030412E-23"</span>
            <span class="hljs-attribute">izz</span>=<span class="hljs-value">"0.000130905430632044"</span> /&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">inertial</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">visual</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mesh
    </span>          <span class="hljs-attribute">filename</span>=<span class="hljs-value">"package://smartcar_description/meshes/right_front_wheel.STL"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">material
    </span>        <span class="hljs-attribute">name</span>=<span class="hljs-value">""</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">color
    </span>          <span class="hljs-attribute">rgba</span>=<span class="hljs-value">"1 1 0 1"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">visual</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">collision</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mesh
    </span>          <span class="hljs-attribute">filename</span>=<span class="hljs-value">"package://smartcar_description/meshes/right_front_wheel.STL"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">collision</span>&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">link</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">joint
    </span>    <span class="hljs-attribute">name</span>=<span class="hljs-value">"right_front_wheel_joint"</span>
        <span class="hljs-attribute">type</span>=<span class="hljs-value">"continuous"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>      <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0.12875 -0.1504 0.04"</span>
          <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"1.5708 0 0"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">parent
    </span>      <span class="hljs-attribute">link</span>=<span class="hljs-value">"base_link"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">child
    </span>      <span class="hljs-attribute">link</span>=<span class="hljs-value">"right_front_wheel"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">axis
    </span>      <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 1"</span> /&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">joint</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">link
    </span>    <span class="hljs-attribute">name</span>=<span class="hljs-value">"left_back_wheel"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">inertial</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 -1.98217916108356E-05 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">mass
    </span>        <span class="hljs-attribute">value</span>=<span class="hljs-value">"0.176438810476987"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">inertia
    </span>        <span class="hljs-attribute">ixx</span>=<span class="hljs-value">"8.8965933887671E-05"</span>
            <span class="hljs-attribute">ixy</span>=<span class="hljs-value">"-1.27300145019925E-23"</span>
            <span class="hljs-attribute">ixz</span>=<span class="hljs-value">"8.0831821149659E-23"</span>
            <span class="hljs-attribute">iyy</span>=<span class="hljs-value">"8.89898462049032E-05"</span>
            <span class="hljs-attribute">iyz</span>=<span class="hljs-value">"2.16519665002917E-23"</span>
            <span class="hljs-attribute">izz</span>=<span class="hljs-value">"0.000130905430632044"</span> /&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">inertial</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">visual</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mesh
    </span>          <span class="hljs-attribute">filename</span>=<span class="hljs-value">"package://smartcar_description/meshes/left_back_wheel.STL"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">material
    </span>        <span class="hljs-attribute">name</span>=<span class="hljs-value">""</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">color
    </span>          <span class="hljs-attribute">rgba</span>=<span class="hljs-value">"1 1 0 1"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">visual</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">collision</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mesh
    </span>          <span class="hljs-attribute">filename</span>=<span class="hljs-value">"package://smartcar_description/meshes/left_back_wheel.STL"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">collision</span>&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">link</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">joint
    </span>    <span class="hljs-attribute">name</span>=<span class="hljs-value">"left_back_wheel_joint"</span>
        <span class="hljs-attribute">type</span>=<span class="hljs-value">"continuous"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>      <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"-0.12875 0.1504 0.04"</span>
          <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"1.5708 0 0"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">parent
    </span>      <span class="hljs-attribute">link</span>=<span class="hljs-value">"base_link"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">child
    </span>      <span class="hljs-attribute">link</span>=<span class="hljs-value">"left_back_wheel"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">axis
    </span>      <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 1"</span> /&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">joint</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">link
    </span>    <span class="hljs-attribute">name</span>=<span class="hljs-value">"right_back_wheel"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">inertial</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"2.77555756156289E-17 -1.98217916108079E-05 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">mass
    </span>        <span class="hljs-attribute">value</span>=<span class="hljs-value">"0.176438810476987"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">inertia
    </span>        <span class="hljs-attribute">ixx</span>=<span class="hljs-value">"8.8965933887671E-05"</span>
            <span class="hljs-attribute">ixy</span>=<span class="hljs-value">"-4.17259369791981E-24"</span>
            <span class="hljs-attribute">ixz</span>=<span class="hljs-value">"2.75455648073554E-21"</span>
            <span class="hljs-attribute">iyy</span>=<span class="hljs-value">"8.89898462049032E-05"</span>
            <span class="hljs-attribute">iyz</span>=<span class="hljs-value">"5.21854052435048E-20"</span>
            <span class="hljs-attribute">izz</span>=<span class="hljs-value">"0.000130905430632044"</span> /&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">inertial</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">visual</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mesh
    </span>          <span class="hljs-attribute">filename</span>=<span class="hljs-value">"package://smartcar_description/meshes/right_back_wheel.STL"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">material
    </span>        <span class="hljs-attribute">name</span>=<span class="hljs-value">""</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">color
    </span>          <span class="hljs-attribute">rgba</span>=<span class="hljs-value">"1 1 0 1"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">visual</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">collision</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>        <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 0"</span>
            <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"0 0 0"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">geometry</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mesh
    </span>          <span class="hljs-attribute">filename</span>=<span class="hljs-value">"package://smartcar_description/meshes/right_back_wheel.STL"</span> /&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">geometry</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">collision</span>&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">link</span>&gt;</span>
      <span class="hljs-tag">&lt;<span class="hljs-title">joint
    </span>    <span class="hljs-attribute">name</span>=<span class="hljs-value">"right_back_wheel_joint"</span>
        <span class="hljs-attribute">type</span>=<span class="hljs-value">"continuous"</span>&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">origin
    </span>      <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"-0.12875 -0.1504 0.04"</span>
          <span class="hljs-attribute">rpy</span>=<span class="hljs-value">"1.5708 0 3.1416"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">parent
    </span>      <span class="hljs-attribute">link</span>=<span class="hljs-value">"base_link"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">child
    </span>      <span class="hljs-attribute">link</span>=<span class="hljs-value">"right_back_wheel"</span> /&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">axis
    </span>      <span class="hljs-attribute">xyz</span>=<span class="hljs-value">"0 0 1"</span> /&gt;</span>
      <span class="hljs-tag">&lt;/<span class="hljs-title">joint</span>&gt;</span>

      <span class="hljs-tag">&lt;/<span class="hljs-title">xacro:macro</span>&gt;</span>  

    <span class="hljs-tag">&lt;/<span class="hljs-title">robot</span>&gt;</span>
<div>作者：ynb19930428发表于2017/10/9 9:46:33[ 原文链接 ](http://blog.csdn.net/ynb19930428/article/details/78179545)</div><div> 阅读：150评论：2[查看评论 ](http://blog.csdn.net/ynb19930428/article/details/78179545#commentstarget =) </div>