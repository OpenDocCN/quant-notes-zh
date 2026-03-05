# Python金融量化：P9：09 DataFrame大杀器之索引与切片

![](img/206f08fb84492594c7099aa39edf6644_0.png)

![](img/206f08fb84492594c7099aa39edf6644_2.png)

在本节课中，我们将要学习Pandas中DataFrame数据结构的索引与切片操作。DataFrame是一个二维表格型数据结构，虽然与NumPy数组同为二维，但其索引和切片机制有显著不同。掌握这些操作是进行数据分析和处理的基础。

![](img/206f08fb84492594c7099aa39edf6644_4.png)

## DataFrame的创建与基本结构

![](img/206f08fb84492594c7099aa39edf6644_6.png)

![](img/206f08fb84492594c7099aa39edf6644_8.png)

首先，我们创建一个DataFrame来演示后续操作。DataFrame可以由NumPy数组生成，并指定行索引和列索引。

![](img/206f08fb84492594c7099aa39edf6644_10.png)

![](img/206f08fb84492594c7099aa39edf6644_12.png)

```python
import pandas as pd
import numpy as np

![](img/206f08fb84492594c7099aa39edf6644_14.png)

# 创建一个8行4列的随机整数数组，数值范围在60到100之间
data = np.random.randint(60, 100, size=(8, 4))
# 创建DataFrame，并指定列索引为A, B, C, D
df = pd.DataFrame(data, columns=['A', 'B', 'C', 'D'])
print(df)
```

![](img/206f08fb84492594c7099aa39edf6644_16.png)

## DataFrame的索引操作

索引操作用于从DataFrame中选取特定的数据。DataFrame的索引分为列索引和行索引，选取方式有所不同。

![](img/206f08fb84492594c7099aa39edf6644_18.png)

### 选取列

在DataFrame中，直接在中括号 `[]` 内放置索引，默认是进行列选取。

![](img/206f08fb84492594c7099aa39edf6644_20.png)

*   **选取单列**：返回一个Series对象。
    ```python
    # 选取列名为‘A’的列
    series_a = df['A']
    print(series_a)
    ```
*   **选取多列**：需要将多个列名放入一个列表中，返回一个新的DataFrame。
    ```python
    # 选取列名为‘A’和‘C’的列
    df_ac = df[['A', 'C']]
    print(df_ac)
    ```

![](img/206f08fb84492594c7099aa39edf6644_22.png)

**注意事项**：如果DataFrame有显示的列索引（即我们指定的列名），那么通过索引机制选取列时，只能使用这些显示的列名，不能使用隐式的整数位置索引（如0, 1, 2...）。

![](img/206f08fb84492594c7099aa39edf6644_24.png)

![](img/206f08fb84492594c7099aa39edf6644_26.png)

![](img/206f08fb84492594c7099aa39edf6644_28.png)

### 选取行

![](img/206f08fb84492594c7099aa39edf6644_30.png)

![](img/206f08fb84492594c7099aa39edf6644_32.png)

选取行需要使用 `.loc[]` 或 `.iloc[]` 索引器。

![](img/206f08fb84492594c7099aa39edf6644_34.png)

![](img/206f08fb84492594c7099aa39edf6644_36.png)

![](img/206f08fb84492594c7099aa39edf6644_38.png)

*   **选取单行**：返回一个Series对象，其索引为列名。
    ```python
    # 使用.iloc通过隐式整数索引选取第0行
    row_0_iloc = df.iloc[0]
    print(row_0_iloc)
    ```
*   **选取多行**：将多个行索引放入一个列表中。
    ```python
    # 使用.iloc选取第0、3、5行
    rows_035 = df.iloc[[0, 3, 5]]
    print(rows_035)
    ```

![](img/206f08fb84492594c7099aa39edf6644_40.png)

### `.loc` 与 `.iloc` 的区别

![](img/206f08fb84492594c7099aa39edf6644_42.png)

这两个索引器的核心区别在于所接受的索引类型：

