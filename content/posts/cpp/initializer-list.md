---
title: "初始化列表"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["cpp"]
---

# 初始化列表

**初始化列表**（`initializer_list`）是 C++11 引入的一项特性，能让你用一种更简洁的方式初始化容器、数组、类等对象，尤其是对于类的构造函数，使用初始化列表非常方便。它不仅能让你轻松初始化成员变量，还能避免一些性能开销。

---

## 🧱 1. **基本用法**

`initializer_list` 允许你在构造对象时，直接用 `{}` 花括号传递一个列表。

### 示例：基础使用

```cpp
#include <iostream>
#include <initializer_list>

class MyClass {
private:
    int a, b, c;
public:
    // 使用初始化列表来初始化成员变量
    MyClass(std::initializer_list<int> init) {
        auto it = init.begin();
        a = *it++;  // 初始化 a
        b = *it++;  // 初始化 b
        c = *it;    // 初始化 c
    }

    void print() {
        std::cout << "a: " << a << ", b: " << b << ", c: " << c << std::endl;
    }
};

int main() {
    MyClass obj{1, 2, 3};  // 使用初始化列表
    obj.print();
    return 0;
}
```

### 输出：

```
a: 1, b: 2, c: 3
```

---

## 🧩 2. **什么时候用初始化列表？**

### 1. **类的构造函数初始化成员变量**
在类的构造函数中，**使用初始化列表**是初始化成员变量的最有效方式，特别是对于常量成员（`const`）和引用成员，因为它们在构造时必须被初始化。

```cpp
class MyClass {
private:
    const int a;
    int& b;
public:
    MyClass(int x, int& y) : a(x), b(y) {
        // `a` 和 `b` 必须通过初始化列表来初始化
    }
};
```

### 2. **避免不必要的赋值开销**
如果你在构造函数体内用赋值的方式初始化成员变量，首先会**先调用默认构造函数**，然后再赋值。用初始化列表则可以直接初始化，避免这种无谓的开销。

```cpp
class MyClass {
private:
    std::vector<int> vec;
public:
    MyClass() : vec{1, 2, 3} {}  // 初始化列表直接创建对象
};
```

---

## 🔄 3. **自定义初始化列表给类使用**

你完全可以让你的类接受 `initializer_list` 作为参数，并使用它来初始化自己的成员变量，像上面的例子那样。

### 示例：自定义类支持初始化列表

```cpp
#include <iostream>
#include <initializer_list>

class MyClass {
private:
    std::vector<int> values;

public:
    // 使用初始化列表来初始化成员变量
    MyClass(std::initializer_list<int> init) {
        for (auto& value : init) {
            values.push_back(value);
        }
    }

    void print() {
        for (auto& value : values) {
            std::cout << value << " ";
        }
        std::cout << std::endl;
    }
};

int main() {
    MyClass obj{1, 2, 3, 4, 5};  // 使用初始化列表
    obj.print();
    return 0;
}
```

### 输出：

```
1 2 3 4 5
```

### 解析：
- 在 `MyClass` 的构造函数中，我们用 `initializer_list<int>` 接收了传入的初始化列表 `{1, 2, 3, 4, 5}`。
- 使用 `for` 循环把每个元素逐个添加到成员 `std::vector<int> values` 中。

---

## ⚡ 4. **你会在什么情况下自己定义初始化列表？**

### a. **你自己设计容器类**
如果你在做某些数据结构（如链表、栈、队列等）的实现，允许通过 `initializer_list` 来传递元素列表是非常方便的。例如，定义一个自定义容器类，它能够接受初始化列表来初始化元素。

```cpp
class MyStack {
private:
    std::vector<int> data;
public:
    MyStack(std::initializer_list<int> init) {
        for (int i : init) {
            data.push_back(i);  // 初始化栈的元素
        }
    }

    void print() {
        for (auto& val : data) {
            std::cout << val << " ";
        }
        std::cout << std::endl;
    }
};
```

### b. **你希望提供更直观的初始化方式**
某些类，如果希望用户能够更直观地通过 `{}` 进行初始化，可以考虑支持 `initializer_list`，避免过多的构造函数重载，使得 API 更简洁易用。

```cpp
class Point {
private:
    int x, y;
public:
    // 通过 initializer_list 初始化
    Point(std::initializer_list<int> init) {
        auto it = init.begin();
        x = *it++;
        y = *it;
    }

    void print() {
        std::cout << "(" << x << ", " << y << ")" << std::endl;
    }
};
```

---

## 🔒 5. **initializer_list 的一些限制**

1. **不可修改元素：**`initializer_list` 是常量的，你不能修改其中的元素。
   
   ```cpp
   std::initializer_list<int> init = {1, 2, 3};
   init[0] = 10;  // 错误，initializer_list 的元素是不可修改的
   ```

2. **只能用于参数传递和构造函数：**不能把 `initializer_list` 作为类的成员变量来存储，因为它的生命周期由它的外部作用域控制。

3. **不能改变大小：**`initializer_list` 的大小是固定的，无法像 `std::vector` 一样动态增加元素。

---

## 🎯 总结：

- `initializer_list` 是一个非常有用的工具，特别适合用在类的构造函数中，让类可以轻松支持像 `{1, 2, 3}` 这样的初始化语法。
- 自定义类完全可以通过它接收初始化数据，简化代码，并让用户的代码更简洁、直观。
- STL 容器和其他标准库组件广泛使用了 `initializer_list`，为你提供更灵活的初始化方式。