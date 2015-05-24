---
layout: post
title: ROS细节之：hector_mapping
category: ROS
---

hector\_mapping可以取代gmapping生成地图，而且不需要提供odom信息，另外比gmapping占用运算资源少。hector\_mapping还可以用来检验odom的正确性。

## hector\_mapping的三种用法

### 配置1，在提供odom的情况下，生成地图

```xml
<param name="base_frame" value="base_link" />
<param name="odom_frame" value="odom" />
<param name="pub_map_odom_transform" value="true"/>
```

前面说过hector并不需要odom，那么这里odom主要作用是，为了提供和gmapping一样的接口，实际上，在生成地图过程中，完全没有使用到odom，只有最后在广播坐标变换时（从map到odom的坐标变换），才用到它。  

odom提供的是在odom坐标系下，base\_link的位姿，这个是存在累积误差的，一般通过卡尔曼滤波算法，融合其它观察数据来矫正。hector\_mapping没有试图利用odom，而是直接通过激光扫描数据匹配地图，得到base\_link 在map中的位姿。在输出时，根据已有的odom->base\_link坐标变换和匹配出来的map->base\_link坐标关系，计算出 map->odom变换。

也就是说： map->odom->base\_link这个坐标关系链中，计算出来的是map和base\_link的关系，输出的是 map和odom的关系。从rviz中观察，以map为fix坐标系，可以看到odom坐标系在不停的微调。

![odom微调](/images/2015-05-24-hector-mapping/hector_001.png)

从算法本身来看，因为没有用到odom信息，在遇到相同的几何图形时，例如在很长的隧道里，
采集的激光扫描数据几乎一致，hector\_mapping会和前面的地图匹配，而认为没有前进。而在有odom的算法中不会出现这种情况。

### 配置2，在不提供odom时，生成地图

```xml
<param name="base_frame" value="base_link" />
<param name="odom_frame" value="base_link" />
<param name="pub_map_odom_transform" value="true"/>
```

rviz中看，没有odom坐标系了。

![without odom](/images/2015-05-24-hector-mapping/hector_002.png)

既然hector\_mapping没有用到odom，那么直接生成map到 base\_link的坐标变换，更加简洁明了。看下navi包中的坐标系关系：

![odom微调](/images/2015-05-24-hector-mapping/frame.jpg)

odom坐标系的物理意义，代表小车在以其起点为参考的坐标系中的位姿，而小车的起点理论上在map中是不动的，也即理论上odom代表了小车在map中的位姿。odom一般通过车轮编码或者6轴传感器累加得到。

从纯粹定位导航的角度来看，我们的目的是精确获得机器人在map中的位姿，如果能通过其它手段（例如视觉图形匹配，uwb无线定位），那么odom坐标系就不是必需的。


### 配置3，检验odom的准确性

首先，将odom固定在map坐标系中

```xml
<node pkg="tf" type="static_transform_publisher" name="link_broadcaster" args="0 0 0 0 0 0 odom map 100" />
```

hector\_mapping中不进行坐标变换

```xml
<param name="pub_map_odom_transform" value="false"/>   
```

两个固定的坐标系： map表示地图， odom表示小车起始位置，map和odom重合放置。

![watch odom](/images/2015-05-24-hector-mapping/hector_003.png)

图中蓝色小车的位姿完全由 odom->base\_link 给出。
scanmatcher_frame 是通过激光传感器数据匹配输出的小车实际位姿。
比较图中两者的差别，就能观察出odom的累计误差来。也可以用来调试odom的准确性。