# Python金融分析+量化交易：P19：DataFrame大杀器之索引与切片 📊

![](img/444daa011e5524c8d5772a075b01594e_0.png)

![](img/444daa011e5524c8d5772a075b01594e_2.png)

![](img/444daa011e5524c8d5772a075b01594e_4.png)

在本节课中，我们将要学习Pandas中DataFrame数据结构的核心操作——索引与切片。DataFrame是一个二维表格型数据结构，虽然与NumPy数组同为二维，但其索引和切片机制有显著不同。掌握这些操作是进行高效数据分析的基础。

![](img/444daa011e5524c8d5772a075b01594e_6.png)

## 创建示例DataFrame

![](img/444daa011e5524c8d5772a075b01594e_8.png)

![](img/444daa011e5524c8d5772a075b01594e_10.png)

首先，我们创建一个DataFrame作为后续操作的示例。我们使用NumPy生成随机整数数据，并指定列索引。

![](img/444daa011e5524c8d5772a075b01594e_12.png)

![](img/444daa011e5524c8d5772a075b01594e_14.png)

```python
import numpy as np
import pandas as pd

# 生成8行4列的随机整数数据（60-100）
data = np.random.randint(60, 100, size=(8, 4))
# 创建DataFrame，指定列索引为A, B, C, D
df = pd.DataFrame(data, columns=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(df)
```

## DataFrame的索引操作

![](img/444daa011e5524c8d5772a075b01594e_16.png)

上一节我们创建了DataFrame，本节中我们来看看如何从中提取数据。DataFrame的索引操作主要分为取列、取行和取单个元素。

![](img/444daa011e5524c8d5772a075b01594e_18.png)

![](img/444daa011e5524c8d5772a075b01594e_20.png)

### 取列操作

![](img/444daa011e5524c8d5772a075b01594e_22.png)

在DataFrame中，直接使用中括号 `[]` 并传入索引，默认是进行取列操作。

![](img/444daa011e5524c8d5772a075b01594e_24.png)

![](img/444daa011e5524c8d5772a075b01594e_26.png)

![](img/444daa011e5524c8d5772a075b01594e_28.png)

![](img/444daa011e5524c8d5772a075b01594e_30.png)

以下是取列操作的要点：
*   **取单列**：`df[‘列名‘]`。这会返回一个Series对象。
    ```python
    # 取A列
    series_a = df[‘A‘]
    ```
*   **取多列**：`df[[‘列名1‘, ‘列名2‘]]`。传入一个列名的列表，这会返回一个新的DataFrame。
    ```python
    # 取A列和C列
    df_ac = df[[‘A‘, ‘C‘]]
    ```
*   **注意事项**：如果DataFrame有**显示的列索引**，则在中括号内**只能使用显示索引**（列名），不能使用隐式的整数位置索引（如0, 1, 2）。

![](img/444daa011e5524c8d5772a075b01594e_32.png)

![](img/444daa011e5524c8d5772a075b01594e_34.png)

### 取行操作

![](img/444daa011e5524c8d5772a075b01594e_36.png)

![](img/444daa011e5524c8d5772a075b01594e_38.png)

取行操作需要借助 `.loc` 或 `.iloc` 索引器。

![](img/444daa011e5524c8d5772a075b01594e_40.png)

以下是 `.loc` 和 `.iloc` 的区别与用法：
*   **`.iloc`**：基于**隐式索引**（即整数位置，0, 1, 2...）进行索引。
    ```python
    # 取第0行（隐式索引）
    row_0 = df.iloc[0]
    # 取第0, 3, 5行
    rows_multi = df.iloc[[0, 3, 5]]
    ```
*   **`.loc`**：基于**显示索引**（即设定的行/列标签）进行索引。在我们的示例中，行索引是隐式的（0,1,2...），因此 `.loc` 在取行时也可以使用这些整数，但它们被视为标签。如果设定了自定义的行标签（如字符串），则必须使用 `.loc` 和对应的标签。
    ```python
    # 如果行索引是默认的，取第0行（此时0被视为标签）
    row_0_loc = df.loc[0]
    ```

### 取元素操作

![](img/444daa011e5524c8d5772a075b01594e_42.png)

![](img/444daa011e5524c8d5772a075b01594e_44.png)

![](img/444daa011e5524c8d5772a075b01594e_46.png)

![](img/444daa011e5524c8d5772a075b01594e_48.png)

取单个或多个元素需要同时指定行和列。

![](img/444daa011e5524c8d5772a075b01594e_50.png)

![](img/444daa011e5524c8d5772a075b01594e_52.png)

以下是取元素的方法：
*   **取单个元素**：`df.iloc[行位置, 列位置]` 或 `df.loc[行标签, 列标签]`。
    ```python
    # 使用隐式索引取第0行，第1列（B列）的元素
    value_iloc = df.iloc[0, 1]
    # 使用显示索引取第0行，’B‘列的元素（假设行索引为默认整数）
    value_loc = df.loc[0, ‘B‘]
    ```
