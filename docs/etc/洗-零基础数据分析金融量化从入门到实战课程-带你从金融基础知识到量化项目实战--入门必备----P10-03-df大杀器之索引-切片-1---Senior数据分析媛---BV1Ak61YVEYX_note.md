# 数据分析+金融量化+数据清洗：P10：03 DataFrame大杀器之索引与切片 📊

![](img/59bcbc517d62732bb295ffbcf523dfb7_0.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_2.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_4.png)

在本节课中，我们将要学习Pandas中DataFrame的核心操作——索引与切片。DataFrame是一个二维数据结构，虽然与NumPy数组同为二维，但其索引和切片机制有很大不同。掌握这些操作是进行高效数据分析的基础。

![](img/59bcbc517d62732bb295ffbcf523dfb7_6.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_8.png)

## DataFrame的创建与基本结构

![](img/59bcbc517d62732bb295ffbcf523dfb7_10.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_12.png)

首先，我们来创建一个DataFrame作为示例数据。DataFrame可以由NumPy数组生成，并指定列索引。

![](img/59bcbc517d62732bb295ffbcf523dfb7_14.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_16.png)

```python
import pandas as pd
import numpy as np

# 创建一个8行4列的随机整数数组，数值范围在60-100之间
data = np.random.randint(60, 100, size=(8, 4))
# 创建DataFrame，并指定列索引为A, B, C, D
df = pd.DataFrame(data, columns=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(df)
```

![](img/59bcbc517d62732bb295ffbcf523dfb7_18.png)

## DataFrame的索引操作

上一节我们创建了DataFrame，本节中我们来看看如何从中提取数据。DataFrame的索引操作主要分为取列、取行和取单个元素。

![](img/59bcbc517d62732bb295ffbcf523dfb7_20.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_22.png)

### 取列操作

![](img/59bcbc517d62732bb295ffbcf523dfb7_24.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_26.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_28.png)

在DataFrame中，直接在中括号 `[]` 内放置索引，默认是进行**取列**操作。

![](img/59bcbc517d62732bb295ffbcf523dfb7_30.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_32.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_34.png)

*   **取单列**：返回一个Series对象。
    ```python
    # 取列名为‘A‘的列
    series_a = df[‘A‘]
    ```
*   **取多列**：需要将多个列名放入一个列表中，返回一个新的DataFrame。
    ```python
    # 取列名为‘A‘和‘C‘的列
    df_ac = df[[‘A‘, ‘C‘]]
    ```

![](img/59bcbc517d62732bb295ffbcf523dfb7_36.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_38.png)

**重要提示**：如果DataFrame有**显示的列索引**，则取列时只能使用这些显示索引，不能使用隐式的整数位置索引（如0, 1, 2...）。

![](img/59bcbc517d62732bb295ffbcf523dfb7_40.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_42.png)

### 取行操作

取行操作需要使用 `.loc[]` 或 `.iloc[]` 索引器。

![](img/59bcbc517d62732bb295ffbcf523dfb7_44.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_46.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_48.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_50.png)

*   **取单行**：
    ```python
    # 使用.iloc按隐式行索引（整数位置）取第0行
    row_0_iloc = df.iloc[0]
    # 使用.loc按显示行索引取行（本例中行索引为隐式，故与.iloc[0]结果相同）
    row_0_loc = df.loc[0]
    ```
*   **取多行**：将多个行索引放入列表即可。
    ```python
    # 取第0、3、5行
    rows_multi = df.iloc[[0, 3, 5]]
    ```

![](img/59bcbc517d62732bb295ffbcf523dfb7_52.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_54.png)

### `.loc` 与 `.iloc` 的区别

![](img/59bcbc517d62732bb295ffbcf523dfb7_56.png)

这是两个核心概念，必须明确区分：
*   **`.iloc[]`**：基于**隐式索引**（即整数位置，从0开始）进行选择。`i` 代表 `integer`。
    *   语法：`df.iloc[行位置, 列位置]`
*   **`.loc[]`**：基于**显示索引**（即自定义的索引标签）进行选择。
    *   语法：`df.loc[行标签, 列标签]`

![](img/59bcbc517d62732bb295ffbcf523dfb7_58.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_60.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_62.png)

**关键点**：即使为DataFrame设置了显示索引，隐式索引依然存在，可以通过 `.iloc` 访问。

![](img/59bcbc517d62732bb295ffbcf523dfb7_64.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_66.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_68.png)

### 取单个或多个元素

结合行和列索引，可以精确提取或修改某个值。

![](img/59bcbc517d62732bb295ffbcf523dfb7_70.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_72.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_74.png)

*   **取单个元素**：
    ```python
    # 使用.iloc取第0行第2列的元素（隐式索引）
    value_iloc = df.iloc[0, 2]
    # 使用.loc取第0行‘C‘列的元素（显示索引）
    value_loc = df.loc[0, ‘C‘]
    ```
*   **取多个元素**：可以同时指定多行和多列的位置/标签。
    ```python
    # 取第1、3、5行，第2列的元素
    values = df.iloc[[1, 3, 5], 2]
    ```

## DataFrame的切片操作

![](img/59bcbc517d62732bb295ffbcf523dfb7_76.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_78.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_80.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_82.png)

切片用于获取一个连续范围的数据。DataFrame的切片机制也因行列而异。

