---
title: "C++ 内部类"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["cpp"]
---

# C++ 内部类

兄弟你问到一个**C++黑魔法级的结构设计点**了，内部类（nested class）虽然看起来是“类中套类”，但其实背后藏着一套很有意思的权限与封装逻辑。下面我给你展开讲，保你之后写库、写设计模式的时候用得上。

---

## 🧱 什么是内部类（Nested Class）？

在 C++ 中，一个类可以定义在另一个类的作用域中，比如这样：

```cpp
class Outer {
public:
    class Inner {
    public:
        void sayHi();
    };
};
```

你可以这样用它：

```cpp
Outer::Inner obj;
obj.sayHi();
```

它是一种**作用域上的嵌套**，但不是 Java/Python 那种“绑定外部对象”的嵌套！

---

## 🧙‍♂️ 权限和访问特权说明：

### ✅ 内部类的权限：

- 内部类 **被当作是外部类的一个成员类型**（和 typedef、enum 类似）
- 它**可以访问外部类的 `private` / `protected` 成员**吗？👉 **不能直接访问！**

```cpp
class Outer {
private:
    int secret = 42;

public:
    class Inner {
    public:
        void access(Outer& o) {
            // int x = o.secret; ❌ 编译错误，不能访问 private
        }
    };
};
```

**除非你加上 `friend`：**

```cpp
class Outer {
private:
    int secret = 42;

public:
    class Inner {
    public:
        void access(Outer& o) {
            int x = o.secret; // ✅ OK
        }
    };

    friend class Inner; // 让内部类变成外部类的朋友
};
```

---

### ❌ 外部类不能访问内部类的私有成员

外部类和内部类**互相之间没有默认的友元关系**，它俩是“邻居，不是家人”。

```cpp
class Outer {
public:
    class Inner {
    private:
        int hidden = 1;
    };

    void test() {
        Inner i;
        // int x = i.hidden; ❌ 编译错误
    }
};
```

---

## 🔒 小结：访问权限规则

| 情况                         | 默认可访问？ | 解法                     |
|------------------------------|---------------|----------------------------|
| 内部类访问外部类私有成员     | ❌           | 把内部类声明为 `friend`   |
| 外部类访问内部类私有成员     | ❌           | 把外部类声明为 `friend`   |
| 内部类访问外部类的 `this` 指针 | ❌           | 传参；无自动绑定           |

---

## 🤔 那内部类到底有啥用？

### 1. **逻辑分组：**把只为外部类服务的工具类藏起来
```cpp
class JSON {
private:
    class Parser { /* only JSON uses it */ };
};
```

### 2. **封装复杂结构：**比如树、图中的节点结构
```cpp
class Tree {
    struct Node {
        Node* left;
        Node* right;
    };
};
```

### 3. **访问控制 + 限定作用域：**
内部类的名字不会污染全局命名空间，只能通过 `Outer::Inner` 来访问。

---

## 🛠 限制汇总：

| 限制项                          | 是否存在 | 说明 |
|----------------------------------|-----------|------|
| 自动拥有外部类的 `this`         | ❌        | 不像 Java，得手动传入外部对象引用 |
| 能隐式访问外部类的成员          | ❌        | 除非加 friend |
| 模板嵌套类展开麻烦（特别是定义体外实现） | ✅        | 需要写 `Outer<T>::Inner<T>` 这种丑东西 |
| 无法通过内部类访问外部类 static 变量 | ❌        | 除非加 `friend` 或传引用 |

---

## 🚀 高阶用途拓展（彩蛋时间）

- **实现访问器（Accessor）设计模式**：把访问内部状态的逻辑藏进内部类，再通过友元解锁访问权限。
- **Pimpl（Pointer to implementation）技术的私有实现细节可以用内部类**来封装。
- **辅助定义 Iterator 类**（C++ STL 源码中常见）

---

## 💬 总结一句话：

> C++ 的内部类**不是**自动绑定上下文的“内外绑定关系”，它更像是一种**作用域限定的命名空间工具 + 私有实现容器**，只有你愿意用 `friend` 才能互相打破那层“对象玻璃”。
