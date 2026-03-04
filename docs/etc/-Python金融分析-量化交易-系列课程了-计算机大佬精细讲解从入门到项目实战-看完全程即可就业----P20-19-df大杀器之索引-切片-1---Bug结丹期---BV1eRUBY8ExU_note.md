# Python金融分析+量化交易：P20：DataFrame大杀器之索引与切片 🎯

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_0.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_2.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_4.png)

在本节课中，我们将要学习Pandas中DataFrame的核心操作——索引与切片。DataFrame是一个二维数据结构，虽然与NumPy数组同为二维，但其索引和切片机制有显著不同。掌握这些操作是进行高效数据处理和分析的基础。

## 创建示例DataFrame

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_6.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_8.png)

首先，我们创建一个DataFrame作为后续操作的示例数据。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_10.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_12.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_14.png)

```python
import pandas as pd
import numpy as np

# 使用NumPy数组生成数据，并指定列索引
data = np.random.randint(60, 100, size=(8, 4))
df = pd.DataFrame(data, columns=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(df)
```

## DataFrame的索引操作

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_16.png)

上一节我们创建了DataFrame，本节中我们来看看如何从中提取数据。DataFrame的索引操作主要分为取列、取行和取单个元素。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_18.png)

### 取列操作

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_20.png)

在DataFrame中，直接在中括号 `[]` 内放置索引，默认是进行列操作。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_22.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_24.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_26.png)

以下是取列的具体方法：
*   **取单列**：`df[‘列名‘]`。这会返回一个Series对象。
    ```python
    # 取A列
    series_a = df[‘A‘]
    ```
*   **取多列**：`df[[‘列名1‘, ‘列名2‘]]`。这会返回一个新的DataFrame。
    ```python
    # 取A列和C列
    df_ac = df[[‘A‘, ‘C‘]]
    ```

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_28.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_30.png)

**注意事项**：如果DataFrame有**显示的列索引**，则必须使用显示索引名来取列，不能使用隐式的整数位置（如 `df[0]` 会报错）。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_32.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_34.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_36.png)

### 取行操作

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_38.png)

取行操作需要使用 `.loc[]` 或 `.iloc[]` 方法。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_40.png)

以下是取行的具体方法：
*   **取单行**：
    *   使用显示索引：`df.loc[‘行索引名‘]`
    *   使用隐式索引（整数位置）：`df.iloc[行位置]`
    ```python
    # 取第一行（隐式索引）
    row_0 = df.iloc[0]
    ```
*   **取多行**：
    ```python
    # 取第0、3、5行（隐式索引）
    rows = df.iloc[[0, 3, 5]]
    ```

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_42.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_44.png)

### `.loc` 与 `.iloc` 的区别

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_46.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_48.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_50.png)

这是两个核心概念，它们的区别在于：
*   **`.iloc[]`**：基于**隐式索引**（整数位置）进行选择。`i` 代表 integer。
    *   公式：`df.iloc[行位置, 列位置]`
*   **`.loc[]`**：基于**显示索引**（索引标签）进行选择。
    *   公式：`df.loc[行标签, 列标签]`

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_52.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_54.png)

### 取单个或多个元素

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_56.png)

结合行和列索引，可以精确提取特定位置的值。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_58.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_60.png)

以下是取元素的方法：
*   **取单个元素**：
    ```python
    # 使用.iloc取第0行第2列的元素（隐式索引）
    value_iloc = df.iloc[0, 2]
    # 使用.loc取名为‘A‘的列，第0行的元素（假设有显示行索引）
    # value_loc = df.loc[0, ‘A‘]
    ```
*   **取多个元素**：可以同时指定多个行位置和列位置。
    ```python
    # 取第1、3、5行，第2列的元素
    values = df.iloc[[1, 3, 5], 2]
    ```

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_62.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_64.png)

## DataFrame的切片操作

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_66.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_68.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_70.png)

切片操作用于获取数据的一个连续子集。DataFrame的切片规则也与NumPy不同。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_72.png)

### 行切片

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_74.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_76.png)

在DataFrame的中括号 `[]` 内直接使用切片语法，是对行进行切片。

```python
# 切片获取前3行数据
slice_rows = df[0:3]
```

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_78.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_80.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_82.png)

### 列切片

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_84.png)

