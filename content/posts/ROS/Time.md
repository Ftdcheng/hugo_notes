---
title: "ros Time"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["ROS"]
---

# ros Time
## 参考链接
[rospy.rostime](https://docs.ros.org/en/jade/api/rospy/html/rospy.rostime.Time-class.html)
## 作用
rospy.Time在一个消息的Header中的stamp字段中出现，表明这个消息的时间戳。

有的人会使用`rospy.Time.now()`这个方法给stamp字段赋值，不过这个方法只有在有节点启动的时候用，也就是说你在编写一个rospy的节点的时候可以用。

## rospy.Time v.s. datetime
rospy.Time对象主要有两个属性，secs(秒数)，nsecs(纳秒数)，所以这意味着它的精度是纳秒级的。
而python的datetime精度是微秒级的。转换的时候要注意到这个差别。转换例子：
```python
# 匹配date精度的日期部分和超出微秒精度的部分
nanosec_pattern = re.compile("(\d{4}-\d{1,2}-\d{1,2} \d{1,2}:\d{1,2}:\d{1,2}\.\d{1,6})(\d*)")
def date2rostime(time: str):
    sec_match = nanosec_pattern.match(time)
    if sec_match is None:
        raise ValueError
    micro_part = sec_match.group(1)
    nano_part = sec_match.group(2)
    micro_date = datetime.strptime(micro_part, "%Y-%m-%d %H:%M:%S.%f")
    secs = int(micro_date.timestamp())
    nano_secs = micro_date.microsecond * 1000
    if nano_part is not None and nano_part != '':
        nano_secs += int(nano_part)
    return rospy.Time(secs, nano_secs)
```

## 运算
rospy.Time支持减法，减出来的结果是`ros.Duration`对象

rospy.Time之间没有加法，但rospy.Time可以和rospy.Duration加，结果是一个rospy.Time