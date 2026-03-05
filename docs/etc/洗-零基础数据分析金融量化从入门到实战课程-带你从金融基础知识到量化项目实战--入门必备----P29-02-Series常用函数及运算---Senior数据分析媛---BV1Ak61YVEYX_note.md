# 数据分析+金融量化+数据清洗：P29：02 Series常用函数及运算 📊

在本节课中，我们将学习Pandas Series数据结构的一些常用函数和运算方法。我们将从Series的基本属性开始，逐步介绍数据查看、缺失值处理、排序、统计以及不同数据对象间的运算规则。掌握这些内容是进行高效数据分析的基础。

## Series的基本概念与属性

上一节我们介绍了如何创建Series。本节中，我们来看看Series对象本身具有哪些基本属性和概念。

首先，Series可以被理解为一个**定长的有序字典**。它结合了字典的键值对特性和数组的有序性。

我们可以通过几个属性来获取Series的基本信息。以下是获取Series属性的方法：

*   **`shape`**: 返回一个表示Series形状的元组。对于一维的Series，其形状为 `(长度,)`。
*   **`size`**: 返回Series中元素的总个数，是一个整数值。
*   **`index`**: 返回Series的索引对象，类型为 `pd.Index`。你可以像访问列表一样通过位置访问它，例如 `s.index[0]`。
*   **`values`**: 返回Series值构成的NumPy数组，类型为 `np.ndarray`。

**代码示例**:
```python
import pandas as pd
import numpy as np

s = pd.Series([85, 33, 11, 21], index=[‘语文‘, ‘数学‘, ‘英语‘, ‘物理‘])
print(‘形状:‘, s.shape)    # 输出: (4,)
print(‘元素个数:‘, s.size)  # 输出: 4
print(‘索引:‘, s.index)     # 输出: Index([‘语文‘, ‘数学‘, ‘英语‘, ‘物理‘], dtype=‘object‘)
print(‘值数组:‘, s.values)  # 输出: [85 33 11 21]
```

## 数据查看：head与tail

在初步了解数据时，我们常常只需要查看数据的前几行或后几行。Pandas提供了 `head()` 和 `tail()` 方法来实现这一目的。

`head(n)` 方法返回Series的前 `n` 个元素，`tail(n)` 方法返回后 `n` 个元素。参数 `n` 的默认值是5。以下是这两个方法的使用方式：

*   `s.head()`: 查看前5个数据。
*   `s.head(2)`: 查看前2个数据。
*   `s.tail()`: 查看后5个数据。
*   `s.tail(3)`: 查看后3个数据。

**代码示例**:
```python
print(s.head(2)) # 输出前两个数据
print(s.tail(1)) # 输出最后一个数据
```

## 缺失值处理

在数据处理中，我们经常会遇到数据缺失的情况。Pandas使用 `NaN` (Not a Number) 来表示缺失值。

当我们使用字典创建Series并指定了额外的、字典中不存在的索引时，Pandas会自动为这些索引位置填充 `NaN` 值。

**代码示例**:
```python
data = {‘语文‘: 85, ‘数学‘: 33, ‘英语‘: 11}
s_with_nan = pd.Series(data, index=[‘化学‘, ‘物理‘, ‘英语‘, ‘语文‘])
print(s_with_nan)
# 化学    NaN
# 物理    NaN
# 英语    11.0
# 语文    85.0
```

为了检测和处理这些缺失值，Pandas提供了两个重要的函数：

*   **`pd.isna()` / `Series.isna()`**: 检测数据是否为缺失值 (`NaN`)，返回一个布尔类型的Series。
*   **`pd.notna()` / `Series.notna()`**: 检测数据是否**不是**缺失值，是 `isna()` 的反向操作。

我们可以结合聚合函数 `any()` 或 `all()` 来检查整个Series中是否存在缺失值。以下是处理缺失值的常见操作：

*   `s.isna()`: 生成一个布尔Series，标记哪些位置是NaN。
*   `s.notna()`: 生成一个布尔Series，标记哪些位置不是NaN。
*   `s.isna().any()`: 如果Series中存在至少一个NaN，则返回 `True`。
*   `s[s.notna()]`: 使用布尔索引过滤掉所有的NaN值，只保留有效数据。

**代码示例**:
```python
print(‘是否存在空值:‘, s_with_nan.isna().any()) # 输出: True
print(‘过滤空值后的数据:‘, s_with_nan[s_with_nan.notna()])
```

## Series的name属性

Series对象有一个 `name` 属性，可以用来为这组数据添加一个标签或说明。这个属性在将Series转换为DataFrame的列时，会自动成为列名。

**代码示例**:
```python
s.name = ‘学生成绩‘
print(s.name) # 输出: ‘学生成绩‘
```

## 数据排序

Pandas提供了两种排序方式：按值排序和按索引排序。