对列进行切片必须结合 `.loc` 或 `.iloc`，并使用逗号分隔行和列，其中列部分使用切片。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_86.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_88.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_90.png)

```python
# 使用.iloc进行列切片：所有行，前2列
slice_cols_iloc = df.iloc[:, 0:2]
# 使用.loc进行列切片（需显示列名）：所有行，A列到C列
# slice_cols_loc = df.loc[:, ‘A‘:‘C‘]
```

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_92.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_94.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_96.png)

**关键总结**：`df[切片]` 切行；`df.iloc[:, 切片]` 或 `df.loc[:, 切片]` 切列。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_98.png)

## 索引与切片总结

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_100.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_102.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_104.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_106.png)

让我们对DataFrame的索引和切片机制做一个清晰的梳理：

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_108.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_110.png)

**索引总结**
*   **取列**：`df[‘col‘]` (单列) 或 `df[[‘col1‘, ‘col2‘]]` (多列)。
*   **取行**：`df.loc[‘label‘]` 或 `df.iloc[index]`。
*   **取元素**：`df.loc[‘row_label‘, ‘col_label‘]` 或 `df.iloc[row_idx, col_idx]`。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_112.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_114.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_116.png)

**切片总结**
*   **切行**：`df[start:stop:step]`
*   **切列**：`df.iloc[:, start:stop:step]` 或 `df.loc[:, ‘start_label‘:‘stop_label‘]`

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_118.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_120.png)

## 数据运算简介

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_122.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_124.png)

DataFrame之间的运算规则与Series类似：**索引对齐**。只有行索引和列索引都匹配的元素才会参与运算。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_126.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_128.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_130.png)

```python
# 假设df1和df2具有相同的行列索引
df_sum = df1 + df2
df_mean = (df1 + df2) / 2 # 计算平均值
```

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_132.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_134.png)

## 综合练习：学生成绩管理

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_136.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_138.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_140.png)

我们通过一个学生成绩管理的案例来巩固索引和切片的操作。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_142.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_144.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_146.png)

```python
# 创建期中、期末成绩DataFrame（这里用相同数据简化演示）
期中 = df.copy()
期末 = df.copy()

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_148.png)

# 1. 计算期中期末平均成绩
平均成绩 = (期中 + 期末) / 2

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_150.png)

# 2. 将‘张三‘的‘数学‘成绩改为0 (假设‘张三‘是行索引，‘数学‘是列索引)
# 期中.loc[‘张三‘, ‘数学‘] = 0

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_152.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_154.png)

# 3. 将‘李四‘的所有成绩加10分
# 期中.loc[‘李四‘] += 10

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_156.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_158.png)

# 4. 所有学生所有科目加5分（整个DataFrame运算）
期中 += 5
```

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_160.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_162.png)

## 常用补充操作

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_164.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_166.png)

### 转换时间序列

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_168.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_170.png)

在处理时间数据时，经常需要将字符串列转换为时间序列类型。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_172.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_174.png)

```python
# 创建一个包含时间字符串的DataFrame
df_time = pd.DataFrame({
    ‘time‘: [‘2020-10-10‘, ‘2021-11-20‘, ‘2022-01-10‘],
    ‘value‘: [30, 33, 31]
})
# 将‘time‘列转换为datetime类型
df_time[‘time‘] = pd.to_datetime(df_time[‘time‘])
print(df_time[‘time‘].dtype) # 检查类型是否已转换
```

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_176.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_178.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_180.png)

### 设置索引

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_182.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_184.png)

可以将DataFrame的某一列设置为其行索引，这对于时间序列分析尤其有用。

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_186.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_188.png)

```python
# 将‘time‘列设置为行索引，inplace=True表示直接修改原DataFrame
df_time.set_index(‘time‘, inplace=True)
print(df_time)
```

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_190.png)

![](img/3f0c4025eeb38fda262b0d3a1aa8322d_192.png)

本节课中我们一起学习了DataFrame强大的索引与切片操作。你需要牢记 `.loc`（基于标签）和 `.iloc`（基于整数位置）的核心区别，并熟练掌握取行、取列、取元素以及切片的各种写法。这些操作是后续进行数据筛选、清洗和分析的基石，请务必通过练习加以巩固。在下一小节，我们将通过更多实际案例来深化对这些操作的理解。