---
layout: post
title:  介绍几款飞行数据分析软件
date:   2022-10-19 10:05:55 +0800
image:  /assets/images/blog/post-2.jpg
tags:   flight data
---

本文将介绍几款飞行数据（或发动机数据）分析软件，提供一些思路帮您排故解难。

#### CloudAhoy

CloudAhoy是一款用于飞行员飞行后汇报的产品。它基于云计算，允许在任何设备（包括台式机、iPad、Android和iPhone）上的任何浏览器中执行简报。CloudAhoy成立于2011年，由一位飞行员工程师创建。

<img src="/assets/images/blog/post-2.png" width="100%">

这个产品的特点可以导入目前世面上大部分通航飞机的飞行数据，包括Garmin EFIS (G1000, G3X, G300)SD 卡的.CSV数据、Avidyne设备的.CSV数据、坏精灵导航（Bad Elf GPS）.GPX数据、GRT航电的.CSV数据、Dynon航电的.CSV数据、Dynon航电的.CSV数据、Appareo GAU 1000设备的.CSV数据、IGC记录器.IGC数据、Garmin VIRB的导航.GPX数据以及飞行模拟软件的数据。

数据导入平台后，普通用户可以导出KML数据。如果是付费用户，根据用户等级可以有二维和三维轨迹、带仪表的驾驶舱视图、全球航空图表、自动分割飞行阶段、3D飞机模型、教员评分等功能。只要注册就可以免费Pro版用户35天的试用权限，只要有足够的邮箱理论上可以白嫖。

国内用户最大的缺点就是要翻墙后使用，因为二维轨迹要使用到google地图功能。还有就是对发动机的一些数据处理不够细致，仅能看下大概的曲线。

最大优点就是对飞行数据可以自动识别处于什么飞行任务或阶段，例如：近进着陆、转弯。可以结合CESIUM地图显示三维轨迹，甚至可以用飞行员的第一视角来体验当时的飞行状态，数字仪表跳动的数据给你带犹如亲临驾驶舱的感觉。

<img src="/assets/images/blog/post-2.gif" width="100%">


#### 软件架构

关于编译原理请
