---
title: pandas和polars
date: 2025-03-17 18:55:09 +0800
categories: [Coding, python]
tag: [python, pandas, polars]
math: true
mermaid: true
---




本来想记录一些基本用法的，但是发现官方文档已经够详细了，再记录就有点多余了。觉着更关键的还是多使用，熟能生巧。不用就忘，这是必不可免的。所以，总结一句话，那就是**多写代码**。

那这篇文章要做什么呢，我也在思考，不如先做个总结和对比吧。

（以下介绍均来自[深度求索](https://chat.deepseek.com/)）

### pandas介绍

诞生于2008年，是 Python 数据科学生态的核心工具之一

- 底层使用 NumPy 数组，支持丰富的数值计算和向量化操作
- 适合中小规模数据（GB级别以内）
- 支持数据清洗、聚合、时间序列、合并、可视化等操作

局限：

- 默认单线程运行，处理大数据时效率较低；
- 操作时可能产生数据副本，内存利用率不够高效

官方文档：[pandas documentation](https://pandas.pydata.org/docs/)


### polars介绍

用 Rust 编写，支持多线程和查询优化，速度比 Pandas 快数倍甚至数十倍
- 采用 Apache Arrow 内存格式，减少不必要的内存复制
- 通过 `lazy()` 模式优化执行计划，提升复杂查询效率
- 能处理远超内存大小的数据集（通过流式处理）

官方文档：[Index - Polars user guide](https://docs.pola.rs/)<br>
API文档：[Python API reference — Polars documentation](https://docs.pola.rs/api/python/stable/reference/index.html)


### 对比

```python
import polars as pl

df_pd = pd.DataFrame(
    {
        "name": ["alice", "ben", "chole", "daniel"],
        "age": [23, 21, 20, 24],
        "weight": [57.9, 72.5, 53.6, 83.1]
    }
)

df_pl = pd.DataFrame(
    {
        "name": ["alice", "ben", "chole", "daniel"],
        "age": [23, 21, 20, 24],
        "weight": [57.9, 72.5, 53.6, 83.1]
    }
)
```

#### 列的引用