*   **`.iloc[]`**：`i` 代表 `integer`。它**只接受隐式的整数位置索引**（如0, 1, 2...），用于按数据在表格中的实际位置进行选取。
*   **`.loc[]`**：它**只接受显示的索引标签**（即我们为行或列指定的名称）。如果行没有指定显示索引，则默认的整数索引（0, 1, 2...）也被视为显示索引。

![](img/206f08fb84492594c7099aa39edf6644_44.png)

![](img/206f08fb84492594c7099aa39edf6644_46.png)

```python
# 假设我们为df指定了显示的行索引
df_with_index = df.copy()
df_with_index.index = ['row_1', 'row_2', 'row_3', 'row_4', 'row_5', 'row_6', 'row_7', 'row_8']

![](img/206f08fb84492594c7099aa39edf6644_48.png)

![](img/206f08fb84492594c7099aa39edf6644_50.png)

![](img/206f08fb84492594c7099aa39edf6644_52.png)

# 使用.loc通过显示的行索引标签选取
row_loc = df_with_index.loc['row_1']
print(row_loc)

![](img/206f08fb84492594c7099aa39edf6644_54.png)

![](img/206f08fb84492594c7099aa39edf6644_56.png)

# 使用.iloc通过隐式的整数位置索引选取（仍然是第一行）
row_iloc = df_with_index.iloc[0]
print(row_iloc)
```

![](img/206f08fb84492594c7099aa39edf6644_58.png)

### 选取特定元素（单元格）

![](img/206f08fb84492594c7099aa39edf6644_60.png)

要选取DataFrame中某个具体的值，需要同时指定行和列。

![](img/206f08fb84492594c7099aa39edf6644_62.png)

![](img/206f08fb84492594c7099aa39edf6644_64.png)

![](img/206f08fb84492594c7099aa39edf6644_66.png)

```python
# 使用.iloc选取第0行，第3列的元素（行列索引都从0开始）
value_iloc = df.iloc[0, 3]
print(value_iloc)

![](img/206f08fb84492594c7099aa39edf6644_68.png)

![](img/206f08fb84492594c7099aa39edf6644_70.png)

# 如果DataFrame有显示索引，使用.loc选取‘row_1’行，‘D’列的元素
value_loc = df_with_index.loc['row_1', 'D']
print(value_loc)

# 选取多个特定元素：第1、3、5行，第2列的值
values = df.iloc[[1, 3, 5], 2]
print(values)
```

![](img/206f08fb84492594c7099aa39edf6644_72.png)

![](img/206f08fb84492594c7099aa39edf6644_74.png)

上一节我们介绍了DataFrame的索引操作，本节中我们来看看如何进行切片。

![](img/206f08fb84492594c7099aa39edf6644_76.png)

## DataFrame的切片操作

切片操作用于选取一个连续范围的数据。

![](img/206f08fb84492594c7099aa39edf6644_78.png)

![](img/206f08fb84492594c7099aa39edf6644_80.png)

![](img/206f08fb84492594c7099aa39edf6644_82.png)

### 行切片

![](img/206f08fb84492594c7099aa39edf6644_84.png)

在DataFrame的中括号 `[]` 内直接使用切片语法，进行的是**行切片**。

![](img/206f08fb84492594c7099aa39edf6644_86.png)

![](img/206f08fb84492594c7099aa39edf6644_88.png)

```python
# 切片选取第0行到第2行（不包含第2行）
rows_slice = df[0:2]
print(rows_slice)
```

![](img/206f08fb84492594c7099aa39edf6644_90.png)

![](img/206f08fb84492594c7099aa39edf6644_92.png)

![](img/206f08fb84492594c7099aa39edf6644_94.png)

### 列切片

![](img/206f08fb84492594c7099aa39edf6644_96.png)

进行列切片必须结合 `.loc[]` 或 `.iloc[]` 索引器，并使用逗号 `,` 分隔行和列的位置。逗号左边是行切片（通常用 `:` 表示全选），右边是列切片。

![](img/206f08fb84492594c7099aa39edf6644_98.png)

