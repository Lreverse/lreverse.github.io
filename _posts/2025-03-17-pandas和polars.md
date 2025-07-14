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

## pandas介绍

诞生于2008年，是 Python 数据科学生态的核心工具之一

- 底层使用 NumPy 数组，支持丰富的数值计算和向量化操作
- 适合中小规模数据（GB级别以内）
- 支持数据清洗、聚合、时间序列、合并、可视化等操作

局限：

- 默认单线程运行，处理大数据时效率较低；
- 操作时可能产生数据副本，内存利用率不够高效

官方文档：[pandas documentation](https://pandas.pydata.org/docs/)


## polars介绍

用 Rust 编写，支持多线程和查询优化，速度比 Pandas 快数倍甚至数十倍
- 采用 Apache Arrow 内存格式，减少不必要的内存复制
- 通过 `lazy()` 模式优化执行计划，提升复杂查询效率
- 能处理远超内存大小的数据集（通过流式处理）

官方文档：[Index - Polars user guide](https://docs.pola.rs/)<br>
API文档：[Python API reference — Polars documentation](https://docs.pola.rs/api/python/stable/reference/index.html)


## polars

### 创建数据帧

```python
import polars as pl
import datetime as dt

df = pl.DataFrame({
    'name': ['Alice Archer', 'Bob Brown', 'Charlie Cooper', 'David Donovan'],
    'birthdate': [
        dt.date(1990, 1, 1),
        dt.date(1985, 5, 15),
        dt.date(2000, 12, 31),
        dt.date(1995, 7, 20)
    ],
    'weight': [55.5, 70.2, 65.0, 80.1],
    'height': [1.65, 1.80, 1.75, 1.90],
})
```

### 数据查看

```python
df.head()       # 前5行
df.tail(3)      # 后3行
df.shape        # (行数，列数)
df.columns      # 列名
df.schema       # 列名和数据类型
df.describe()   # 统计描述
```

### 数据选择


#### 选择列

```python
df['name', 'weight']

df.select(["name", 'weight'])

df.select(pl.col('name', 'weight'))
```

#### 选择行

```python
df[0]
df[1:3]
```

#### 条件选择

```python
df.filter(
    pl.col('weight') > 60 &
    (pl.col('height')).is_in([1.75, 1.90])
)
"""
与：&
或：|
非：~
"""
```


### 数据操作

#### 添加列

```python
df = df.with_columns(
    pl.Series("salary", [5000, 6000, 7000, 4000]),
    (dt.date.today().year - pl.col('birthdate').dt.year()).alias('age'),
    bmi=pl.col('weight') / (pl.col('height') ** 2),
    is_adult=pl.lit(True),
)
```

#### 删除列

```python
df = df.drop('salary')
```

#### 列重命名

```python
df.rename({'old_name': 'new_name'})
```


#### 排列

```python
df.sort('age', descending=True)
```

#### 分组聚合

```python
df.group_by(
    decade=pl.col('birthdate').dt.year() // 10 * 10
).agg(
    pl.len().alias('sample_size'),
    pl.col('weight').mean().alias('avg_weight'),
    pl.col('height').max().alias('tallest'),
)
```

#### 合并

连接

```python
df2 = pl.DataFrame({
    "name": ["Bob Brown", "David Donovan", "Alice Archer", 'Eliy Smith'],
    "has_kid": [True, False, False, False],
    "siblings": [1, 2, 4, 6],
})

joined = df.join(df2, on='name', how='left').sort('name')
```

垂直合并

```python
df3 = pl.DataFrame({
    "name": ["Ethan Edwards", "Fiona Foster", "Grace Gibson", "Henry Harris"],
    "birthdate": [
        dt.date(1977, 5, 10),
        dt.date(1975, 6, 23),
        dt.date(1973, 7, 22),
        dt.date(1971, 8, 3),
    ],
    "weight": [67.9, 72.5, 57.6, 93.1],
    "height": [1.76, 1.6, 1.66, 1.8],
})

results = pl.concat([df, df3], how='vertical').sort('name')
```


#### 处理缺失值

```python
# 检查缺失值
df.null_count()

# 删除包含缺失值的行
df.drop_nulls()

# 填充缺失值
df.fill_null(0)
```

#### 应用函数

```python
df.select(pl.col('age').map_elements(lambda x: x + 1))
```

### 数据输入/输出

#### 读取数据

```python
pl.read_csv('file.csv')

pl.read_parquet('file.parquet')
```

#### 写入数据

```python
df.write_csv('output.csv')
df.write_parquet('output.parquet')
```

### 数据帧转换

#### polars和pandas相互转换

使用的都是polars的方法

```python
pd_df = pl_df.to_pandas()

pl_df = pl.from_pandas(pd_df)
```

#### 转换成字典

```python
df.to_dicts()

df.rows(named=True) 
```

## pandas

### 创建数据帧

```python
import pandas as pd
import datetime as dt

df = pd.DataFrame({
    'name': ['Alice Archer', 'Bob Brown', 'Charlie Cooper', 'David Donovan'],
    'birthdate': [
        dt.date(1990, 1, 1),
        dt.date(1985, 5, 15),
        dt.date(2000, 12, 31),
        dt.date(1995, 7, 20)
    ],
    'weight': [55.5, 70.2, 65.0, 80.1],
    'height': [1.65, 1.80, 1.75, 1.90],
})
```

### 数据选择

#### 选择列

```python
df['name']

df[['name', 'weight']]
```

#### 选择行

基于位置

```python
# 返回series
df.iloc[0]

# 返回dataframe
df.iloc[[0, 2, 3]]  
df.iloc[0:3]

# 选择元素
df.iloc[2, 0]  # 返回'Charlie Cooper'
```

基于标签

```python

```

#### 条件选择

```python
df[df['age'] > 20]
df[(df['age'] > 20) & (df['weight'] > 60)]
```

### 数据操作

#### 添加列

```python
df['salary'] = [5000, 6000, 7000, 4000]
```

#### 删除列

```python
df.drop('salary', axis=1, inplace=True)
```

#### 排序

```python
df.sort_values('age', ascending=False)
```

#### 分组聚合

```python
df.groupby('age')['weight'].mean()
```


#### 处理缺失值

```python
df.isnull()       # 检查缺失值
df.dropna()       # 删除包含缺失值的行
df.fillna(value)  # 填充缺失值
```

#### 合并

连接

```python
pd.merge(df1, df2, on='key_column')
```

垂直合并

```python
pd.concat([df1, df2])
```


### 数据帧转换

#### 转化成字典

```python
df.to_dict("records")
```