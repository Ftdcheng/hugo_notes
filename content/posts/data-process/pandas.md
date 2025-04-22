---
title: "pandas"
date: 2025-04-22
author: "Citrus Cheng"
tags: ["data-process"]
---

# pandas
## 基本属性

* `DataFrame.columns`: 表头，一个列表

## 数据访问

* `DataFrame.loc[row_idx, col_idx]`: 访问`row_idx`行`col_idx`列的元素
* `Series[idx]`: 访问`idx`处的元素 

## 合并

* `pandas.concat([df1, df2, ..., dfn], axis=a)` 沿着轴`a`合并df1, df2, ..., dfn

## 批量操作

* `DataFrame.apply(f)`，对DataFrame的每一行应用f，f的参数是一行数据，一行数据使用`Series`封装