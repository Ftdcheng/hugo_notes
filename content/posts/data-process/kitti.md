---
title: "KITTI"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["data-process"]
---

# KITTI

## 点云数据
### 文件结构
放在`velodyne_points`下，
`velodyne_points/data`放置的是所有点云扫描文件，
`velodyne_points/timestamps.txt`放置的是每个扫描的时间戳，
`velodyne_points/timestamps_start.txt`是扫描开始的时间戳，
`velodyne_points/timestamps_end.txt`是扫描结束的时间戳。

`velodyne_points/data`下的文件数量和`velodyne_points/timestamps*`文件的行数是对应的，
也就是说每个文件就是一个扫描，每个扫描按照文件名顺序排列，依次对应时间戳文件的每一行。

`velodyne_points/timestamps.txt`的每一行是`velodyne_points/timestamps_start.txt`的每一行和`velodyne_points/timestamps_start.txt`的每一行表示的时间段的中点。

`velodyne_points/data`中的单个文件表示一个扫描，一个扫描大概有10w+的点。文件一行代表一个点，一个点有三个字段： `(x, y, z, intensity)`。也就是说对于单个点实际上没有记录时间戳。不过文件中点的出现顺序是激光雷达扫描得到点的顺序。激光雷达扫描得到点的时间间隔是固定的，所以可以通过开始和结束时间戳线性插值估算每个点的时间戳。

### 激光雷达信息
KITTI采集点云使用的激光雷达是`Velodyne`。
* 线数: 16