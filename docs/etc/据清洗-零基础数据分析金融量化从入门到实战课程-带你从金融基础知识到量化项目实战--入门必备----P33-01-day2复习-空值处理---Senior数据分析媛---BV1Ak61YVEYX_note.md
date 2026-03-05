# 数据分析与金融量化：P33：01 day2复习+空值处理 📊

在本节课中，我们将复习Pandas的核心数据结构Series和DataFrame，并重点学习如何处理数据中的空值。这是数据清洗的关键步骤，对于后续的金融量化分析至关重要。

## 复习：Series与DataFrame

上一节我们介绍了Pandas的基础，本节中我们来看看如何回顾和运用Series与DataFrame的核心操作。

### Series回顾

Series是一维带标签的数组。我们可以通过多种方式创建和操作它。

例如，生成一个包含随机整数的Series：
```python
import pandas as pd
import numpy as np
s = pd.Series(np.random.randint(0, 4, size=1000))
```

以下是Series常用的几个方法：
*   **`s.unique()`**：返回Series中所有不重复的值。
*   **`s.value_counts()`**：统计每个唯一值出现的次数。
*   **`s.head(n)`** 和 **`s.tail(n)`**：查看前n项或后n项数据，默认n=5。
*   **`s.sort_values()`** 和 **`s.sort_index()`**：根据值或索引进行排序。

### DataFrame回顾

DataFrame是Series的容器，用于处理二维表格数据。每一列是一个Series，拥有相同的数据类型；而每一行则可能包含不同类型的数据。

DataFrame有几个关键属性：
*   **`df.index`**：行索引。
*   **`df.columns`**：列索引（列名）。
*   **`df.values`**：以NumPy数组形式返回DataFrame的数据。
*   **`df.dtypes`**：查看每一列的数据类型。

构造DataFrame的常见方法有以下几种：
1.  使用字典：键成为列名，值（列表）成为该列的数据。
    ```python
    data = {'A': [1, 2, 3], 'B': [4, 5, 6]}
    df = pd.DataFrame(data)
    ```
2.  使用构造函数 `pd.DataFrame(data, index, columns)`。
3.  从文件读取，如 `pd.read_excel()` 或 `pd.read_csv()`，这是最常用的方法。

### 数据访问与切片

访问DataFrame中的数据需要区分显示索引（使用标签名）和隐式索引（使用整数位置）。

访问列数据最直接：
```python
df[‘列名‘] # 访问单列
df[[‘列名1‘, ‘列名2‘]] # 访问多列
```
访问行、元素或切片时，通常使用 `.loc`（显示索引）或 `.iloc`（隐式索引），遵循“先行后列”的原则。
```python
df.loc[‘行标签‘, ‘列标签‘] # 访问单个元素
df.iloc[0, 1] # 使用整数位置访问
```
**重要口诀**：直接使用中括号 `df[]` 时，**索引是列索引，切片是行切片**。

### 数据运算

DataFrame的运算需要注意索引对齐。
*   与单个Series运算：默认按**列索引**对齐。可以使用 `add()`, `sub()` 等函数，并通过 `axis` 参数控制对齐方向（0为列，1为行）。
*   DataFrame之间运算：默认行列索引都对齐。对齐不上的位置会产生空值（NaN）。可以使用 `fill_value` 参数预先填充这些可能产生的空值。

### 数据概览

在深入分析前，快速了解数据全貌非常重要。
*   **`df.describe()`**：生成数值型列的描述性统计报告，包括计数、均值、标准差、最小值、百分位数等。
*   **`df.info()`**：查看数据集的基本信息，包括行数、列数、每列的非空值数量及数据类型。

---

## 核心：空值（NaN）处理 🧹

在现实数据中，缺失值非常常见。正确处理空值是数据清洗的核心任务。

![](img/28cb5c6d482684e5afa4a1c174b71a7b_1.png)

![](img/28cb5c6d482684e5afa4a1c174b71a7b_2.png)

![](img/28cb5c6d482684e5afa4a1c174b71a7b_3.png)

### 认识空值

Pandas中的空值主要有两种来源：
1.  Python的 `None` 对象，类型为 `object`。
2.  NumPy的 `np.nan`（Not a Number），类型为 `float`。

Pandas在处理时会自动将 `None` 优化为 `np.nan` 以提高效率。但需要注意，在纯Python列表中不会进行这种转换。

### 检测空值

