# Python金融量化：P10：03 DataFrame大杀器之索引与切片 📊

![](img/a773f5568e56c97aa892bd81cf2277c7_0.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_2.png)

在本节课中，我们将要学习Pandas中DataFrame的核心操作——索引与切片。DataFrame是一个二维数据结构，虽然与NumPy数组同为二维，但其索引和切片的方式有很大不同。掌握这些操作是进行数据分析和处理的基础。

![](img/a773f5568e56c97aa892bd81cf2277c7_4.png)

## DataFrame的创建与基本概念

![](img/a773f5568e56c97aa892bd81cf2277c7_6.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_8.png)

首先，我们创建一个DataFrame来演示后续的操作。DataFrame可以由NumPy数组生成，并指定行索引和列索引。

![](img/a773f5568e56c97aa892bd81cf2277c7_10.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_12.png)

```python
import pandas as pd
import numpy as np

![](img/a773f5568e56c97aa892bd81cf2277c7_14.png)

# 创建一个8行4列的DataFrame，数据为60-100的随机整数
df = pd.DataFrame(
    np.random.randint(60, 100, size=(8, 4)),
    columns=['A', 'B', 'C', 'D']  # 指定显示的列索引
)
print(df)
```
在这个例子中，我们只指定了显示的列索引（A, B, C, D），行索引是隐式的（0到7）。

## DataFrame的索引操作

![](img/a773f5568e56c97aa892bd81cf2277c7_16.png)

索引操作指的是获取DataFrame中特定行、列或元素的值。DataFrame的索引机制因其二维结构和显示/隐式索引的存在而变得独特。

### 取单列与多列

![](img/a773f5568e56c97aa892bd81cf2277c7_18.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_20.png)

在DataFrame中，直接在中括号 `[]` 内放置索引，默认操作的是**列**。

![](img/a773f5568e56c97aa892bd81cf2277c7_22.png)

*   **取单列**：返回一个Series对象。
    ```python
    # 取列名为‘A’的单列
    series_a = df['A']
    print(series_a)
    print(type(series_a))  # 输出：<class 'pandas.core.series.Series'>
    ```
*   **取多列**：需要将多个列名放入一个列表中，返回一个新的DataFrame。
    ```python
    # 取列名为‘A’和‘C’的多列
    df_ac = df[['A', 'C']]
    print(df_ac)
    print(type(df_ac))  # 输出：<class 'pandas.core.frame.DataFrame'>
    ```
**重要提示**：如果DataFrame有**显示的列索引**，那么通过中括号取列时，**只能使用显示索引**（如‘A’），不能直接使用隐式索引（如数字0）。

![](img/a773f5568e56c97aa892bd81cf2277c7_24.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_26.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_28.png)

### 取单行与多行

![](img/a773f5568e56c97aa892bd81cf2277c7_30.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_32.png)

取行操作需要使用 `.loc[]` 或 `.iloc[]` 方法。

![](img/a773f5568e56c97aa892bd81cf2277c7_34.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_36.png)

*   **取单行**：
    ```python
    # 使用 .iloc 通过隐式行索引取第0行
    row_0_iloc = df.iloc[0]
    print(row_0_iloc)

    # 如果设置了显示的行索引，可以使用 .loc 通过显示行索引取行
    # df_with_index = df.set_index('A') # 假设将A列设为行索引
    # row_a_loc = df_with_index.loc[某个A列的值]
    ```
*   **取多行**：将多个行索引放入列表。
    ```python
    # 取第0, 3, 5行
    rows_multi = df.iloc[[0, 3, 5]]
    print(rows_multi)
    ```

![](img/a773f5568e56c97aa892bd81cf2277c7_38.png)

### `.loc` 与 `.iloc` 的区别

![](img/a773f5568e56c97aa892bd81cf2277c7_40.png)

这是DataFrame索引中的核心概念，务必分清：
*   **`.iloc[index]`**：`i` 代表 integer（整数）。它基于**隐式索引**（即行/列的位置，从0开始）进行选择。`df.iloc[0]` 选择第一行。
*   **`.loc[index]`**：基于**显示索引**（即行/列的名称标签）进行选择。`df.loc[‘row_label’]` 选择行标签为 ‘row_label’ 的行。

![](img/a773f5568e56c97aa892bd81cf2277c7_42.png)

### 取单个与多个元素

![](img/a773f5568e56c97aa892bd81cf2277c7_44.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_46.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_48.png)

要精确获取某个单元格的值，需要同时指定行和列。

![](img/a773f5568e56c97aa892bd81cf2277c7_50.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_52.png)

