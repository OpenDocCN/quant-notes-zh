# Python金融量化投资分析与股票交易：P25：DataFrame索引和切片 📊

在本节课中，我们将要学习如何从Pandas的DataFrame对象中获取数据。DataFrame是一个二维表格型数据结构，拥有行索引和列索引。我们将重点介绍如何使用`.loc`和`.iloc`属性进行精确、安全的索引和切片操作，避免直接使用中括号可能带来的混淆。

上一节我们介绍了DataFrame的一些常用属性，本节中我们来看看如何获取DataFrame中的具体数值。

## 索引基础：`.loc`与`.iloc`

与Series类似，DataFrame也可以通过中括号`[]`获取值。例如，想获取列`‘one’`和行`‘A’`交叉处的值`1.0`。在Pandas中，按标签索引的习惯是**先指定列，再指定行**，这与数据库查询`SELECT`语句先选列的逻辑相似。

**代码示例：**
```python
# 直接使用中括号（不推荐，易混淆）
value = df['one']['A']  # 先列后行
```

然而，如果行索引也是整数，这种方式容易产生歧义。因此，更推荐使用`.loc`（基于标签）或`.iloc`（基于整数位置）属性，并通过逗号分隔行和列索引，格式为`[行, 列]`。

**更推荐的写法：**
```python
# 使用 .loc 通过标签索引
value = df.loc['A', 'one']

# 使用 .iloc 通过位置索引（行A是第0行，列one是第0列）
value = df.iloc[0, 0]
```
这种方法明确了索引方式，且遵循了**先行后列**的直观顺序。

## 访问整行或整列数据

如果想获取某一整行或整列的数据，需要避免连续使用两个中括号`[][]`，因为DataFrame是由多个Series组合而成，连续索引可能导致意外错误或无法访问。

以下是访问整行和整列的正确方法：

![](img/c1fdc1d35c2830971b55db36efae0f5a_1.png)

**访问整行数据（例如行‘A’）：**
```python
# 使用 .loc，列部分用切片 `:` 表示选择所有列
row_data = df.loc['A', :]
# 或者省略列部分，默认选择所有列
row_data = df.loc['A']
```

![](img/c1fdc1d35c2830971b55db36efae0f5a_3.png)

**访问整列数据（例如列‘one’）：**
```python
# 直接使用中括号选择列是允许的，返回一个Series
column_data = df['one']
```

![](img/c1fdc1d35c2830971b55db36efae0f5a_5.png)

## 灵活的切片与索引组合

DataFrame的索引功能非常强大，允许在行和列部分混合使用标签、位置切片、布尔索引和花式索引。

![](img/c1fdc1d35c2830971b55db36efae0f5a_7.png)

**代码示例：**
```python
import pandas as pd
import numpy as np

![](img/c1fdc1d35c2830971b55db36efae0f5a_8.png)

# 假设有如下DataFrame
df = pd.DataFrame(np.arange(12).reshape(4,3),
                  index=['A', 'B', 'C', 'D'],
                  columns=['one', 'two', 'three'])

# 1. 花式索引：选择指定的行（‘A’和‘C’）和指定的列（‘two’）
subset = df.loc[['A', 'C'], 'two']

![](img/c1fdc1d35c2830971b55db36efae0f5a_10.png)

# 2. 切片与花式索引组合：选择行‘A’到‘C’，以及列‘one’和‘three’
subset2 = df.loc['A':'C', ['one', 'three']]

![](img/c1fdc1d35c2830971b55db36efae0f5a_12.png)

# 3. 布尔索引：选择‘one’列大于2的所有行
bool_subset = df.loc[df['one'] > 2, :]
```

以下是`.loc`和`.iloc`支持的主要索引类型：
*   **标签/位置切片**：如 `‘A’:‘C’` 或 `0:2`
*   **标签列表（花式索引）**：如 `[‘A‘, ’C‘]`
*   **布尔数组**：如 `df[‘one‘] > 2`
*   **单个标量**：如 `‘A‘` 或 `0`

这些方法可以自由组合，实现灵活的数据筛选。

![](img/c1fdc1d35c2830971b55db36efae0f5a_14.png)

## 总结

![](img/c1fdc1d35c2830971b55db36efae0f5a_16.png)

本节课中我们一起学习了DataFrame的核心数据获取方法。关键在于区分并使用`.loc`（基于标签）和`.iloc`（基于整数位置）属性，通过`[行索引, 列索引]`的格式进行访问。我们介绍了如何安全地获取单个值、整行或整列数据，并展示了混合使用切片、花式索引和布尔索引的强大功能。掌握这些索引技巧是高效进行金融数据清洗与分析的基础。