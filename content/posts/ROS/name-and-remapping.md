---
title: "ROS 名称"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["ROS"]
---

# ROS 名称

## 名称

名称(name)全名是图资源名称(Graph Resource Names)，是用来标识ROS中一个资源的，比如节点、话题、服务、参数等

为了让名称减少冲突，引入了名称空间（namespace）的概念。名称存在于命名空间中，命名空间可以存在于命名空间中，命名空间中可以有多个名称。这样的话，我们知道名称空间肯定是一层套一层的，是有层次的。这就引出了不同类别的名称：

* 基名称(base)，没有带名称空间修饰的名称，事实上是相对名称的一种，和相对名称有相同的解析规则，常用来初始化节点
* 相对名称(relative)，相对于名称空间的名称，在解析的时候会添加上前面的名称空间修饰
* 全局名称(global)，以`/`开头的都是全局名称，在代码中尽量不要用这种名称，这会限制代码的通用性
* 私有名称(private)，把名称转换成一个命名空间，从参数服务器上传递参数到特定节点的时候很有用。

名称的解析与命名空间相关，比如你在一个命名空间`aicv`下创建了一个节点`turtle_control`那么它的完整的名称就是`/aicv/turtle_control`

你在launch文件中启动node的时候有规定name属性，这个name就是我们的名称，比如

```xml
<?xml version="1.0"?>
<launch>
    <group ns="aicv">
    	<node pkg="rosplayground" type="time_service" name="operation"/>
    </group>
</launch>
```

这个launch文件规定了名称为operation的节点会启动。启动之后，节点完整的名称就是`/aicv/operation`

如果你没有使用launch文件，你直接执行了rosrun，并没有规定命名空间。那么默认你在全局的命名空间中操作。比如

```bash
rosrun rosplayground time_service 
```

这样启动完之后你的节点名称就变成了`/time_service_node`（我们在ros::init中规定的名称，launch文件中的name会覆盖这个设置）

你在发布/订阅话题或者服务的时候也会规定话题或者服务的名称，这个名称就是这里的名称

此外消息类型和服务类型也有名称，他们的名称通常是包名/类型名，比如std_msgs/String

## 重映射

### 为什么要重映射

有这么一种情况，一个话题被一系列节点订阅，这个时候你有一个新的节点，这个新的节点也想订阅这个话题，但我们的话题固然有个名字，而新的节点订阅的话题不是这个话题的名字，但是它们的话题类型是一样的，这个时候我们就可以重映射，这就像在没有修改我们新节点代码的情况下，更改了新节点订阅话题的名字。

### 重映射的方法

#### \<remap\>标签

在launch文件中，你启动节点时可以在节点标签\<node\>中添加\<remap\>标签，让节点订阅的话题/服务的名称换成新的。

```xml
<?xml version="1.0"?>
<launch>
    <group ns="aicv">
    	<node pkg="rosplayground" type="time_client" name="time_client">
            <remap from="time_service" to="operation"/>
        </node>
    </group>
</launch>
```

这里我们的time_client节点原先请求的服务是time_service，重映射之后就请求operation服务了。

#### rosrun参数

```bash
rosrun rosplayground time_client time_service:=operation
```

