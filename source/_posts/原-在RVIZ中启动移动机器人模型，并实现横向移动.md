---
title: 在RVIZ中启动移动机器人模型，并实现横向移动
categories: ros
tags: 
  - ros
  - 横向移动
  - 机器人
  - rviz
comments: true
keywords: ROS, 横向移动, 机器人, rviz, gazebo
description: 在gazebo中启动移动机器人模型，并实现横向移动
date: 2017-10-09 15:59:54
---

本博客只是个人学习记录使用，瞎写写，内容比较粗糙。编写过程中，借鉴了古月居大神的smartcar和ROS-WIKI以及其它一些大神的内容。  

引入古月居大神的链接[使用smartcar进行仿真](http://www.guyuehome.com/248)和[smartcar源码上传](http://www.guyuehome.com/251)

## 下面我针对自己的机器人编写的代码和文件。

launch文件

    <span class="hljs-tag">&lt;<span class="hljs-title">launch</span>&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"/use_sim_time"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"false"</span> /&gt;</span>  

        <span class="hljs-comment">&lt;!-- Load the URDF/Xacro model of our robot --&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">arg</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"urdf_file"</span> <span class="hljs-attribute">default</span>=<span class="hljs-value">"$(find xacro)/xacro.py '$(find smartcar_description)/urdf/smartcar.urdf.xacro'"</span> /&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">arg</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"gui"</span> <span class="hljs-attribute">default</span>=<span class="hljs-value">"true"</span> /&gt;</span>  
        <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"robot_description"</span> <span class="hljs-attribute">command</span>=<span class="hljs-value">"$(arg urdf_file)"</span> /&gt;</span>  
        <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"use_gui"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"$(arg gui)"</span>/&gt;</span>  
        <span class="hljs-tag">&lt;<span class="hljs-title">node</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"arbotix"</span> <span class="hljs-attribute">pkg</span>=<span class="hljs-value">"arbotix_python"</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"arbotix_driver"</span> <span class="hljs-attribute">output</span>=<span class="hljs-value">"screen"</span>&gt;</span>  
            <span class="hljs-tag">&lt;<span class="hljs-title">rosparam</span> <span class="hljs-attribute">file</span>=<span class="hljs-value">"$(find smartcar_description)/config/smartcar_arbotix.yaml"</span> <span class="hljs-attribute">command</span>=<span class="hljs-value">"load"</span> /&gt;</span> 
            <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"sim"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"true"</span>/&gt;</span>  
        <span class="hljs-tag">&lt;/<span class="hljs-title">node</span>&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">node</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"joint_state_publisher"</span> <span class="hljs-attribute">pkg</span>=<span class="hljs-value">"joint_state_publisher"</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"joint_state_publisher"</span> &gt;</span>  

        <span class="hljs-tag">&lt;/<span class="hljs-title">node</span>&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">node</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"robot_state_publisher"</span> <span class="hljs-attribute">pkg</span>=<span class="hljs-value">"robot_state_publisher"</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"state_publisher"</span>&gt;</span>  

            <span class="hljs-tag">&lt;<span class="hljs-title">param</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"publish_frequency"</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"double"</span> <span class="hljs-attribute">value</span>=<span class="hljs-value">"20.0"</span> /&gt;</span>  

        <span class="hljs-tag">&lt;/<span class="hljs-title">node</span>&gt;</span>  

         <span class="hljs-comment">&lt;!-- We need a static transforms for the wheels --&gt;</span>  

         <span class="hljs-tag">&lt;<span class="hljs-title">node</span> <span class="hljs-attribute">pkg</span>=<span class="hljs-value">"tf"</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"static_transform_publisher"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"odom_left_wheel_broadcaster"</span> <span class="hljs-attribute">args</span>=<span class="hljs-value">"0 0 0 0 0 0 /base_link /left_front_link 100"</span> /&gt;</span>  

         <span class="hljs-tag">&lt;<span class="hljs-title">node</span> <span class="hljs-attribute">pkg</span>=<span class="hljs-value">"tf"</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"static_transform_publisher"</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"odom_right_wheel_broadcaster"</span> <span class="hljs-attribute">args</span>=<span class="hljs-value">"0 0 0 0 0 0 /base_link /right_front_link 100"</span> /&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">node</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"rviz"</span> <span class="hljs-attribute">pkg</span>=<span class="hljs-value">"rviz"</span> <span class="hljs-attribute">type</span>=<span class="hljs-value">"rviz"</span> <span class="hljs-attribute">args</span>=<span class="hljs-value">"-d $(find smartcar_description)/urdf.rviz"</span> /&gt;</span>  

    <span class="hljs-tag">&lt;/<span class="hljs-title">launch</span>&gt;</span>`</pre>

    * * *

    xacro文件包括三部分：

1.  smartcar_body

    <pre class="prettyprint">`<span class="hljs-pi">&lt;?xml version="1.0"?&gt;</span>  
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

    <span class="hljs-tag">&lt;/<span class="hljs-title">robot</span>&gt;</span>`</pre>

1.  gazebo.urdf.xacro

    <pre class="prettyprint">`<span class="hljs-pi">&lt;?xml version="1.0"?&gt;</span>  
    <span class="hljs-tag">&lt;<span class="hljs-title">robot</span> <span class="hljs-attribute">xmlns:controller</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#controller"</span>   

        <span class="hljs-attribute">xmlns:interface</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#interface"</span>   

        <span class="hljs-attribute">xmlns:sensor</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#sensor"</span>   

        <span class="hljs-attribute">xmlns:xacro</span>=<span class="hljs-value">"http://ros.org/wiki/xacro"</span>   

        <span class="hljs-attribute">name</span>=<span class="hljs-value">"smartcar_gazebo"</span>&gt;</span>  

     <span class="hljs-comment">&lt;!-- ASUS Xtion PRO camera for simulation --&gt;</span>  

    <span class="hljs-comment">&lt;!-- gazebo_ros_wge100 plugin is in kt2_gazebo_plugins package --&gt;</span>  

    <span class="hljs-tag">&lt;<span class="hljs-title">xacro:macro</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"smartcar_sim"</span>&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">gazebo</span> <span class="hljs-attribute">reference</span>=<span class="hljs-value">"base_footprint"</span>&gt;</span>
           <span class="hljs-tag">&lt;<span class="hljs-title">material</span>&gt;</span>Gazebo/Blue<span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>  
                 <span class="hljs-tag">&lt;<span class="hljs-title">turnGravityOff</span>&gt;</span>false<span class="hljs-tag">&lt;/<span class="hljs-title">turnGravityOff</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">gazebo</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">gazebo</span> <span class="hljs-attribute">reference</span>=<span class="hljs-value">"base_link"</span>&gt;</span>  

            <span class="hljs-tag">&lt;<span class="hljs-title">material</span>&gt;</span>Gazebo/Blue<span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>  

        <span class="hljs-tag">&lt;/<span class="hljs-title">gazebo</span>&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">gazebo</span> <span class="hljs-attribute">reference</span>=<span class="hljs-value">"right_front_wheel"</span>&gt;</span>  

            <span class="hljs-tag">&lt;<span class="hljs-title">material</span>&gt;</span>Gazebo/FlatBlack<span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>  

        <span class="hljs-tag">&lt;/<span class="hljs-title">gazebo</span>&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">transmission</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"right_front_wheel_joint_trans"</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">type</span>&gt;</span>transmission_interface/SimpleTransmission<span class="hljs-tag">&lt;/<span class="hljs-title">type</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">joint</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"right_front_wheel_joint"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">actuator</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"right_front_wheel_joint_motor"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">hardwareInterface</span>&gt;</span>EffortJointInterface<span class="hljs-tag">&lt;/<span class="hljs-title">hardwareInterface</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mechanicalReduction</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">mechanicalReduction</span>&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">actuator</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">transmission</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">gazebo</span> <span class="hljs-attribute">reference</span>=<span class="hljs-value">"right_back_wheel"</span>&gt;</span>  

            <span class="hljs-tag">&lt;<span class="hljs-title">material</span>&gt;</span>Gazebo/FlatBlack<span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>  

        <span class="hljs-tag">&lt;/<span class="hljs-title">gazebo</span>&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">transmission</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"right_back_wheel_joint_trans"</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">type</span>&gt;</span>transmission_interface/SimpleTransmission<span class="hljs-tag">&lt;/<span class="hljs-title">type</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">joint</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"right_back_wheel_joint"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">actuator</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"right_back_wheel_joint_motor"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">hardwareInterface</span>&gt;</span>EffortJointInterface<span class="hljs-tag">&lt;/<span class="hljs-title">hardwareInterface</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mechanicalReduction</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">mechanicalReduction</span>&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">actuator</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">transmission</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">gazebo</span> <span class="hljs-attribute">reference</span>=<span class="hljs-value">"left_front_wheel"</span>&gt;</span>  

            <span class="hljs-tag">&lt;<span class="hljs-title">material</span>&gt;</span>Gazebo/FlatBlack<span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>  

        <span class="hljs-tag">&lt;/<span class="hljs-title">gazebo</span>&gt;</span>  

        <span class="hljs-tag">&lt;<span class="hljs-title">transmission</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"left_front_wheel_joint_trans"</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">type</span>&gt;</span>transmission_interface/SimpleTransmission<span class="hljs-tag">&lt;/<span class="hljs-title">type</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">joint</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"left_front_wheel_joint"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">actuator</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"left_front_wheel_joint_motor"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">hardwareInterface</span>&gt;</span>EffortJointInterface<span class="hljs-tag">&lt;/<span class="hljs-title">hardwareInterface</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mechanicalReduction</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">mechanicalReduction</span>&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">actuator</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">transmission</span>&gt;</span>

        <span class="hljs-tag">&lt;<span class="hljs-title">gazebo</span> <span class="hljs-attribute">reference</span>=<span class="hljs-value">"left_back_wheel"</span>&gt;</span>  

            <span class="hljs-tag">&lt;<span class="hljs-title">material</span>&gt;</span>Gazebo/FlatBlack<span class="hljs-tag">&lt;/<span class="hljs-title">material</span>&gt;</span>  

        <span class="hljs-tag">&lt;/<span class="hljs-title">gazebo</span>&gt;</span> 

            <span class="hljs-tag">&lt;<span class="hljs-title">transmission</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"left_back_wheel_joint_trans"</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">type</span>&gt;</span>transmission_interface/SimpleTransmission<span class="hljs-tag">&lt;/<span class="hljs-title">type</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">joint</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"left_back_wheel_joint"</span> /&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">actuator</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"left_back_wheel_joint_motor"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">hardwareInterface</span>&gt;</span>EffortJointInterface<span class="hljs-tag">&lt;/<span class="hljs-title">hardwareInterface</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">mechanicalReduction</span>&gt;</span>1<span class="hljs-tag">&lt;/<span class="hljs-title">mechanicalReduction</span>&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">actuator</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">transmission</span>&gt;</span>

        <span class="hljs-comment">&lt;!-- position controller --&gt;</span>
        <span class="hljs-tag">&lt;<span class="hljs-title">gazebo</span>&gt;</span>
          <span class="hljs-tag">&lt;<span class="hljs-title">plugin</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"base_controller"</span> <span class="hljs-attribute">filename</span>=<span class="hljs-value">"libgazebo_ros_planar_move.so"</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">commandTopic</span>&gt;</span>cmd_vel<span class="hljs-tag">&lt;/<span class="hljs-title">commandTopic</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">odometryTopic</span>&gt;</span>odom<span class="hljs-tag">&lt;/<span class="hljs-title">odometryTopic</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">odometryFrame</span>&gt;</span>odom<span class="hljs-tag">&lt;/<span class="hljs-title">odometryFrame</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">odometryRate</span>&gt;</span>100.0<span class="hljs-tag">&lt;/<span class="hljs-title">odometryRate</span>&gt;</span>
            <span class="hljs-tag">&lt;<span class="hljs-title">robotBaseFrame</span>&gt;</span>base_footprint<span class="hljs-tag">&lt;/<span class="hljs-title">robotBaseFrame</span>&gt;</span>
          <span class="hljs-tag">&lt;/<span class="hljs-title">plugin</span>&gt;</span>
        <span class="hljs-tag">&lt;/<span class="hljs-title">gazebo</span>&gt;</span>

    <span class="hljs-tag">&lt;/<span class="hljs-title">xacro:macro</span>&gt;</span>  

    <span class="hljs-tag">&lt;/<span class="hljs-title">robot</span>&gt;</span>`</pre>

1.  smartcar.urdf.xacro

    <pre class="prettyprint">`<span class="hljs-pi">&lt;?xml version="1.0"?&gt;</span>  
    <span class="hljs-tag">&lt;<span class="hljs-title">robot</span> <span class="hljs-attribute">name</span>=<span class="hljs-value">"smartcar"</span>    

        <span class="hljs-attribute">xmlns:xi</span>=<span class="hljs-value">"http://www.w3.org/2001/XInclude"</span>  

        <span class="hljs-attribute">xmlns:gazebo</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#gz"</span>  

        <span class="hljs-attribute">xmlns:model</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#model"</span>  

        <span class="hljs-attribute">xmlns:sensor</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#sensor"</span>  

        <span class="hljs-attribute">xmlns:body</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#body"</span>  

        <span class="hljs-attribute">xmlns:geom</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#geom"</span>  

        <span class="hljs-attribute">xmlns:joint</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#joint"</span>  

        <span class="hljs-attribute">xmlns:controller</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#controller"</span>  

        <span class="hljs-attribute">xmlns:interface</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#interface"</span>  

        <span class="hljs-attribute">xmlns:rendering</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#rendering"</span>  

        <span class="hljs-attribute">xmlns:renderable</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#renderable"</span>  

        <span class="hljs-attribute">xmlns:physics</span>=<span class="hljs-value">"http://playerstage.sourceforge.net/gazebo/xmlschema/#physics"</span>  

        <span class="hljs-attribute">xmlns:xacro</span>=<span class="hljs-value">"http://ros.org/wiki/xacro"</span>&gt;</span>  

      <span class="hljs-tag">&lt;<span class="hljs-title">xacro:include</span> <span class="hljs-attribute">filename</span>=<span class="hljs-value">"$(find smartcar_description)/urdf/smartcar_body.urdf.xacro"</span> /&gt;</span>  

      <span class="hljs-comment">&lt;!-- Body of SmartCar, with plates, standoffs and Create (including sim sensors) --&gt;</span>  

      <span class="hljs-tag">&lt;<span class="hljs-title">smartcar_body</span>/&gt;</span>  

      <span class="hljs-tag">&lt;<span class="hljs-title">smartcar_sim</span>/&gt;</span>  
    <span class="hljs-tag">&lt;/<span class="hljs-title">robot</span>&gt;</span>`</pre>

    * * *

    然后按照古月大神的方法启动并控制就可以了。下面我要说的就是我比较独特的部分了。 

    现在大部分的移动机器人主要差动轮进行运动，只有linear.x和angular.z两个值，但是我的模型是４个瑞典轮，能提供横向的移动速度，即linear.y。这里对diff_controller进行修改让他能够接受linear.y。 

    diff_controller.py在/opt/ros/indigo/lib/python2.7/dist-packages下。（可以用sudo vim进行修改），修改都已经注释过了（add by yang）。代码如下：

    <pre class="prettyprint">`<span class="hljs-comment">#!/usr/bin/env python</span>

    <span class="hljs-string">""</span><span class="hljs-string">"
      diff_controller.py - controller for a differential drive
      Copyright (c) 2010-2011 Vanadium Labs LLC.  All right reserved.

      Redistribution and use in source and binary forms, with or without
      modification, are permitted provided that the following conditions are met:
          * Redistributions of source code must retain the above copyright
            notice, this list of conditions and the following disclaimer.
          * Redistributions in binary form must reproduce the above copyright
            notice, this list of conditions and the following disclaimer in the
            documentation and/or other materials provided with the distribution.
          * Neither the name of Vanadium Labs LLC nor the names of its 
            contributors may be used to endorse or promote products derived 
            from this software without specific prior written permission.

      THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "</span><span class="hljs-constant">AS</span> <span class="hljs-constant">IS</span><span class="hljs-string">" AND
      ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED
      WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE ARE
      DISCLAIMED. IN NO EVENT SHALL VANADIUM LABS BE LIABLE FOR ANY DIRECT, INDIRECT,
      INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT
      LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA,
      OR PROFITS; OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF
      LIABILITY, WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE
      OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF
      ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
    "</span><span class="hljs-string">""</span>

    import rospy

    from math import sin,cos,pi

    from geometry_msgs.msg import <span class="hljs-constant">Quaternion</span>
    from geometry_msgs.msg import <span class="hljs-constant">Twist</span>
    from nav_msgs.msg import <span class="hljs-constant">Odometry</span>
    from diagnostic_msgs.msg import *
    from tf.broadcaster import <span class="hljs-constant">TransformBroadcaster</span>

    from ax12 import *
    from controllers import *
    from struct import unpack

    <span class="hljs-class"><span class="hljs-keyword">class</span> <span class="hljs-title">DiffController</span>(<span class="hljs-title">Controller</span>):</span>
        <span class="hljs-string">""</span><span class="hljs-string">" Controller to handle movement &amp; odometry feedback for a differential 
                drive mobile base. "</span><span class="hljs-string">""</span>
        <span class="hljs-function"><span class="hljs-keyword">def</span> </span>__init_<span class="hljs-number">_</span>(<span class="hljs-keyword">self</span>, device, name)<span class="hljs-symbol">:</span>
            <span class="hljs-constant">Controller</span>.__init_<span class="hljs-number">_</span>(<span class="hljs-keyword">self</span>, device, name)
            <span class="hljs-keyword">self</span>.pause = <span class="hljs-constant">True</span>
            <span class="hljs-keyword">self</span>.last_cmd = rospy.<span class="hljs-constant">Time</span>.now()

            <span class="hljs-comment"># parameters: rates and geometry</span>
            <span class="hljs-keyword">self</span>.rate = rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/rate'</span>,<span class="hljs-number">10.0</span>)
            <span class="hljs-keyword">self</span>.timeout = rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/timeout'</span>,<span class="hljs-number">1.0</span>)
            <span class="hljs-keyword">self</span>.t_delta = rospy.<span class="hljs-constant">Duration</span>(<span class="hljs-number">1.0</span>/<span class="hljs-keyword">self</span>.rate)
            <span class="hljs-keyword">self</span>.t_next = rospy.<span class="hljs-constant">Time</span>.now() + <span class="hljs-keyword">self</span>.t_delta
            <span class="hljs-keyword">self</span>.ticks_meter = float(rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/ticks_meter'</span>))
            <span class="hljs-keyword">self</span>.base_width = float(rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/base_width'</span>))

            <span class="hljs-keyword">self</span>.base_frame_id = rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/base_frame_id'</span>, <span class="hljs-string">'base_link'</span>)
            <span class="hljs-keyword">self</span>.odom_frame_id = rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/odom_frame_id'</span>, <span class="hljs-string">'odom'</span>)

            <span class="hljs-comment"># parameters: PID</span>
            <span class="hljs-keyword">self</span>.<span class="hljs-constant">Kp</span> = rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/Kp'</span>, <span class="hljs-number">5</span>)
            <span class="hljs-keyword">self</span>.<span class="hljs-constant">Kd</span> = rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/Kd'</span>, <span class="hljs-number">1</span>)
            <span class="hljs-keyword">self</span>.<span class="hljs-constant">Ki</span> = rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/Ki'</span>, <span class="hljs-number">0</span>)
            <span class="hljs-keyword">self</span>.<span class="hljs-constant">Ko</span> = rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/Ko'</span>, <span class="hljs-number">50</span>)

            <span class="hljs-comment"># parameters: acceleration</span>
            <span class="hljs-keyword">self</span>.accel_limit = rospy.get_param(<span class="hljs-string">'~controllers/'</span>+name+<span class="hljs-string">'/accel_limit'</span>, <span class="hljs-number">0</span>.<span class="hljs-number">1</span>)
            <span class="hljs-keyword">self</span>.max_accel = int(<span class="hljs-keyword">self</span>.accel_limit*<span class="hljs-keyword">self</span>.ticks_meter/<span class="hljs-keyword">self</span>.rate)

            <span class="hljs-comment"># output for joint states publisher</span>
            <span class="hljs-keyword">self</span>.joint_names = [<span class="hljs-string">"base_l_wheel_joint"</span>,<span class="hljs-string">"base_r_wheel_joint"</span>]
            <span class="hljs-keyword">self</span>.joint_positions = [<span class="hljs-number">0</span>,<span class="hljs-number">0</span>]
            <span class="hljs-keyword">self</span>.joint_velocities = [<span class="hljs-number">0</span>,<span class="hljs-number">0</span>]

            <span class="hljs-comment"># internal data            </span>
            <span class="hljs-keyword">self</span>.v_left = <span class="hljs-number">0</span>                 <span class="hljs-comment"># current setpoint velocity</span>
            <span class="hljs-keyword">self</span>.v_right = <span class="hljs-number">0</span>
            <span class="hljs-keyword">self</span>.v_des_left = <span class="hljs-number">0</span>             <span class="hljs-comment"># cmd_vel setpoint</span>
            <span class="hljs-keyword">self</span>.v_des_right = <span class="hljs-number">0</span>
            <span class="hljs-keyword">self</span>.enc_left = <span class="hljs-constant">None</span>            <span class="hljs-comment"># encoder readings</span>
            <span class="hljs-keyword">self</span>.enc_right = <span class="hljs-constant">None</span>
            <span class="hljs-keyword">self</span>.x = <span class="hljs-number">0</span>                      <span class="hljs-comment"># position in xy plane</span>
            <span class="hljs-keyword">self</span>.y = <span class="hljs-number">0</span>
            <span class="hljs-keyword">self</span>.th = <span class="hljs-number">0</span>
            <span class="hljs-keyword">self</span>.dx = <span class="hljs-number">0</span>                     <span class="hljs-comment"># speeds in x/rotation</span>
            <span class="hljs-keyword">self</span>.dy = <span class="hljs-number">0</span>                     <span class="hljs-comment"># add by yang</span>
        <span class="hljs-keyword">self</span>.dr = <span class="hljs-number">0</span>
            <span class="hljs-keyword">self</span>.<span class="hljs-keyword">then</span> = rospy.<span class="hljs-constant">Time</span>.now()    <span class="hljs-comment"># time for determining dx/dy</span>

            <span class="hljs-comment"># subscriptions</span>
            rospy.<span class="hljs-constant">Subscriber</span>(<span class="hljs-string">"cmd_vel"</span>, <span class="hljs-constant">Twist</span>, <span class="hljs-keyword">self</span>.cmdVelCb)
            <span class="hljs-keyword">self</span>.odomPub = rospy.<span class="hljs-constant">Publisher</span>(<span class="hljs-string">"odom"</span>, <span class="hljs-constant">Odometry</span>, queue_size=<span class="hljs-number">5</span>)
            <span class="hljs-keyword">self</span>.odomBroadcaster = <span class="hljs-constant">TransformBroadcaster</span>()

            rospy.loginfo(<span class="hljs-string">"Started yang DiffController ("</span>+name+<span class="hljs-string">"). Geometry: "</span> + str(<span class="hljs-keyword">self</span>.base_width) + <span class="hljs-string">"m wide, "</span> + str(<span class="hljs-keyword">self</span>.ticks_meter) + <span class="hljs-string">" ticks/m."</span>)

        <span class="hljs-function"><span class="hljs-keyword">def</span> </span>startup(<span class="hljs-keyword">self</span>)<span class="hljs-symbol">:</span>
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">not</span> <span class="hljs-keyword">self</span>.<span class="hljs-symbol">fake:</span>
                <span class="hljs-keyword">self</span>.setup(<span class="hljs-keyword">self</span>.<span class="hljs-constant">Kp</span>,<span class="hljs-keyword">self</span>.<span class="hljs-constant">Kd</span>,<span class="hljs-keyword">self</span>.<span class="hljs-constant">Ki</span>,<span class="hljs-keyword">self</span>.<span class="hljs-constant">Ko</span>) 

        <span class="hljs-function"><span class="hljs-keyword">def</span> </span>update(<span class="hljs-keyword">self</span>)<span class="hljs-symbol">:</span>
            now = rospy.<span class="hljs-constant">Time</span>.now()
            <span class="hljs-keyword">if</span> now &gt; <span class="hljs-keyword">self</span>.<span class="hljs-symbol">t_next:</span>
                elapsed = now - <span class="hljs-keyword">self</span>.<span class="hljs-keyword">then</span>
                <span class="hljs-keyword">self</span>.<span class="hljs-keyword">then</span> = now
                elapsed = elapsed.to_sec()

                <span class="hljs-keyword">if</span> <span class="hljs-keyword">self</span>.<span class="hljs-symbol">fake:</span>
                    x = cos(<span class="hljs-keyword">self</span>.th)*<span class="hljs-keyword">self</span>.dx*elapsed
                    y = -sin(<span class="hljs-keyword">self</span>.th)*<span class="hljs-keyword">self</span>.dx*elapsed
            <span class="hljs-comment">#self.x and self.y has changed for the effort of dy</span>
                    <span class="hljs-keyword">self</span>.x += cos(<span class="hljs-keyword">self</span>.th)*<span class="hljs-keyword">self</span>.dx*elapsed + sin(<span class="hljs-keyword">self</span>.th)*<span class="hljs-keyword">self</span>.dy*elapsed
                    <span class="hljs-keyword">self</span>.y += sin(<span class="hljs-keyword">self</span>.th)*<span class="hljs-keyword">self</span>.dx*elapsed + cos(<span class="hljs-keyword">self</span>.th)*<span class="hljs-keyword">self</span>.dy*elapsed
                    <span class="hljs-keyword">self</span>.th += <span class="hljs-keyword">self</span>.dr*elapsed
                <span class="hljs-symbol">else:</span>
                    <span class="hljs-comment"># read encoders</span>
                    <span class="hljs-symbol">try:</span>
                        left, right = <span class="hljs-keyword">self</span>.status()
                    except <span class="hljs-constant">Exception</span> as <span class="hljs-symbol">e:</span>
                        rospy.logerr(<span class="hljs-string">"Could not update encoders: "</span> + str(e))
                        <span class="hljs-keyword">return</span>
                    rospy.logdebug(<span class="hljs-string">"Encoders: "</span> + str(left) +<span class="hljs-string">","</span>+ str(right))

                    <span class="hljs-comment"># calculate odometry</span>
                    <span class="hljs-keyword">if</span> <span class="hljs-keyword">self</span>.enc_left == <span class="hljs-constant">None</span><span class="hljs-symbol">:</span>
                        d_left = <span class="hljs-number">0</span>
                        d_right = <span class="hljs-number">0</span>
                    <span class="hljs-symbol">else:</span>
                        d_left = (left - <span class="hljs-keyword">self</span>.enc_left)/<span class="hljs-keyword">self</span>.ticks_meter
                        d_right = (right - <span class="hljs-keyword">self</span>.enc_right)/<span class="hljs-keyword">self</span>.ticks_meter
                    <span class="hljs-keyword">self</span>.enc_left = left
                    <span class="hljs-keyword">self</span>.enc_right = right

                    d = (d_left+d_right)/<span class="hljs-number">2</span>
                    th = (d_right-d_left)/<span class="hljs-keyword">self</span>.base_width
                    <span class="hljs-keyword">self</span>.dx = d / elapsed
                    <span class="hljs-keyword">self</span>.dr = th / elapsed

                    <span class="hljs-keyword">if</span> (d != <span class="hljs-number">0</span>)<span class="hljs-symbol">:</span>
                        x = cos(th)*d
                        y = -sin(th)*d
                        <span class="hljs-keyword">self</span>.x = <span class="hljs-keyword">self</span>.x + (cos(<span class="hljs-keyword">self</span>.th)*x - sin(<span class="hljs-keyword">self</span>.th)*y)
                        <span class="hljs-keyword">self</span>.y = <span class="hljs-keyword">self</span>.y + (sin(<span class="hljs-keyword">self</span>.th)*x + cos(<span class="hljs-keyword">self</span>.th)*y)
                    <span class="hljs-keyword">if</span> (th != <span class="hljs-number">0</span>)<span class="hljs-symbol">:</span>
                        <span class="hljs-keyword">self</span>.th = <span class="hljs-keyword">self</span>.th + th

                <span class="hljs-comment"># publish or perish</span>
                quaternion = <span class="hljs-constant">Quaternion</span>()
                quaternion.x = <span class="hljs-number">0</span>.<span class="hljs-number">0</span> 
                quaternion.y = <span class="hljs-number">0</span>.<span class="hljs-number">0</span>
                quaternion.z = sin(<span class="hljs-keyword">self</span>.th/<span class="hljs-number">2</span>)
                quaternion.w = cos(<span class="hljs-keyword">self</span>.th/<span class="hljs-number">2</span>)
                <span class="hljs-keyword">self</span>.odomBroadcaster.sendTransform(
                    (<span class="hljs-keyword">self</span>.x, <span class="hljs-keyword">self</span>.y, <span class="hljs-number">0</span>), 
                    (quaternion.x, quaternion.y, quaternion.z, quaternion.w),
                    rospy.<span class="hljs-constant">Time</span>.now(),
                    <span class="hljs-keyword">self</span>.base_frame_id,
                    <span class="hljs-keyword">self</span>.odom_frame_id
                    )

                odom = <span class="hljs-constant">Odometry</span>()
                odom.header.stamp = now
                odom.header.frame_id = <span class="hljs-keyword">self</span>.odom_frame_id
                odom.pose.pose.position.x = <span class="hljs-keyword">self</span>.x
                odom.pose.pose.position.y = <span class="hljs-keyword">self</span>.y
                odom.pose.pose.position.z = <span class="hljs-number">0</span>
                odom.pose.pose.orientation = quaternion
                odom.child_frame_id = <span class="hljs-keyword">self</span>.base_frame_id
                odom.twist.twist.linear.x = <span class="hljs-keyword">self</span>.dx
                odom.twist.twist.linear.y = <span class="hljs-keyword">self</span>.dy  <span class="hljs-comment">#it was 0 before changed</span>
                odom.twist.twist.angular.z = <span class="hljs-keyword">self</span>.dr
                <span class="hljs-keyword">self</span>.odomPub.publish(odom)

                <span class="hljs-keyword">if</span> now &gt; (<span class="hljs-keyword">self</span>.last_cmd + rospy.<span class="hljs-constant">Duration</span>(<span class="hljs-keyword">self</span>.timeout))<span class="hljs-symbol">:</span>
                    <span class="hljs-keyword">self</span>.v_des_left = <span class="hljs-number">0</span>
                    <span class="hljs-keyword">self</span>.v_des_right = <span class="hljs-number">0</span>

                <span class="hljs-comment"># update motors</span>
                <span class="hljs-keyword">if</span> <span class="hljs-keyword">not</span> <span class="hljs-keyword">self</span>.<span class="hljs-symbol">fake:</span>
                    <span class="hljs-keyword">if</span> <span class="hljs-keyword">self</span>.v_left &lt; <span class="hljs-keyword">self</span>.<span class="hljs-symbol">v_des_left:</span>
                        <span class="hljs-keyword">self</span>.v_left += <span class="hljs-keyword">self</span>.max_accel
                        <span class="hljs-keyword">if</span> <span class="hljs-keyword">self</span>.v_left &gt; <span class="hljs-keyword">self</span>.<span class="hljs-symbol">v_des_left:</span>
                            <span class="hljs-keyword">self</span>.v_left = <span class="hljs-keyword">self</span>.v_des_left
                    <span class="hljs-symbol">else:</span>
                        <span class="hljs-keyword">self</span>.v_left -= <span class="hljs-keyword">self</span>.max_accel
                        <span class="hljs-keyword">if</span> <span class="hljs-keyword">self</span>.v_left &lt; <span class="hljs-keyword">self</span>.<span class="hljs-symbol">v_des_left:</span>
                            <span class="hljs-keyword">self</span>.v_left = <span class="hljs-keyword">self</span>.v_des_left

                    <span class="hljs-keyword">if</span> <span class="hljs-keyword">self</span>.v_right &lt; <span class="hljs-keyword">self</span>.<span class="hljs-symbol">v_des_right:</span>
                        <span class="hljs-keyword">self</span>.v_right += <span class="hljs-keyword">self</span>.max_accel
                        <span class="hljs-keyword">if</span> <span class="hljs-keyword">self</span>.v_right &gt; <span class="hljs-keyword">self</span>.<span class="hljs-symbol">v_des_right:</span>
                            <span class="hljs-keyword">self</span>.v_right = <span class="hljs-keyword">self</span>.v_des_right
                    <span class="hljs-symbol">else:</span>
                        <span class="hljs-keyword">self</span>.v_right -= <span class="hljs-keyword">self</span>.max_accel
                        <span class="hljs-keyword">if</span> <span class="hljs-keyword">self</span>.v_right &lt; <span class="hljs-keyword">self</span>.<span class="hljs-symbol">v_des_right:</span>
                            <span class="hljs-keyword">self</span>.v_right = <span class="hljs-keyword">self</span>.v_des_right
                    <span class="hljs-keyword">self</span>.write(<span class="hljs-keyword">self</span>.v_left, <span class="hljs-keyword">self</span>.v_right)

                <span class="hljs-keyword">self</span>.t_next = now + <span class="hljs-keyword">self</span>.t_delta

        <span class="hljs-function"><span class="hljs-keyword">def</span> </span>shutdown(<span class="hljs-keyword">self</span>)<span class="hljs-symbol">:</span>
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">not</span> <span class="hljs-keyword">self</span>.<span class="hljs-symbol">fake:</span>
                <span class="hljs-keyword">self</span>.write(<span class="hljs-number">0</span>,<span class="hljs-number">0</span>)

        <span class="hljs-function"><span class="hljs-keyword">def</span> </span>cmdVelCb(<span class="hljs-keyword">self</span>,req)<span class="hljs-symbol">:</span>
            <span class="hljs-string">""</span><span class="hljs-string">" Handle movement requests. "</span><span class="hljs-string">""</span>
            <span class="hljs-keyword">self</span>.last_cmd = rospy.<span class="hljs-constant">Time</span>.now()
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">self</span>.<span class="hljs-symbol">fake:</span>
                <span class="hljs-keyword">self</span>.dx = req.linear.x        <span class="hljs-comment"># m/s</span>
                <span class="hljs-keyword">self</span>.dy = req.linear.y        <span class="hljs-comment"># m/s add by yang</span>
                <span class="hljs-keyword">self</span>.dr = req.angular.z       <span class="hljs-comment"># rad/s</span>
            <span class="hljs-symbol">else:</span>
                <span class="hljs-comment"># set motor speeds in ticks per 1/30s</span>
                <span class="hljs-keyword">self</span>.v_des_left = int( ((req.linear.x - (req.angular.z * <span class="hljs-keyword">self</span>.base_width/<span class="hljs-number">2.0</span>)) * <span class="hljs-keyword">self</span>.ticks_meter) / <span class="hljs-number">30.0</span>)
                <span class="hljs-keyword">self</span>.v_des_right = int( ((req.linear.x + (req.angular.z * <span class="hljs-keyword">self</span>.base_width/<span class="hljs-number">2.0</span>)) * <span class="hljs-keyword">self</span>.ticks_meter) / <span class="hljs-number">30.0</span>)

        <span class="hljs-function"><span class="hljs-keyword">def</span> </span>getDiagnostics(<span class="hljs-keyword">self</span>)<span class="hljs-symbol">:</span>
            <span class="hljs-string">""</span><span class="hljs-string">" Get a diagnostics status. "</span><span class="hljs-string">""</span>
            msg = <span class="hljs-constant">DiagnosticStatus</span>()
            msg.name = <span class="hljs-keyword">self</span>.name
            msg.level = <span class="hljs-constant">DiagnosticStatus</span>.<span class="hljs-constant">OK</span>
            msg.message = <span class="hljs-string">"OK"</span>
            <span class="hljs-keyword">if</span> <span class="hljs-keyword">not</span> <span class="hljs-keyword">self</span>.<span class="hljs-symbol">fake:</span>
                msg.values.append(<span class="hljs-constant">KeyValue</span>(<span class="hljs-string">"Left"</span>, str(<span class="hljs-keyword">self</span>.enc_left)))
                msg.values.append(<span class="hljs-constant">KeyValue</span>(<span class="hljs-string">"Right"</span>, str(<span class="hljs-keyword">self</span>.enc_right)))
            msg.values.append(<span class="hljs-constant">KeyValue</span>(<span class="hljs-string">"dX"</span>, str(<span class="hljs-keyword">self</span>.dx)))
            msg.values.append(<span class="hljs-constant">KeyValue</span>(<span class="hljs-string">"dR"</span>, str(<span class="hljs-keyword">self</span>.dr)))
            <span class="hljs-keyword">return</span> msg

        <span class="hljs-comment">###</span>
        <span class="hljs-comment">### Controller Specification: </span>
        <span class="hljs-comment">###</span>
        <span class="hljs-comment">###  setup: Kp, Kd, Ki, Ko (all unsigned char)</span>
        <span class="hljs-comment">###</span>
        <span class="hljs-comment">###  write: left_speed, right_speed (2-byte signed, ticks per frame)</span>
        <span class="hljs-comment">###</span>
        <span class="hljs-comment">###  status: left_enc, right_enc (4-byte signed)</span>
        <span class="hljs-comment">### </span>

        <span class="hljs-function"><span class="hljs-keyword">def</span> </span>setup(<span class="hljs-keyword">self</span>, kp, kd, ki, ko)<span class="hljs-symbol">:</span>
            success = <span class="hljs-keyword">self</span>.device.execute(<span class="hljs-number">253</span>, <span class="hljs-constant">AX_CONTROL_SETUP</span>, [<span class="hljs-number">10</span>, kp, kd, ki, ko])

        <span class="hljs-function"><span class="hljs-keyword">def</span> </span>write(<span class="hljs-keyword">self</span>, left, right)<span class="hljs-symbol">:</span>
            <span class="hljs-string">""</span><span class="hljs-string">" Send a closed-loop speed. Base PID loop runs at 30Hz, these values
                    are therefore in ticks per 1/30 second. "</span><span class="hljs-string">""</span>
            left = left&amp;<span class="hljs-number">0xffff</span>
            right = right&amp;<span class="hljs-number">0xffff</span>
            success = <span class="hljs-keyword">self</span>.device.execute(<span class="hljs-number">253</span>, <span class="hljs-constant">AX_CONTROL_WRITE</span>, [<span class="hljs-number">10</span>, left%<span class="hljs-number">256</span>, left&gt;&gt;<span class="hljs-number">8</span>, right%<span class="hljs-number">256</span>, right&gt;&gt;<span class="hljs-number">8</span>])

        <span class="hljs-function"><span class="hljs-keyword">def</span> </span>status(<span class="hljs-keyword">self</span>)<span class="hljs-symbol">:</span>
            <span class="hljs-string">""</span><span class="hljs-string">" read 32-bit (signed) encoder values. "</span><span class="hljs-string">""</span>
            values = <span class="hljs-keyword">self</span>.device.execute(<span class="hljs-number">253</span>, <span class="hljs-constant">AX_CONTROL_STAT</span>, [<span class="hljs-number">10</span>])
            left_values = <span class="hljs-string">""</span>.join([chr(k) <span class="hljs-keyword">for</span> k <span class="hljs-keyword">in</span> values[<span class="hljs-number">0</span><span class="hljs-symbol">:</span><span class="hljs-number">4</span>] ])        
            right_values = <span class="hljs-string">""</span>.join([chr(k) <span class="hljs-keyword">for</span> k <span class="hljs-keyword">in</span> values[<span class="hljs-number">4</span><span class="hljs-symbol">:</span>] ])
            <span class="hljs-symbol">try:</span>
                left = unpack(<span class="hljs-string">'=l'</span>,left_values)[<span class="hljs-number">0</span>]
                right = unpack(<span class="hljs-string">'=l'</span>,right_values)[<span class="hljs-number">0</span>]
                <span class="hljs-keyword">return</span> [left, right]
            <span class="hljs-symbol">except:</span>
                <span class="hljs-keyword">return</span> <span class="hljs-constant">None</span>
<div>作者：ynb19930428发表于2017/10/9 15:59:54[ 原文链接 ](http://blog.csdn.net/ynb19930428/article/details/78183524)</div><div> 阅读：168评论：3[查看评论 ](http://blog.csdn.net/ynb19930428/article/details/78183524#commentstarget =) </div>