![](img/59bcbc517d62732bb295ffbcf523dfb7_84.png)

### 行切片

![](img/59bcbc517d62732bb295ffbcf523dfb7_86.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_88.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_90.png)

在DataFrame的中括号 `[]` 内直接使用切片语法，进行的是**行切片**。

![](img/59bcbc517d62732bb295ffbcf523dfb7_92.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_94.png)

```python
# 切片获取前3行数据（0, 1, 2行）
df_slice_rows = df[0:3]
```

![](img/59bcbc517d62732bb295ffbcf523dfb7_96.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_98.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_100.png)

### 列切片

![](img/59bcbc517d62732bb295ffbcf523dfb7_102.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_104.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_106.png)

列切片必须借助 `.loc` 或 `.iloc` 索引器，并通过逗号 `,` 分隔行和列，在列的位置使用切片。

![](img/59bcbc517d62732bb295ffbcf523dfb7_108.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_110.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_112.png)

```python
# 使用.iloc进行列切片：取所有行，前2列（0, 1列）
df_slice_cols_iloc = df.iloc[:, 0:2]
# 使用.loc进行列切片：取所有行，‘A‘到‘B‘列（按列标签切片）
df_slice_cols_loc = df.loc[:, ‘A‘:‘B‘]
```

![](img/59bcbc517d62732bb295ffbcf523dfb7_114.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_116.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_118.png)

## 索引与切片总结 📝

![](img/59bcbc517d62732bb295ffbcf523dfb7_120.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_122.png)

以下是DataFrame索引与切片的核心方法总结：

![](img/59bcbc517d62732bb295ffbcf523dfb7_124.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_126.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_128.png)

**索引操作：**
*   **取列**：`df[‘列名‘]` 或 `df[[‘列名1‘, ‘列名2‘]]`
*   **取行**：`df.loc[行标签]` 或 `df.iloc[行位置]`
*   **取元素**：`df.loc[行标签, 列标签]` 或 `df.iloc[行位置, 列位置]`

![](img/59bcbc517d62732bb295ffbcf523dfb7_130.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_132.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_134.png)

**切片操作：**
*   **切行**：`df[起始行:结束行]`
*   **切列**：`df.loc[:, 起始列:结束列]` 或 `df.iloc[:, 起始位置:结束位置]`

![](img/59bcbc517d62732bb295ffbcf523dfb7_136.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_138.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_140.png)

## 实战练习：学生成绩管理

![](img/59bcbc517d62732bb295ffbcf523dfb7_142.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_144.png)

让我们通过一个学生成绩管理的例子来巩固所学操作。

![](img/59bcbc517d62732bb295ffbcf523dfb7_146.png)

```python
# 创建期中考试成绩表
期中 = pd.DataFrame({
    ‘张三‘: [150, 120, 130],
    ‘李四‘: [100, 110, 90],
    ‘王五‘: [140, 130, 150]
}, index=[‘语文‘, ‘数学‘, ‘英语‘])
期末 = 期中.copy() # 假设期末成绩与期中相同

![](img/59bcbc517d62732bb295ffbcf523dfb7_148.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_150.png)

# 1. 计算期中期末平均分
平均分 = (期中 + 期末) / 2

![](img/59bcbc517d62732bb295ffbcf523dfb7_152.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_154.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_156.png)

# 2. 张三数学作弊，成绩记0分
期中.loc[‘数学‘, ‘张三‘] = 0

![](img/59bcbc517d62732bb295ffbcf523dfb7_158.png)

# 3. 李四举报有功，所有科目加100分
期中[‘李四‘] += 100

![](img/59bcbc517d62732bb295ffbcf523dfb7_160.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_162.png)

# 4. 题目出错，所有学生所有科目加10分
期中 += 10
```

![](img/59bcbc517d62732bb295ffbcf523dfb7_164.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_166.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_168.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_170.png)

## 扩展知识：时间序列处理

![](img/59bcbc517d62732bb295ffbcf523dfb7_172.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_174.png)

在金融数据分析中，处理时间数据非常常见。以下是两个实用操作：

![](img/59bcbc517d62732bb295ffbcf523dfb7_176.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_178.png)

1.  **将字符串列转换为时间序列类型**
    ```python
    df = pd.DataFrame({‘time‘: [‘2020-10-10‘, ‘2021-11-20‘, ‘2022-01-10‘], ‘value‘: [30, 31, 30]})
    df[‘time‘] = pd.to_datetime(df[‘time‘]) # 转换数据类型
    print(df[‘time‘].dtype) # 查看类型，应为datetime64[ns]
    ```

![](img/59bcbc517d62732bb295ffbcf523dfb7_180.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_182.png)

2.  **将某一列设置为行索引**
    ```python
    df.set_index(‘time‘, inplace=True) # 将‘time‘列设为索引，并替换原df
    print(df)
    ```

![](img/59bcbc517d62732bb295ffbcf523dfb7_184.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_186.png)

![](img/59bcbc517d62732bb295ffbcf523dfb7_188.png)

本节课中我们一起学习了DataFrame强大的索引与切片操作，包括使用 `[]`、`.loc` 和 `.iloc` 进行行列及元素的提取，以及如何进行切片。这些是操作和清洗数据的基石，务必熟练掌握。在下一小节，我们将通过更多案例来巩固这些技巧。