*   **取多个元素**：可以传入行和列的位置/标签列表。
    ```python
    # 取第1,3,5行，第2列（C列）的元素
    values = df.iloc[[1, 3, 5], 2]
    ```

![](img/444daa011e5524c8d5772a075b01594e_54.png)

![](img/444daa011e5524c8d5772a075b01594e_56.png)

## DataFrame的切片操作

![](img/444daa011e5524c8d5772a075b01594e_58.png)

切片操作用于获取数据的连续子集。DataFrame的切片机制也因行列而异。

![](img/444daa011e5524c8d5772a075b01594e_60.png)

![](img/444daa011e5524c8d5772a075b01594e_62.png)

![](img/444daa011e5524c8d5772a075b01594e_64.png)

### 行切片

![](img/444daa011e5524c8d5772a075b01594e_66.png)

![](img/444daa011e5524c8d5772a075b01594e_68.png)

![](img/444daa011e5524c8d5772a075b01594e_70.png)

在DataFrame的中括号 `[]` 中直接使用切片语法，进行的是**行切片**。

![](img/444daa011e5524c8d5772a075b01594e_72.png)

```python
# 切片获取前3行数据（0, 1, 2行）
slice_rows = df[0:3]
```

![](img/444daa011e5524c8d5772a075b01594e_74.png)

![](img/444daa011e5524c8d5772a075b01594e_76.png)

### 列切片

列切片必须借助 `.iloc` 或 `.loc`，并通过逗号分隔行和列的切片范围。

![](img/444daa011e5524c8d5772a075b01594e_78.png)

![](img/444daa011e5524c8d5772a075b01594e_80.png)

![](img/444daa011e5524c8d5772a075b01594e_82.png)

```python
# 使用.iloc进行列切片：取所有行，前2列（0, 1列）
slice_cols_iloc = df.iloc[:, 0:2]
# 使用.loc进行列切片：取所有行，’A‘到’B‘列（按列名切片）
# 注意：按标签切片时，末端是包含的
slice_cols_loc = df.loc[:, ‘A‘:‘B‘]
```

![](img/444daa011e5524c8d5772a075b01594e_84.png)

![](img/444daa011e5524c8d5772a075b01594e_86.png)

**关键总结**：`df[切片]` 切行，`df.iloc/loc[:, 切片]` 切列。

![](img/444daa011e5524c8d5772a075b01594e_88.png)

![](img/444daa011e5524c8d5772a075b01594e_90.png)

![](img/444daa011e5524c8d5772a075b01594e_92.png)

## 索引与切片总结

![](img/444daa011e5524c8d5772a075b01594e_94.png)

![](img/444daa011e5524c8d5772a075b01594e_96.png)

让我们对DataFrame的索引和切片规则做一个清晰的总结：

![](img/444daa011e5524c8d5772a075b01594e_98.png)

![](img/444daa011e5524c8d5772a075b01594e_100.png)

*   **索引**：
    *   列：`df[‘col‘]` (单列)， `df[[‘col1‘, ‘col2‘]]` (多列)。
    *   行：`df.iloc[row_index]` (隐式索引)， `df.loc[row_label]` (显示索引)。
    *   元素：`df.iloc[row_idx, col_idx]` 或 `df.loc[row_label, col_label]`。
*   **切片**：
    *   行：`df[start:stop:step]`。
    *   列：`df.iloc[:, start:stop:step]` 或 `df.loc[:, ‘start_label‘:‘stop_label‘]`。

![](img/444daa011e5524c8d5772a075b01594e_102.png)

![](img/444daa011e5524c8d5772a075b01594e_104.png)

![](img/444daa011e5524c8d5772a075b01594e_106.png)

## DataFrame的运算

![](img/444daa011e5524c8d5772a075b01594e_108.png)

![](img/444daa011e5524c8d5772a075b01594e_110.png)

![](img/444daa011e5524c8d5772a075b01594e_112.png)

DataFrame之间的运算规则与Series类似：**索引对齐**。只有当两个DataFrame的行索引和列索引都匹配时，对应位置的元素才会进行运算，不匹配的位置会引入缺失值 `NaN`。

![](img/444daa011e5524c8d5772a075b01594e_114.png)

![](img/444daa011e5524c8d5772a075b01594e_116.png)

![](img/444daa011e5524c8d5772a075b01594e_118.png)

```python
df1 = pd.DataFrame({‘A‘: [1, 2], ‘B‘: [3, 4]})
df2 = pd.DataFrame({‘A‘: [5, 6], ‘B‘: [7, 8]})
# 对应位置元素相加
result = df1 + df2
```

![](img/444daa011e5524c8d5772a075b01594e_120.png)

## 实践练习：学生成绩管理

![](img/444daa011e5524c8d5772a075b01594e_122.png)

