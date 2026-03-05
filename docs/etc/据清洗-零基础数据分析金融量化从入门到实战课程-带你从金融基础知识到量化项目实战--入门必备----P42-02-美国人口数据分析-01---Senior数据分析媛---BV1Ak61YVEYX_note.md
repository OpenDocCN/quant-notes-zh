# 数据分析+金融量化+数据清洗：P42：02 美国人口数据分析-01

![](img/3c820777f20a4cb06cc29431018ac921_1.png)

![](img/3c820777f20a4cb06cc29431018ac921_3.png)

![](img/3c820777f20a4cb06cc29431018ac921_4.png)

## 概述
在本节课中，我们将学习如何对多张美国人口相关的数据集进行合并与数据清洗。我们将处理三张表格：州名缩写映射表、各州面积表和各州人口表。核心任务是合并数据，并处理合并过程中发现的缺失值问题，为后续计算人口密度做准备。

## 数据读取与初步查看
首先，我们需要导入必要的库并读取数据。以下是读取三张表格的代码：

```python
import numpy as np
import pandas as pd

# 读取州名与缩写映射表
abb = pd.read_csv('Data/state-abbrevs.csv')
# 读取各州面积表
area = pd.read_csv('Data/state-areas.csv')
# 读取各州人口表
pop = pd.read_csv('Data/state-population.csv')
```

数据读取后，我们需要了解其基本结构和内容。可以使用 `head()`、`describe()` 和 `info()` 函数进行初步探索。

以下是查看数据基本组成的示例：
```python
# 查看前几行数据
print(abb.head())
print(area.head())
print(pop.head())

# 查看数据摘要信息
print(abb.info())
print(area.info())
print(pop.info())
```

通过初步查看，我们了解到：
*   `abb` 表包含州名全称（`state`）和缩写（`abbreviation`）两列。
*   `area` 表包含州名全称（`state`）和面积（`area (sq. mi)`）两列。
*   `pop` 表包含州名缩写（`state/region`）、年份（`year`）和人口（`population`）三列。

## 数据合并的逻辑与问题发现
我们的最终目标是计算人口密度，这需要将人口数据与面积数据关联起来。然而，`pop` 表使用州名缩写，而 `area` 表使用州名全称。因此，我们需要借助 `abb` 映射表作为桥梁，分步合并数据。

在合并前，我们先检查各表中唯一州的数量，以发现潜在的数据不一致问题。

以下是检查各表唯一州数量的代码：
```python
# 检查映射表中的州数量
print(abb['abbreviation'].unique().size)
# 检查面积表中的州数量
print(area['state'].unique().size)
# 检查人口表中的州数量
print(pop['state/region'].unique().size)
```

检查发现：
*   映射表 (`abb`) 有 51 个唯一的州/地区。
*   面积表 (`area`) 有 52 个唯一的州/地区。
*   人口表 (`pop`) 有 53 个唯一的州/地区。

数据数量不一致，意味着直接合并可能导致信息丢失或错位。`abb` 表的记录最少，若以其为准进行内连接 (`inner join`)，`pop` 和 `area` 表中多出的记录将被丢弃。因此，我们应采用外连接 (`outer join`) 来优先保留数据量最大的表的信息。

## 首次合并与缺失值分析
由于 `pop` 表数据最多，我们首先将其与 `abb` 映射表进行外连接合并，并保留 `pop` 表中的州名缩写列。

以下是合并 `pop` 与 `abb` 表的代码：
```python
# 将人口表与缩写映射表进行外连接合并
pop_abb = pd.merge(pop, abb, how='outer', left_on='state/region', right_on='abbreviation')
# 删除合并后重复的缩写列
pop_abb = pop_abb.drop(labels='abbreviation', axis=1)
```

合并后，我们检查新表 `pop_abb` 中的缺失值。

以下是检查缺失值的代码：
```python
# 检查各列是否存在缺失值
print(pop_abb.isnull().any())
# 找出存在缺失值的具体行
null_rows = pop_abb.isnull().any(axis=1)
print(pop_abb.loc[null_rows])
```

分析发现两类缺失值：
1.  **州名全称 (`state`) 缺失**：例如 `PR` (波多黎各) 在 `abb` 映射表中没有对应记录，导致合并后其州名全称为空。
2.  **人口数据 (`population`) 缺失**：`PR` 在2000年以前的人口记录为空。

![](img/3c820777f20a4cb06cc29431018ac921_6.png)

![](img/3c820777f20a4cb06cc29431018ac921_8.png)

我们的处理原则是：能合理填充的数据就填充，无法填充的无效数据则删除。对于州名，我们可以从其他表（如 `area` 表）查找并填充；对于历史人口数据，由于无法凭空编造，应予以删除。

## 数据清洗：填充与删除
首先处理州名缺失问题。我们查到 `PR` 对应 “波多黎各”，`USA` 对应美国全国数据。我们将这些信息填充到 `pop_abb` 表中。

以下是填充州名缺失值的代码：
```python
# 填充PR（波多黎各）的州名全称
pr_condition = pop_abb['state/region'] == 'PR'
pop_abb.loc[pr_condition, 'state'] = '波多黎各'

# 填充USA（美国全国）的州名全称
usa_condition = pop_abb['state/region'] == 'USA'
pop_abb.loc[usa_condition, 'state'] = 'USA'
```

州名填充完成后，表中仅剩 `population` 列存在缺失值，即 `PR` 地区2000年以前的数据。这些数据无法填补，因此我们将其删除。

以下是删除人口缺失行的代码：
```python
# 找出人口数据为空的行的索引
drop_index = pop_abb[pop_abb['population'].isnull()].index
# 根据索引删除这些行
pop_abb = pop_abb.drop(labels=drop_index)

# 再次确认已无缺失值
print(pop_abb.isnull().any())
```

![](img/3c820777f20a4cb06cc29431018ac921_10.png)

## 总结
本节课中，我们一起学习了多表合并与数据清洗的初步流程。我们首先读取并查看了三张美国人口相关数据集的基本信息。接着，通过检查唯一值发现了数据不一致的问题，并确定了使用外连接合并的策略。然后，我们合并了人口表与州名映射表，并系统分析了合并后产生的缺失值。最后，我们根据“可填则填，不可填则删”的原则，对州名缺失值进行了填充，并删除了无效的历史人口数据记录，得到了一个清洗后的中间数据集 `pop_abb`。在下一节中，我们将继续将此表与面积表进行合并，并最终计算各州的人口密度。