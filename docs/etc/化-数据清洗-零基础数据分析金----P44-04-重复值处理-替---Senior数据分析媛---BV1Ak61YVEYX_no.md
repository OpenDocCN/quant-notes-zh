# 数据分析+金融量化+数据清洗：P44：04 重复值处理+替换 🧹

![](img/d93dfdfaab43baa4a54d2004c8770b67_1.png)

在本节课中，我们将学习如何使用Pandas处理数据中的重复值，以及如何对数据进行映射和替换操作。这些是数据清洗过程中的基础且重要的步骤。

![](img/d93dfdfaab43baa4a54d2004c8770b67_3.png)

![](img/d93dfdfaab43baa4a54d2004c8770b67_4.png)

![](img/d93dfdfaab43baa4a54d2004c8770b67_5.png)

上一节我们介绍了数据的连接与合并，本节中我们来看看如何处理数据中的重复元素以及进行值的替换。

## 删除重复元素

删除重复元素主要涉及两个函数：`duplicated()`用于查询重复行，`drop_duplicates()`用于删除重复行。

首先，我们生成一组包含重复行的示例数据。

```python
import numpy as np
import pandas as pd

# 生成随机数据
data = np.random.randint(0, 100, size=(8, 3))
df = pd.DataFrame(data, columns=['A', 'B', 'C'])

# 人为制造重复行：使第1、3、4、6行与第0行相同
df.iloc[[1, 3, 4, 6]] = df.iloc[0]
```

现在，我们可以使用`duplicated()`函数来检测重复行。

```python
# 检测重复行，默认标记第一个出现之外的行
duplicate_mask = df.duplicated()
print(duplicate_mask)
```

`duplicated()`函数可以通过`keep`参数控制保留哪个重复项：
*   `keep=‘first’`：保留第一个出现的行，标记后续重复项（默认）。
*   `keep=‘last’`：保留最后一个出现的行，标记之前的重复项。
*   `keep=False`：将所有重复项都标记为True。

```python
# 保留最后一个出现的重复行
duplicate_mask_last = df.duplicated(keep='last')
print(duplicate_mask_last)
```

如果想直接获取非重复数据，可以使用布尔索引进行过滤。

```python
# 获取非重复数据
non_duplicate_df = df[~df.duplicated()]
print(non_duplicate_df)
```

更便捷的方法是直接使用`drop_duplicates()`函数删除重复行。

```python
# 直接删除重复行，默认保留第一个
df_dropped = df.drop_duplicates()
print(df_dropped)

# 删除重复行，保留最后一个
df_dropped_last = df.drop_duplicates(keep='last')
print(df_dropped_last)
```

![](img/d93dfdfaab43baa4a54d2004c8770b67_7.png)

![](img/d93dfdfaab43baa4a54d2004c8770b67_9.png)

`drop_duplicates()`函数也有一个`inplace`参数，默认为`False`。如果设置为`True`，则会直接修改原DataFrame对象，使用时需谨慎，避免多次运行。

> **注意**：在早期版本中，某些连接操作后使用`drop_duplicates`可能存在索引相关的小问题。如果遇到，可以尝试先重置索引（`reset_index()`）再进行去重操作。

## 映射与替换处理

映射处理是一个重要的概念，主要包括三种操作：元素值替换、`map`函数应用和索引重命名。

首先，我们创建一个包含多种数据类型的示例DataFrame。

```python
# 创建示例数据
data_dict = {
    ‘name‘: [‘LUCY‘, ‘Marry‘, ‘tom‘, ‘杰克‘],
    ‘old_name‘: [‘张狗蛋‘, ‘王小三‘, ‘魏淑芬‘, ‘李铁牛‘],
    ‘salary‘: [9800.6, 8765.2, 17985.8, 35000.3],
    ‘age‘: [28, 29, 32, 50],
    ‘address‘: [‘三里屯‘, np.nan, np.nan, ‘昌平‘]
}
df = pd.DataFrame(data_dict)
```