![](img/444daa011e5524c8d5772a075b01594e_124.png)

![](img/444daa011e5524c8d5772a075b01594e_126.png)

我们通过一个学生成绩管理的案例来巩固索引和修改操作。

![](img/444daa011e5524c8d5772a075b01594e_128.png)

![](img/444daa011e5524c8d5772a075b01594e_130.png)

![](img/444daa011e5524c8d5772a075b01594e_132.png)

```python
# 假设df是一个期中考试成绩表
mid_term = df.copy() # 复制一份作为期中成绩
final_term = df.copy() # 再复制一份作为期末成绩（示例用）

![](img/444daa011e5524c8d5772a075b01594e_134.png)

![](img/444daa011e5524c8d5772a075b01594e_136.png)

![](img/444daa011e5524c8d5772a075b01594e_138.png)

# 1. 计算期中期末平均分
average_score = (mid_term + final_term) / 2

![](img/444daa011e5524c8d5772a075b01594e_140.png)

![](img/444daa011e5524c8d5772a075b01594e_142.png)

![](img/444daa011e5524c8d5772a075b01594e_144.png)

# 2. 将“张三”的“数学”成绩改为0分 (假设‘张三‘是行标签，‘数学‘是列标签)
mid_term.loc[‘张三‘, ‘数学‘] = 0

![](img/444daa011e5524c8d5772a075b01594e_146.png)

![](img/444daa011e5524c8d5772a075b01594e_148.png)

# 3. 将“李四”的所有成绩加10分
mid_term.loc[‘李四‘] += 10

# 4. 所有学生的所有成绩加5分（安抚分数）
mid_term += 5
```

![](img/444daa011e5524c8d5772a075b01594e_150.png)

![](img/444daa011e5524c8d5772a075b01594e_152.png)

## 常用高级操作

![](img/444daa011e5524c8d5772a075b01594e_154.png)

![](img/444daa011e5524c8d5772a075b01594e_156.png)

![](img/444daa011e5524c8d5772a075b01594e_158.png)

最后，介绍两个数据处理中常用的高级操作。

![](img/444daa011e5524c8d5772a075b01594e_160.png)

### 转换时间序列类型

![](img/444daa011e5524c8d5772a075b01594e_162.png)

![](img/444daa011e5524c8d5772a075b01594e_164.png)

数据分析中经常需要处理时间数据，`pd.to_datetime()` 函数可以将字符串列转换为Pandas的日期时间类型。

![](img/444daa011e5524c8d5772a075b01594e_166.png)

![](img/444daa011e5524c8d5772a075b01594e_168.png)

```python
# 创建一个包含日期字符串的DataFrame
df_time = pd.DataFrame({
    ‘time‘: [‘2020-10-10‘, ‘2021-11-20‘, ‘2022-01-10‘],
    ‘value‘: [30, 33, 31]
})
# 将‘time‘列转换为datetime类型
df_time[‘time‘] = pd.to_datetime(df_time[‘time‘])
print(df_time[‘time‘].dtype) # 查看类型，应为datetime64[ns]
```

![](img/444daa011e5524c8d5772a075b01594e_170.png)

![](img/444daa011e5524c8d5772a075b01594e_172.png)

![](img/444daa011e5524c8d5772a075b01594e_174.png)

### 设置索引

![](img/444daa011e5524c8d5772a075b01594e_176.png)

![](img/444daa011e5524c8d5772a075b01594e_178.png)

![](img/444daa011e5524c8d5772a075b01594e_180.png)

`set_index()` 方法可以将DataFrame的某一列设置为新的行索引，这在时间序列分析中尤为有用。

![](img/444daa011e5524c8d5772a075b01594e_182.png)

```python
# 将‘time‘列设置为索引，inplace=True表示直接修改原DataFrame
df_time.set_index(‘time‘, inplace=True)
print(df_time)
# 现在，行索引变成了时间，原来的‘time‘列不再作为数据列显示
```

![](img/444daa011e5524c8d5772a075b01594e_184.png)

![](img/444daa011e5524c8d5772a075b01594e_186.png)

![](img/444daa011e5524c8d5772a075b01594e_188.png)

## 课程总结

![](img/444daa011e5524c8d5772a075b01594e_190.png)

![](img/444daa011e5524c8d5772a075b01594e_192.png)

本节课中我们一起深入学习了DataFrame的索引与切片，这是操作Pandas数据的核心技能。我们明确了取列、取行、取元素以及行列切片的不同语法，特别是 `.loc`（基于标签）和 `.iloc`（基于位置）的关键区别。此外，我们还了解了DataFrame的运算规则，并通过实战练习和高级操作（时间转换、设置索引）巩固了所学知识。记住，DataFrame的索引机制与NumPy数组不同，需要单独理解和熟练运用。在接下来的课程中，我们将利用这些知识进行更复杂的数据分析。