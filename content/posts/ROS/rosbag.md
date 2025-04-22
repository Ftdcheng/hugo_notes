---
title: "rosbag"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["ROS"]
---

# rosbag

rosbag是一种保存ROS系统内的历史消息的数据结构，扩展名一般是`.bag`，通过命令`rosbag`进行播放或者检查。

参考链接: 
* [rosbag - ros docs](https://wiki.ros.org/rosbag)
* [rosbag.Bag](https://docs.ros.org/en/kinetic/api/rosbag/html/python/rosbag.bag.Bag-class.html)

## rosbag包
### 安装

```bash
sudo apt install ros-{ROS_DISTRO}-rosbag
```

然后你可以导入使用
```python
import rosbag
```

值得注意的是在系统默认python环境中无需额外的配置即可导入`rosbag`，然而你如果使用`venv`或者`anaconda`需要安装一些额外的包。

这些包有：`pyyaml`, `pycryptodomex`, `gnupg`, `rospkg`，激活环境后安装：
```bash
pip install pycryptodome pyyaml gnupg rospkg
``` 

在你使用`venv`的时候你需要修改虚拟环境目录下的`bin/activate`在它的最后一行加上`source /opt/ros/${ROS-DISTRO}/setup.bash`。完成这些配置后你的rosbag才能成功导入。

### 写入

```python
with rosbag.Bag('test.bag', 'w') as bag:
    bag.write(topic_name, msg)
```

### 读取
```python
with rosbag.Bag('test.bag', 'r') as bag:
    for topic, msg, t in bag.read_messages(topics=["chater", "numbers"]):
        print(msg)
```

使用with进行读写是一个简便的方法，否则你需要手动对bag进行关闭，使用bag.close()

## 命令行工具

参考链接: [rosbag commandline](https://wiki.ros.org/rosbag/Commandline)