### 1. replace函数：元素值替换

`replace()`函数用于替换DataFrame或Series中的值。主要参数是`to_replace`（要替换的值）和`value`（替换后的值）。

以下是几种常见的用法：

**单个值替换**

```python
# 将所有的空值NaN替换为100
df_filled = df.replace(np.nan, 100)
```

**多个值同时替换（使用列表）**

```python
# 将NaN替换为100，将‘三里屯‘替换为‘宇宙中心‘
df_replaced = df.replace([np.nan, ‘三里屯‘], [100, ‘宇宙中心‘])
```

**使用字典进行替换**

```python
# 使用字典指定替换关系
df_replaced_dict = df.replace({‘三里屯‘: ‘故宫‘, ‘昌平‘: ‘西二旗‘})
```

**指定列进行替换**

如果想只替换特定列中的某个值，需要使用嵌套字典。

```python
# 只将‘old_name‘列中的‘Marry‘替换为‘MARRY‘
df_col_specific = df.replace({‘old_name‘: {‘Marry‘: ‘MARRY‘}})
```

**使用正则表达式替换**

`replace()`支持正则表达式，需要通过`regex=True`参数启用。

```python
# 将name列中以‘LU‘开头的名字替换为‘孙悟空‘
df_regex = df.replace({‘name‘: r‘^LU.*‘}, {‘name‘: ‘孙悟空‘}, regex=True)
```

### 2. Series的method参数替换

对于Series对象，`replace()`还有一个`method`参数，可以用相邻的值（向前或向后填充）来替换目标值。这在处理有序数据（如时间序列）时很有用。

```python
# 获取address列
address_series = df[‘address‘].copy()

# 将第一个地址设为NaN以便演示
address_series.iloc[0] = np.nan

# 使用向后填充（bfill）的方式替换NaN
# limit=1表示只填充一次
filled_series = address_series.replace(to_replace=np.nan, method=‘bfill‘, limit=1)
```

> **注意**：`method`参数只能在Series对象上使用，在DataFrame上调用会报错。

### 3. map函数与rename函数

`map()`函数是Series的方法，用于根据输入的映射关系（函数或字典）将序列中的每个值替换为新值。它功能强大且常用，类似于Excel中的VLOOKUP。

`rename()`函数则主要用于修改索引或列标签的名称，特别是进行局部修改时非常方便。

由于课程视频本节重点在`replace`，`map`和`rename`的详细用法将在后续章节深入介绍。

## 练习：成绩表替换

以下是本节课的一个练习，我们将一张成绩表中的特定分数替换为零。

```python
# 创建成绩表
score_data = {
    ‘name‘: [‘张三‘, ‘李四‘],
    ‘Python‘: [150, 134],
    ‘Java‘: [139, 150],
    ‘C‘: [278, 300]
}
score_df = pd.DataFrame(score_data)

# 目标：将150分和300分替换为0分

# 方法一：使用列表
score_replaced_1 = score_df.replace([150, 300], [0, 0])

# 方法二：使用字典
score_replaced_2 = score_df.replace({150: 0, 300: 0})

print(“原始数据：“)
print(score_df)
print(“\n替换后数据（方法一）：“)
print(score_replaced_1)
print(“\n替换后数据（方法二）：“)
print(score_replaced_2)
```

## 总结

本节课中我们一起学习了Pandas中处理重复数据和进行值替换的核心技能。

我们掌握了：
*   **查询与删除重复值**：使用`duplicated()`和`drop_duplicates()`函数。
*   **灵活的值替换**：使用`replace()`函数进行单个、多个、按列、乃至使用正则表达式的复杂替换。
*   **Series的特殊填充**：了解了Series的`replace`方法中`method`参数的使用场景和限制。

![](img/d93dfdfaab43baa4a54d2004c8770b67_11.png)

这些操作是数据清洗的基石，能帮助我们将杂乱的数据整理得更加规整，为后续的分析工作打下坚实基础。下一节，我们将继续学习其他重要的数据处理技术。