![](img/206f08fb84492594c7099aa39edf6644_100.png)

```python
# 使用.iloc进行列切片：选取所有行，第0列到第2列（不包含第2列）
cols_slice_iloc = df.iloc[:, 0:2]
print(cols_slice_iloc)

![](img/206f08fb84492594c7099aa39edf6644_102.png)

![](img/206f08fb84492594c7099aa39edf6644_104.png)

![](img/206f08fb84492594c7099aa39edf6644_106.png)

# 如果使用.loc，且列有显示索引，可以按列名切片（包含结束列）
# 注意：基于标签的切片是包含结束点的
cols_slice_loc = df.loc[:, 'A':'C']
print(cols_slice_loc)
```

![](img/206f08fb84492594c7099aa39edf6644_108.png)

## 索引与切片总结

![](img/206f08fb84492594c7099aa39edf6644_110.png)

![](img/206f08fb84492594c7099aa39edf6644_112.png)

![](img/206f08fb84492594c7099aa39edf6644_114.png)

以下是DataFrame索引与切片核心操作的总结：

![](img/206f08fb84492594c7099aa39edf6644_116.png)

![](img/206f08fb84492594c7099aa39edf6644_118.png)

*   **索引取列**：
    *   单列：`df[‘列名’]`
    *   多列：`df[[‘列名1’， ‘列名2’]]`
*   **索引取行**：
    *   单行：`df.iloc[行位置]` 或 `df.loc[‘行标签’]`
    *   多行：`df.iloc[[行位置1， 行位置2]]` 或 `df.loc[[‘行标签1’， ‘行标签2’]]`
*   **索引取元素**：
    *   `df.iloc[行位置， 列位置]`
    *   `df.loc[‘行标签’， ‘列标签’]`
*   **切片取行**：
    *   `df[起始行位置：结束行位置]`
*   **切片取列**：
    *   `df.iloc[：， 起始列位置：结束列位置]`
    *   `df.loc[：， ‘起始列标签’：‘结束列标签’]`

![](img/206f08fb84492594c7099aa39edf6644_120.png)

![](img/206f08fb84492594c7099aa39edf6644_122.png)

## DataFrame的运算

![](img/206f08fb84492594c7099aa39edf6644_124.png)

![](img/206f08fb84492594c7099aa39edf6644_126.png)

DataFrame之间的运算规则与Series类似：**只有行列索引完全对齐的元素才会进行运算**。如果索引不一致，结果中会出现NaN（非数字）值。

![](img/206f08fb84492594c7099aa39edf6644_128.png)

![](img/206f08fb84492594c7099aa39edf6644_130.png)

![](img/206f08fb84492594c7099aa39edf6644_132.png)

```python
df1 = pd.DataFrame(np.random.rand(3, 3), columns=[‘A‘， ’B‘， ’C‘])
df2 = pd.DataFrame(np.random.rand(3, 3), columns=[‘A‘， ’B‘， ’C‘])

![](img/206f08fb84492594c7099aa39edf6644_134.png)

![](img/206f08fb84492594c7099aa39edf6644_136.png)

# 行列索引一致，可以对应元素相加
result_add = df1 + df2
print(result_add)

![](img/206f08fb84492594c7099aa39edf6644_138.png)

![](img/206f08fb84492594c7099aa39edf6644_140.png)

# 如果df2的列索引不同，则对应不上的位置结果为NaN
df2_diff_cols = pd.DataFrame(np.random.rand(3, 3), columns=[‘X‘， ’Y‘， ’Z‘])
result_add_nan = df1 + df2_diff_cols
print(result_add_nan)
```

![](img/206f08fb84492594c7099aa39edf6644_142.png)

![](img/206f08fb84492594c7099aa39edf6644_144.png)

![](img/206f08fb84492594c7099aa39edf6644_146.png)

## 综合练习：学生成绩处理

![](img/206f08fb84492594c7099aa39edf6644_148.png)

让我们通过一个学生成绩处理的例子来巩固索引和切片操作。

