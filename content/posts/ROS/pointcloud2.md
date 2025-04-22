---
title: "PointCloud2"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["ROS"]
---

# PointCloud2

参考链接：

* [sensor_msgs.point_cloud2](https://docs.ros.org/en/noetic/api/sensor_msgs/html/namespacesensor__msgs_1_1point__cloud2.html)
* [sensor_msgs/PointField](https://docs.ros.org/en/noetic/api/sensor_msgs/html/msg/PointField.html)
* [std_msgs/Header](https://docs.ros.org/en/noetic/api/std_msgs/html/msg/Header.html)
## 作用

`PointCloud2`存储点云，点云是一个n维向量的集合，常见的有3,4维的，即xyz, xyzi。有些传感器产生的点云可能还带有其他字段，这些额外的字段使用`PointField`表示
`sensor_msgs.point_cloud2`不是数据结构，而是存放了许多操作`PointCloud2`的方法的命名空间，可以使用其中的方法来创建`PointCloud2`

## 结构

```
# 包含当前点云数据采集的时间，坐标系。想知道Header到底有哪些字段，看std_msgs/Header

# 点云的2D结构，如果点云无序，那么height为1，width为点数
uint32 height
uint32 width

# 描述data部分字段，即每个点的每个维度
PointField[] fields

bool    is_bigendian # 数据是否为大端
uint32  point_step   # 一个点的字节数
uint32  row_step     # 一行的字节数
uint8[] data         # 存储点的字节数组，大小为(row_step*height)

bool is_dense        # 如果没有无效点，那么这个字段为True
```

## 创建点云

```python
from sensor_msgs.msg import PointCloud2, PointField
from sensor_msgs.point_cloud2 import create_cloud

# Tips, 对于这些使用ROS消息生成系统生成的消息，
# 你总是可以使用msg定义中的字段名作为关键字参数传递给消息类构造函数，
# 从而创建一个消息对象，这里的Header，PointField皆是如此。
# 此外这些字段亦可以通过对象的对应属性访问
def create_pointscloud_xyzi(points, timestamp, frame_id):
    header = Header(stamp=timestamp, frame_id=frame_id)
    dtype = PointField.FLOAT32
    fields = [PointField(name='x', offset=0, datatype=dtype, count=1),
                PointField(name='y', offset=4, datatype=dtype, count=1),
                PointField(name='z', offset=8, datatype=dtype, count=1),
                PointField(name='intensity', offset=12, datatype=dtype, count=1),]
    # create_cloud可以方便地创建点云，不用去深究PointCloud2的数据结构
    return pc2.create_cloud(header, fields, points)
```