*   **取单个元素**：使用 `df.iloc[行索引, 列索引]` 或 `df.loc[行标签, 列标签]`。
    ```python
    # 使用隐式索引取第0行，第1列（即‘B’列）的元素
    value_iloc = df.iloc[0, 1]
    print(value_iloc)

    # 使用显示索引取第0行（假设行索引为0），‘B’列的元素
    value_loc = df.loc[0, 'B'] # 注意，此时行索引0是显示标签
    print(value_loc)
    ```
*   **取多个元素**：可以传递行和列索引的列表。
    ```python
    # 取第1,3,5行，第2列的元素
    values_multi = df.iloc[[1, 3, 5], 2]
    print(values_multi)
    ```

![](img/a773f5568e56c97aa892bd81cf2277c7_54.png)

## DataFrame的切片操作

![](img/a773f5568e56c97aa892bd81cf2277c7_56.png)

切片操作用于获取DataFrame中一个连续范围的数据。

![](img/a773f5568e56c97aa892bd81cf2277c7_58.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_60.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_62.png)

### 切行

![](img/a773f5568e56c97aa892bd81cf2277c7_64.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_66.png)

在DataFrame的中括号 `[]` 内直接使用切片语法，操作的是**行**。

![](img/a773f5568e56c97aa892bd81cf2277c7_68.png)

```python
# 切取前3行（隐式索引0:3）
slice_rows = df[0:3]
print(slice_rows)
```

![](img/a773f5568e56c97aa892bd81cf2277c7_70.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_72.png)

### 切列

![](img/a773f5568e56c97aa892bd81cf2277c7_74.png)

切列必须结合 `.iloc` 或 `.loc`，并通过逗号 `,` 分隔行和列，在列的位置使用切片。

![](img/a773f5568e56c97aa892bd81cf2277c7_76.png)

```python
# 使用 .iloc 切取前2列（隐式索引）
slice_cols_iloc = df.iloc[:, 0:2] # 逗号前`:`表示所有行
print(slice_cols_iloc)

![](img/a773f5568e56c97aa892bd81cf2277c7_78.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_80.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_82.png)

# 如果使用 .loc 切取显示列索引，例如从‘A’列到‘C’列
# slice_cols_loc = df.loc[:, 'A':'C']
```

![](img/a773f5568e56c97aa892bd81cf2277c7_84.png)

## 索引与切片总结 📝

![](img/a773f5568e56c97aa892bd81cf2277c7_86.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_88.png)

为了帮助记忆，以下是DataFrame索引与切片的核心规则总结：

![](img/a773f5568e56c97aa892bd81cf2277c7_90.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_92.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_94.png)

**索引操作：**
1.  **取列**：`df[‘col’]` (单列) 或 `df[[‘col1‘, ’col2‘]]` (多列)。
2.  **取行**：`df.iloc[row_index]` (隐式索引) 或 `df.loc[‘row_label’]` (显示索引)。
3.  **取元素**：`df.iloc[row_idx, col_idx]` 或 `df.loc[‘row_label’, ’col_label’]`。

![](img/a773f5568e56c97aa892bd81cf2277c7_96.png)

**切片操作：**
1.  **切行**：`df[start:stop]`。
2.  **切列**：`df.iloc[:, start:stop]` 或 `df.loc[:, ’start_label‘:’stop_label‘]`。

![](img/a773f5568e56c97aa892bd81cf2277c7_98.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_100.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_102.png)

**关键点**：即使为DataFrame或Series设置了显示索引，其隐式索引依然存在，可以通过 `.iloc` 访问。

![](img/a773f5568e56c97aa892bd81cf2277c7_104.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_106.png)

## DataFrame的运算

![](img/a773f5568e56c97aa892bd81cf2277c7_108.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_110.png)

DataFrame之间的运算规则与Series类似：**只有行列索引完全对齐的元素才会进行运算**。索引不一致的位置会产生`NaN`值。

![](img/a773f5568e56c97aa892bd81cf2277c7_112.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_114.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_116.png)

```python
df1 = pd.DataFrame(np.random.rand(3,2), columns=[‘A‘, ’B‘])
df2 = pd.DataFrame(np.random.rand(3,2), columns=[‘A‘, ’B‘])

![](img/a773f5568e56c97aa892bd81cf2277c7_118.png)

# 行列索引一致，可以对应元素相加
result_add = df1 + df2

![](img/a773f5568e56c97aa892bd81cf2277c7_120.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_122.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_124.png)

# 如果df2的列索引是[‘A‘, ’C‘]，则‘B‘和’C‘列无法对齐，结果为NaN
```

