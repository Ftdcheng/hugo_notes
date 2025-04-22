---
title: "C++ range for"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["cpp"]
---

# C++ range for

要让一个对象能被用于 C++ 的 **range-based for loop**（范围 for 循环）中，它的类需要遵循一定的“协议”，即满足一组特定的要求。这个协议**不是形式化的接口**（不像 Java 那样有 `Iterable` 接口），但你可以把它看作一个“概念”（concept）或“约定”。

我们来拆一下细节：

---

### ✅ **C++ range for 的底层语义**
```cpp
for (auto x : container) {
    // ...
}
```
等价于：
```cpp
{
    auto&& __range = container;
    auto __begin = begin(__range);
    auto __end = end(__range);
    for (; __begin != __end; ++__begin) {
        auto x = *__begin;
        // ...
    }
}
```

也就是说：

---

### 🔑 你的类需要满足以下要求：

#### 1. `begin(obj)` 和 `end(obj)` 必须能被调用
- 这通常意味着：
  - 你的类有成员函数 `begin()` 和 `end()`，**或者**
  - 对你的类定义了适配的 `begin()` 和 `end()` 非成员函数。

C++标准库的 `std::begin()` 和 `std::end()` 其实可以处理很多情况，包括数组、标准容器、自定义类（只要有 `.begin()`、`.end()`）。

#### 2. `begin()` 和 `end()` 返回一个“迭代器”
这个“迭代器”要支持：
- `*it`（解引用）
- `++it`（前缀或后缀都行）
- `it != end`（可以比较）

你可以自定义一个类作为迭代器，只要它满足这些操作，就能工作。

---

### 🛠️ 举个栗子：手写一个可迭代类

```cpp
class MyContainer {
public:
    struct Iterator {
        int current;

        int operator*() const { return current; }
        Iterator& operator++() { ++current; return *this; }
        bool operator!=(const Iterator& other) const { return current != other.current; }
    };

    Iterator begin() const { return Iterator{0}; }
    Iterator end() const { return Iterator{5}; }
};
```

然后就能：
```cpp
for (int x : MyContainer()) {
    std::cout << x << " ";
}
// 输出：0 1 2 3 4
```

---

### ✨ C++20：可以更“正规”一点（但可选）

如果你用的是 C++20，还可以使用 `std::ranges::range` 概念，写成：

```cpp
template <std::ranges::range R>
void do_something(R&& r) {
    for (auto&& x : r) {
        // ...
    }
}
```

---

### 📌 总结：类要支持 range-for，满足以下几点就行

| 要求 | 说明 |
|------|------|
| `begin()` 和 `end()` | 成员函数或可被 `std::begin()/std::end()` 找到的非成员函数 |
| 返回迭代器 | begin/end 返回的对象支持：`*`, `++`, `!=` |
| 迭代器本身 | 可以是你自己定义的类或指针、标准迭代器 |
