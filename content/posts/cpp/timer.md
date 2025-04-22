---
title: "C++ 程序执行计时器的写法"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["cpp"]
---

# C++ 程序执行计时器的写法

提到 **“优雅地”测量程序性能并输出到文件**，我们就不是简简单单扔个 `std::chrono` 然后 `cout` 一把完事儿了——得讲究结构性、复用性、可读性，甚至还能加点儿仪式感✨。

下面我分两种层次跟你说：

---

## ✅ 快速整洁版：适合小段代码测试

### 封装一个 `Timer` 类

```cpp
#include <chrono>
#include <fstream>
#include <string>

class Timer {
public:
    Timer(const std::string& label = "", const std::string& log_file = "timing_log.txt")
        : label_(label), log_file_(log_file), start_time_(std::chrono::high_resolution_clock::now()) {}

    ~Timer() {
        auto end_time = std::chrono::high_resolution_clock::now();
        auto duration_ms = std::chrono::duration_cast<std::chrono::milliseconds>(end_time - start_time_).count();
        std::ofstream ofs(log_file_, std::ios::app); // 追加写
        ofs << "[" << label_ << "] took " << duration_ms << " ms\n";
    }

private:
    std::string label_;
    std::string log_file_;
    std::chrono::high_resolution_clock::time_point start_time_;
};
```

### 用法非常 Zen：
```cpp
{
    Timer t("Tracking Update");  // 离开作用域自动写入时间
    run_tracking_update();
}
```

输出会自动写到 `timing_log.txt`，像这样：

```
[Tracking Update] took 32 ms
```

你甚至可以在 main 函数、模块级别用这个做 performance profiling，一目了然。

---

## 🔥 高阶炫酷版：多点测量 + 分段统计 + 平均值分析

如果你需要对某个函数多次执行做性能统计（比如 1000 帧的处理时间分布），可以这样玩：

### 写个 `Benchmark` 工具：

```cpp
#include <chrono>
#include <vector>
#include <fstream>
#include <string>
#include <numeric>

class Benchmark {
public:
    void start() {
        start_time_ = std::chrono::high_resolution_clock::now();
    }

    void stop() {
        auto end_time = std::chrono::high_resolution_clock::now();
        durations_.push_back(std::chrono::duration<double, std::milli>(end_time - start_time_).count());
    }

    void report(const std::string& label, const std::string& filename = "benchmark_report.txt") {
        std::ofstream ofs(filename, std::ios::app);
        double avg = std::accumulate(durations_.begin(), durations_.end(), 0.0) / durations_.size();
        ofs << "[" << label << "] Average Time: " << avg << " ms over " << durations_.size() << " runs\n";
    }

private:
    std::chrono::high_resolution_clock::time_point start_time_;
    std::vector<double> durations_;
};
```

### 使用示例：

```cpp
Benchmark bm;
for (int i = 0; i < 1000; ++i) {
    bm.start();
    process_frame(i);
    bm.stop();
}
bm.report("Frame Processing");
```

输出就会是：

```
[Frame Processing] Average Time: 12.44 ms over 1000 runs
```

---

## 🌟 模板版：

> 用模板封装定时逻辑，在编译期根据“是否启用性能分析”决定是否启用计时器，做到**零运行时开销**，而且用法和 `ScopedTimer` 一样丝滑。

---

### 🎯 Step 1：定义一个模板类

```cpp
// 定时器启用的版本
template<bool ENABLED = true>
class Timer {
public:
    Timer(const std::string& name) : name_(name) {
        if constexpr (ENABLED) {
            start_ = std::chrono::steady_clock::now();
        }
    }

    ~Timer() {
        if constexpr (ENABLED) {
            auto end = std::chrono::steady_clock::now();
            auto duration = std::chrono::duration_cast<std::chrono::microseconds>(end - start_).count();
            std::cout << "[Timer] " << name_ << " took " << duration << " us\n";
        }
    }

private:
    std::string name_;
    std::chrono::steady_clock::time_point start_;
};
```

---

### 🧩 Step 2：定义宏开关

```cpp
#define ENABLE_PROFILING true  // 改成 false 就完全关闭计时

#define PROFILE_SCOPE(name) Timer<ENABLE_PROFILING> timer_##__LINE__(name)
```

用法就像这样：

```cpp
void SomeFunction() {
    PROFILE_SCOPE("SomeFunction logic");
    // ... 代码逻辑 ...
}
```

#### ✅ 如果 `ENABLE_PROFILING` 为 `false`：

- `Timer<false>` 的计时代码会在编译期被完全裁剪掉，0 运行时成本，不需要 if-else。
- 就像代码没写过一样轻盈。

---

### 💎 进阶功能（可选）

可以加上 `__FILE__`, `__func__`, `__LINE__` 这些宏，让输出更详细：

```cpp
#define PROFILE_SCOPE_LOC() Timer<ENABLE_PROFILING> timer_##__LINE__(std::string(__FILE__) + ":" + std::to_string(__LINE__) + " " + __func__)
```

---

### 🧠 模板的优势总结：

- **零开销切换**：开关一个宏，计时器全体启用/关闭。
- **支持嵌套**：多处调用不会互相干扰。
- **模板裁剪优化**：编译器优化到飞起，启用时详细，不启用时连对象都不构造。

---

要不要我再帮你封装个日志写入到文件版本的？支持你开多线程分析后批量看瓶颈。你这工作量一看就是要上点系统性方案的😎

---

## ✅ 加分项：输出 CSV 图表数据

如果你想可视化这些性能数据，比如用 Python 画图分析，还可以：

```cpp
std::ofstream csv("timing_data.csv");
csv << "frame_id,time_ms\n";
for (size_t i = 0; i < durations.size(); ++i) {
    csv << i << "," << durations[i] << "\n";
}
```

---

## 🎁 总结

| 方法 | 场景 | 优点 |
|------|------|------|
| `Timer` 类 | 局部定时 | 简单自动、代码整洁 |
| `Benchmark` 类 | 多次统计 | 平均、分布、分析 |
| CSV 输出 | 高级分析 | 配合 matplotlib/Excel |

有！而且还真不少。**模板 + RAII + 条件编译** 是实现灵活、优雅、可插拔性能测量的绝配。

咱们这就来盘一个高级 yet 实用的模板方案：  