使用 `isna()` 或 `isnull()` 可以检测数据中的空值，返回一个布尔型的DataFrame或Series。
```python
df.isna()
```
结合聚合函数 `any()` 和 `all()`，可以快速定位存在空值的行或列。
```python
df.isna().any() # 检查哪些列存在空值
df.isna().all(axis=1) # 检查哪些行全部为空值
```

### 处理空值：删除

当空值较少或所在行/列不重要时，可以选择删除。使用 `dropna()` 方法。
*   **`axis`**：默认为0，删除包含空值的行；设置为1则删除包含空值的列。
*   **`how`**：默认为 `‘any‘`，只要存在空值就删除；设置为 `‘all‘` 则只有全部为空值时才删除。
```python
df.dropna() # 删除包含任何空值的行
df.dropna(axis=1, how=‘all‘) # 删除全部为空值的列
```

### 处理空值：填充

更常见的做法是使用合理的数据填充空值。使用 `fillna()` 方法。

**1. 统一值填充**
将所有空值替换为同一个值。
```python
df.fillna(100)
```

**2. 前向/后向填充**
用相邻的非空值进行填充。
*   `method=‘ffill‘` 或 `‘pad‘`：**前向填充**，用上面的值填充下面的空值（列方向），或用左边的值填充右边的空值（行方向）。
*   `method=‘bfill‘` 或 `‘backfill‘`：**后向填充**，用下面的值填充上面的空值，或用右边的值填充左边的空值。
可以通过 `axis` 参数控制填充方向。
```python
df.fillna(method=‘ffill‘, axis=0) # 列方向，用上一个非空值填充
```

**3. 统计值填充**
用数据的统计特征（如均值、中位数）进行填充，这是非常实用的方法。这通常需要先进行聚合计算。
```python
# 用每列的平均值填充该列的空值
column_means = df.mean()
df.fillna(column_means)
```
其原理是DataFrame与Series的运算：`df.fillna(column_means)` 会按列索引对齐，用 `column_means` 中对应列的平均值去填充 `df` 中该列的空值。

---

## 实战练习 ✍️

让我们通过一个例子巩固空值处理的知识。

**场景**：张三和李四参加了模拟考试。张三在英语考试中缺考，成绩记为NaN。

**任务**：
1.  创建包含“语文”、“数学”、“英语”三科成绩的DataFrame，索引为“张三”、“李四”。
2.  将张三的英语成绩设为NaN。
3.  老师决定用张三的**数学成绩**填充他的英语成绩（横向填充）。
4.  随后，老师改变主意，用李四的**英语成绩**填充张三的英语成绩（纵向填充）。

![](img/28cb5c6d482684e5afa4a1c174b71a7b_5.png)

![](img/28cb5c6d482684e5afa4a1c174b71a7b_7.png)

**代码实现**：
```python
import pandas as pd
import numpy as np

# 1. 创建DataFrame
np.random.seed(42) # 确保结果可重现
data = np.random.randint(0, 100, size=(2, 3))
df_scores = pd.DataFrame(data,
                         index=[‘张三‘, ‘李四‘],
                         columns=[‘语文‘, ‘数学‘, ‘英语‘])
print(“原始成绩：“)
print(df_scores)

# 2. 将张三的英语成绩设为NaN
df_scores.loc[‘张三‘, ‘英语‘] = np.nan
print(“\n张三英语缺考后：“)
print(df_scores)

# 3. 用张三的数学成绩填充英语成绩 (横向前向填充)
df_filled_math = df_scores.fillna(method=‘ffill‘, axis=1)
print(“\n用数学成绩填充张三英语后：“)
print(df_filled_math)

# 重置张三的英语成绩为NaN，进行下一步
df_scores.loc[‘张三‘, ‘英语‘] = np.nan

# 4. 用李四的英语成绩填充张三的英语成绩 (纵向后向填充)
df_filled_peer = df_scores.fillna(method=‘bfill‘, axis=0)
print(“\n用李四成绩填充张三英语后：“)
print(df_filled_peer)
```

---

![](img/28cb5c6d482684e5afa4a1c174b71a7b_9.png)

本节课中我们一起学习了Pandas中Series和DataFrame的核心操作复习，并深入探讨了数据清洗的关键环节——空值处理。我们掌握了如何检测、删除以及用多种策略（统一值、前后相邻值、统计值）填充空值。这些技能是构建干净、可靠数据集的基础，为后续的金融量化分析与建模做好了准备。