```python
# 创建一个期中考试成绩表
期中 = pd.DataFrame({
    ‘张三‘: [150, 120, 140],
    ‘李四‘: [90, 80, 70],
    ‘王五‘: [110, 115, 105]
}, index=[‘语文‘， ’数学‘， ’英语‘])
print(“期中成绩：“)
print(期中)

![](img/206f08fb84492594c7099aa39edf6644_150.png)

![](img/206f08fb84492594c7099aa39edf6644_152.png)

期末 = 期中.copy() # 假设期末成绩与期中相同，用于演示

![](img/206f08fb84492594c7099aa39edf6644_154.png)

![](img/206f08fb84492594c7099aa39edf6644_156.png)

# 1. 计算期中期末平均分
平均分 = (期中 + 期末) / 2
print(“\n平均分：“)
print(平均分)

![](img/206f08fb84492594c7099aa39edf6644_158.png)

![](img/206f08fb84492594c7099aa39edf6644_160.png)

# 2. 张三数学作弊，成绩记0分
期中.loc[‘数学‘， ’张三‘] = 0
print(“\n修改后期中成绩：“)
print(期中)

![](img/206f08fb84492594c7099aa39edf6644_162.png)

# 3. 李四举报有功，所有科目加10分
期中[‘李四‘] += 10
print(“\n奖励李四后期中成绩：“)
print(期中)

![](img/206f08fb84492594c7099aa39edf6644_164.png)

![](img/206f08fb84492594c7099aa39edf6644_166.png)

# 4. 题目出错，所有学生所有科目加5分
期中 += 5
print(“\n全体加分后期中成绩：“)
print(期中)
```

![](img/206f08fb84492594c7099aa39edf6644_168.png)

![](img/206f08fb84492594c7099aa39edf6644_170.png)

## 常用操作：时间序列转换与索引设置

![](img/206f08fb84492594c7099aa39edf6644_172.png)

![](img/206f08fb84492594c7099aa39edf6644_174.png)

在实际数据分析中，经常需要处理时间数据，并将其设置为索引。

![](img/206f08fb84492594c7099aa39edf6644_176.png)

![](img/206f08fb84492594c7099aa39edf6644_178.png)

```python
# 创建一个包含日期字符串的DataFrame
df_time = pd.DataFrame({
    ‘time‘: [‘2023-10-10‘， ’2023-11-20‘， ’2024-01-10‘],
    ‘temperature‘: [30, 31, 30]
})
print(“原始数据：“)
print(df_time)
print(“time列类型：“， df_time[‘time‘].dtype)

![](img/206f08fb84492594c7099aa39edf6644_180.png)

![](img/206f08fb84492594c7099aa39edf6644_182.png)

# 1. 将‘time‘列从字符串转换为时间序列类型
df_time[‘time‘] = pd.to_datetime(df_time[‘time‘])
print(“\n转换时间类型后：“)
print(df_time)
print(“time列新类型：“， df_time[‘time‘].dtype)

![](img/206f08fb84492594c7099aa39edf6644_184.png)

# 2. 将‘time‘列设置为DataFrame的行索引
df_time.set_index(‘time‘， inplace=True)
print(“\n设置time为索引后：“)
print(df_time)
```

![](img/206f08fb84492594c7099aa39edf6644_186.png)

![](img/206f08fb84492594c7099aa39edf6644_188.png)

## 总结

![](img/206f08fb84492594c7099aa39edf6644_190.png)

![](img/206f08fb84492594c7099aa39edf6644_192.png)

本节课中我们一起学习了Pandas DataFrame的核心数据选取方法——索引与切片。我们明确了直接使用 `[]` 是进行列操作，而行操作及更精细的元素选取需要借助 `.loc`（基于标签）和 `.iloc`（基于整数位置）这两个重要的索引器。我们还通过练习巩固了这些操作，并介绍了转换时间序列和设置索引两个实用技巧。DataFrame的索引机制是其强大功能的基础，熟练掌握后，你就能高效地访问和操作表格数据了。在接下来的课程中，我们将通过更多案例来深化对这些概念的理解。