---
layout: post
title: ROSϸ��֮��hector_mapping
category: ROS
---

hector_mapping����ȡ��gmapping���ɵ�ͼ�����Ҳ���Ҫ�ṩodom��Ϣ�������gmappingռ��������Դ�١�hector_mapping��������������odom����ȷ�ԡ�

## hector_mapping�������÷�

### ����1�����ṩodom������£����ɵ�ͼ

```xml
<param name="base_frame" value="base_link" />
<param name="odom_frame" value="odom" />
<param name="pub_map_odom_transform" value="true"/>
```

ǰ��˵��hector������Ҫodom����ô����odom��Ҫ�����ǣ�Ϊ���ṩ��gmappingһ���Ľӿڣ�ʵ���ϣ������ɵ�ͼ�����У���ȫû��ʹ�õ�odom��ֻ������ڹ㲥����任ʱ����map��odom������任�������õ�����  

odom�ṩ������odom����ϵ�£�base\_link��λ�ˣ�����Ǵ����ۻ����ģ�һ��ͨ���������˲��㷨���ں������۲�������������hector_mappingû����ͼ����odom������ֱ��ͨ������ɨ������ƥ���ͼ���õ�base\_link ��map�е�λ�ˡ������ʱ���������е�odom->base\_link����任��ƥ�������map->base\_link�����ϵ������� map->odom�任��

Ҳ����˵�� map->odom->base\_link��������ϵ���У������������map��base\_link�Ĺ�ϵ��������� map��odom�Ĺ�ϵ����rviz�й۲죬��mapΪfix����ϵ�����Կ���odom����ϵ�ڲ�ͣ��΢����

![odom΢��](/images/2015-05-24-hector-mapping/hector_001.png)

���㷨������������Ϊû���õ�odom��Ϣ����������ͬ�ļ���ͼ��ʱ�������ںܳ�������
�ɼ��ļ���ɨ�����ݼ���һ�£�hector\_mapping���ǰ��ĵ�ͼƥ�䣬����Ϊû��ǰ����������odom���㷨�в���������������

### ����2���ڲ��ṩodomʱ�����ɵ�ͼ

```xml
<param name="base_frame" value="base_link" />
<param name="odom_frame" value="base_link" />
<param name="pub_map_odom_transform" value="true"/>
```

rviz�п���û��odom����ϵ�ˡ�
![without odom](/images/2015-05-24-hector-mapping/hector_002.png)

��Ȼhector\_mappingû���õ�odom����ôֱ������map�� base\_link������任�����Ӽ�����ˡ�����navi���е�����ϵ��ϵ��

![odom΢��](/images/2015-05-24-hector-mapping/frame.jpg)

odom����ϵ���������壬����С�����������Ϊ�ο�������ϵ�е�λ�ˣ���С���������������map���ǲ����ģ�Ҳ��������odom������С����map�е�λ�ˡ�odomһ��ͨ�����ֱ������6�ᴫ�����ۼӵõ���

�Ӵ��ⶨλ�����ĽǶ����������ǵ�Ŀ���Ǿ�ȷ��û�������map�е�λ�ˣ������ͨ�������ֶΣ������Ӿ�ͼ��ƥ�䣬uwb���߶�λ������ôodom����ϵ�Ͳ��Ǳ���ġ�


### ����3������odom��׼ȷ��

���ȣ���odom�̶���map����ϵ��

```xml
<node pkg="tf" type="static_transform_publisher" name="link_broadcaster" args="0 0 0 0 0 0 odom map 100" />
```

hector\_mapping�в���������任

```xml
<param name="pub_map_odom_transform" value="false"/>   
```

�����̶�������ϵ�� map��ʾ��ͼ�� odom��ʾС����ʼλ�ã�map��odom�غϷ��á�

![watch odom](/images/2015-05-24-hector-mapping/hector_003.png)

ͼ����ɫС����λ����ȫ�� odom->base\_link ������
scanmatcher_frame ��ͨ�����⴫��������ƥ�������С��ʵ��λ�ˡ�
�Ƚ�ͼ�����ߵĲ�𣬾��ܹ۲��odom���ۼ��������Ҳ������������odom��׼ȷ�ԡ