*   **按值排序**: 使用 `sort_values()` 方法。参数 `ascending` 控制升序(`True`)或降序(`False`)。
*   **按索引排序**: 使用 `sort_index()` 方法。

这两个方法默认返回一个新的排序后的Series，而不改变原数据。如果希望直接在原数据上修改，可以设置参数 `inplace=True`，但需谨慎使用。

**代码示例**:
```python
s1 = pd.Series(np.random.randint(0, 100, 10))
s_sorted_by_value = s1.sort_values(ascending=False) # 按值降序排列
s_sorted_by_index = s_sorted_by_value.sort_index()   # 再按索引升序排列
print(‘按值降序:‘, s_sorted_by_value)
print(‘按索引排序:‘, s_sorted_by_index)
```

## 数值统计：value_counts

`value_counts()` 是一个非常有用的方法，它可以统计Series中每个唯一值出现的次数，默认按次数降序排列。这在分析类别分布（如男女比例、产品销量排名）时非常常用。

**代码示例**:
```python
# 模拟100个人的性别数据，0代表男，1代表女
sex_data = pd.Series(np.random.randint(0, 2, 100), name=‘sex‘)
gender_counts = sex_data.value_counts()
print(‘性别统计:‘)
print(gender_counts)
# 输出示例:
# 1    52
# 0    48
```

![](img/69eb4383f6c559591b59b649c3a03370_1.png)

## Series的运算规则

Series的运算在NumPy数组运算的基础上，引入了**索引对齐**的概念，这是其核心特性。

**1. Series与标量的运算**
Series与单个数字（标量）进行运算时，应用广播机制，Series中的每个元素都会与该标量进行运算。

![](img/69eb4383f6c559591b59b649c3a03370_3.png)

![](img/69eb4383f6c559591b59b649c3a03370_5.png)

**公式**: `Series + 标量` -> 每个元素 `s[i] + 标量`

![](img/69eb4383f6c559591b59b649c3a03370_7.png)

**代码示例**:
```python
print(s + 10) # 每个成绩加10分
```

![](img/69eb4383f6c559591b59b649c3a03370_9.png)

**2. Series与Series的运算（索引对齐）**
两个Series进行运算时，Pandas会先根据**索引进行对齐**，然后再对相同索引位置的值进行运算。
*   如果某个索引只在一个Series中出现，则运算结果中该索引对应的值为 `NaN`。
*   如果想在运算时用特定值（如0）填充缺失的索引，可以使用 `add()`, `sub()`, `mul()`, `div()` 等方法，并指定 `fill_value` 参数。

![](img/69eb4383f6c559591b59b649c3a03370_11.png)

**代码示例**:
```python
s1 = pd.Series([10, 20, 30], index=[‘A‘, ‘B‘, ‘C‘])
s2 = pd.Series([1, 2, 3], index=[‘B‘, ‘C‘, ‘D‘])

print(‘直接相加（索引对齐）:‘)
print(s1 + s2)
# A     NaN  (s2中没有A)
# B    21.0  (20+1)
# C    32.0  (30+2)
# D     NaN  (s1中没有D)

print(‘使用add方法填充0:‘)
print(s1.add(s2, fill_value=0))
# A    10.0  (10+0)
# B    21.0  (20+1)
# C    32.0  (30+2)
# D     3.0  (0+3)
```

**3. Series与NumPy数组的运算**
*   Series与**一维**NumPy数组运算时，会忽略Series的索引，直接按位置进行对应元素的运算（前提是长度相同）。
*   Series与**多维**NumPy数组运算时，可能会因形状不匹配而失败。此时，通常需要先通过 `.values` 属性将Series转换为NumPy数组，再按数组的广播规则进行运算。

**代码示例**:
```python
arr_1d = np.array([1, 2, 3, 4])
print(s.values + arr_1d) # 按位置相加，忽略索引

arr_2d = np.random.randint(0,10, (4, 2))
# print(s + arr_2d) # 可能报错
print(s.values.reshape(-1, 1) + arr_2d) # 转换为列向量后再广播相加
```

## 总结

![](img/69eb4383f6c559591b59b649c3a03370_13.png)

本节课中我们一起学习了Pandas Series的核心函数与运算。
我们首先回顾了Series的四个基本属性：`shape`, `size`, `index`, `values`。
接着，学习了如何使用 `head()` 和 `tail()` 快速查看数据。
在缺失值处理部分，我们掌握了 `isna()` 和 `notna()` 的用法，以及如何检测和过滤空值。
我们还了解了 `name` 属性的作用，以及通过 `sort_values()` 和 `sort_index()` 进行数据排序。
`value_counts()` 方法为我们提供了一种强大的工具来统计离散值的分布。
最后，我们深入探讨了Series的运算规则，特别是**索引对齐**这一关键概念，它区分了Series运算与普通数组运算的本质不同，并学习了Series与标量、Series以及NumPy数组进行运算时的不同行为。掌握这些知识将为后续更复杂的数据操作打下坚实基础。