![](img/a773f5568e56c97aa892bd81cf2277c7_126.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_128.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_130.png)

## 实战练习：学生成绩处理 🏫

![](img/a773f5568e56c97aa892bd81cf2277c7_132.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_134.png)

让我们通过一个学生成绩处理的例子来巩固索引操作。

![](img/a773f5568e56c97aa892bd81cf2277c7_136.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_138.png)

```python
# 创建期中成绩表
期中 = pd.DataFrame({
    ‘张三‘: [150, 120, 130],
    ‘李四‘: [100, 110, 90],
    ‘王五‘: [140, 130, 120]
}, index=[‘语文‘, ’数学‘, ’英语‘])

![](img/a773f5568e56c97aa892bd81cf2277c7_140.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_142.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_144.png)

期末 = 期中.copy() # 假设期末成绩与期中相同

![](img/a773f5568e56c97aa892bd81cf2277c7_146.png)

# 1. 求期中期末平均值
平均成绩 = (期中 + 期末) / 2
print(“平均成绩：“)
print(平均成绩)

![](img/a773f5568e56c97aa892bd81cf2277c7_148.png)

# 2. 张三数学作弊，成绩记0分
期中.loc[‘数学‘, ’张三‘] = 0
print(“\n修改后期中成绩：“)
print(期中)

![](img/a773f5568e56c97aa892bd81cf2277c7_150.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_152.png)

# 3. 李四举报有功，所有成绩加10分
期中[‘李四‘] += 10 # 取‘李四‘列这个Series，整体加10
print(“\n奖励李四后期中成绩：“)
print(期中)

![](img/a773f5568e56c97aa892bd81cf2277c7_154.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_156.png)

# 4. 题目出错，所有人所有科目加5分
期中 += 5 # DataFrame整体运算
print(“\n全体加分后期中成绩：“)
print(期中)
```

![](img/a773f5568e56c97aa892bd81cf2277c7_158.png)

## 高级操作：时间序列处理 ⏰

![](img/a773f5568e56c97aa892bd81cf2277c7_160.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_162.png)

在金融数据分析中，处理时间数据至关重要。Pandas提供了便捷的时间类型转换和索引设置功能。

![](img/a773f5568e56c97aa892bd81cf2277c7_164.png)

```python
# 创建包含日期字符串的DataFrame
df_time = pd.DataFrame({
    ‘time‘: [‘2020-10-10‘, ‘2021-11-20‘, ‘2022-01-10‘],
    ‘temperature‘: [30, 33, 31]
})
print(“原始数据：“)
print(df_time)
print(df_time[‘time‘].dtype) # 输出：object (字符串)

![](img/a773f5568e56c97aa892bd81cf2277c7_166.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_168.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_170.png)

# 1. 将‘time‘列转换为时间序列类型
df_time[‘time‘] = pd.to_datetime(df_time[‘time‘])
print(“\n转换时间类型后：“)
print(df_time[‘time‘].dtype) # 输出：datetime64[ns]

![](img/a773f5568e56c97aa892bd81cf2277c7_172.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_174.png)

# 2. 将‘time‘列设置为行索引
df_time.set_index(‘time‘, inplace=True)
print(“\n设置时间索引后：“)
print(df_time)
# 现在行索引是时间，便于进行时间序列分析，如重采样等
```

![](img/a773f5568e56c97aa892bd81cf2277c7_176.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_178.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_180.png)

## 总结

![](img/a773f5568e56c97aa892bd81cf2277c7_182.png)

本节课中我们一起深入学习了DataFrame的索引与切片操作。我们了解到：

![](img/a773f5568e56c97aa892bd81cf2277c7_184.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_186.png)

1.  DataFrame通过中括号`[]`直接索引的是列，而切片操作的是行。
2.  取行或精确取元素需要借助`.loc`（基于显示标签）和`.iloc`（基于隐式整数位置）这两个核心方法。
3.  即使存在显示索引，隐式索引依然有效。
4.  DataFrame的运算要求索引对齐。
5.  我们通过学生成绩案例实战了数据修改，并学习了如何转换时间数据和设置时间索引，这些是金融量化分析中的常见任务。

![](img/a773f5568e56c97aa892bd81cf2277c7_188.png)

![](img/a773f5568e56c97aa892bd81cf2277c7_190.png)

这些操作是Pandas数据处理的基础，请务必熟练掌握。在下一节中，我们将通过更多实际案例来巩固这些技巧。