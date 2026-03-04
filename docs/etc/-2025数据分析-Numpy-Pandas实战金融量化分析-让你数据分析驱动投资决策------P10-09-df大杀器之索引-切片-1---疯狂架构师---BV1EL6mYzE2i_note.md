# 数据分析实战：P10：DataFrame的索引与切片操作 📊🔍

![](img/ae0e30edaaeb3b24f057beb681e65bc7_0.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_2.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_4.png)

在本节课中，我们将学习Pandas中DataFrame数据结构的核心操作之一：索引与切片。DataFrame是一个二维表格型数据结构，虽然与NumPy数组同为二维，但其索引和切片机制有显著不同。掌握这些操作是高效进行数据选取和子集分析的基础。

![](img/ae0e30edaaeb3b24f057beb681e65bc7_6.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_8.png)

## DataFrame的索引操作

![](img/ae0e30edaaeb3b24f057beb681e65bc7_10.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_12.png)

上一节我们介绍了DataFrame的基本概念，本节中我们来看看如何从DataFrame中选取数据。首先，我们需要创建一个DataFrame作为示例。

![](img/ae0e30edaaeb3b24f057beb681e65bc7_14.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_16.png)

```python
import pandas as pd
import numpy as np

# 创建一个8行4列的DataFrame，数据为60到100之间的随机整数
np.random.seed(42) # 固定随机种子以便复现
data = np.random.randint(60, 100, size=(8, 4))
df = pd.DataFrame(data, columns=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(df)
```

![](img/ae0e30edaaeb3b24f057beb681e65bc7_18.png)

在DataFrame中，直接使用中括号 `[]` 进行索引时，默认操作的是列。

### 选取单列与多列

![](img/ae0e30edaaeb3b24f057beb681e65bc7_20.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_22.png)

以下是选取列的操作方法：

![](img/ae0e30edaaeb3b24f057beb681e65bc7_24.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_26.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_28.png)

*   **选取单列**：传入列名（显示索引），返回一个Series对象。
    ```python
    series_a = df[‘A‘]  # 选取A列
    ```
*   **选取多列**：传入一个包含多个列名的列表，返回一个新的DataFrame。
    ```python
    df_ac = df[[‘A‘, ‘C‘]]  # 选取A列和C列
    ```

![](img/ae0e30edaaeb3b24f057beb681e65bc7_30.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_32.png)

**注意**：如果DataFrame有显示的列索引，则必须使用显示索引（列名）来选取列，不能使用隐式的整数位置索引（如 `df[0]` 会报错）。

![](img/ae0e30edaaeb3b24f057beb681e65bc7_34.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_36.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_38.png)

### 选取单行与多行

![](img/ae0e30edaaeb3b24f057beb681e65bc7_40.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_42.png)

选取行需要借助 `.loc` 或 `.iloc` 索引器。

以下是选取行的操作方法：

![](img/ae0e30edaaeb3b24f057beb681e65bc7_44.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_46.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_48.png)

*   **选取单行**：使用 `.iloc[行位置]` 或 `.loc[行索引]`。
    ```python
    row_0_iloc = df.iloc[0]  # 使用隐式索引选取第0行
    # 如果设置了显示的行索引名，例如 df.index = [‘row1‘, ‘row2‘, ...]
    # row_0_loc = df.loc[‘row1‘]  # 使用显示索引选取名为‘row1‘的行
    ```
*   **选取多行**：传入一个索引列表。
    ```python
    rows_035 = df.iloc[[0, 3, 5]]  # 选取第0、3、5行
    ```

![](img/ae0e30edaaeb3b24f057beb681e65bc7_50.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_52.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_54.png)

### `.loc` 与 `.iloc` 的区别

![](img/ae0e30edaaeb3b24f057beb681e65bc7_56.png)

这两个索引器的核心区别在于所使用的索引类型：

![](img/ae0e30edaaeb3b24f057beb681e65bc7_58.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_60.png)

*   **`.iloc[index]`**：`i` 代表 integer（整数）。它基于**隐式索引**，即数据的整数位置（0, 1, 2…）进行选取。
*   **`.loc[index]`**：基于**显示索引**，即用户自定义的索引标签（如行名、列名）进行选取。

![](img/ae0e30edaaeb3b24f057beb681e65bc7_62.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_64.png)

### 选取特定元素（单元格）

![](img/ae0e30edaaeb3b24f057beb681e65bc7_66.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_68.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_70.png)

要选取单个或多个特定单元格的值，需要同时指定行和列。

![](img/ae0e30edaaeb3b24f057beb681e65bc7_72.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_74.png)

以下是选取元素的操作方法：

![](img/ae0e30edaaeb3b24f057beb681e65bc7_76.png)

*   **选取单个元素**：使用 `df.iloc[行位置, 列位置]` 或 `df.loc[行索引, 列索引]`。
    ```python
    value_iloc = df.iloc[0, 2]  # 选取第0行，第2列（C列）的值
    value_loc = df.loc[0, ‘C‘]   # 选取索引为0的行，‘C‘列的值（假设行索引为默认的0,1,2...）
    ```
*   **选取多个元素**：可以同时传入行和列的列表。
    ```python
    values = df.iloc[[1, 3, 5], [2]]  # 选取第1,3,5行，第2列的值
    ```

![](img/ae0e30edaaeb3b24f057beb681e65bc7_78.png)

## DataFrame的切片操作

![](img/ae0e30edaaeb3b24f057beb681e65bc7_80.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_82.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_84.png)

切片操作用于获取数据的连续子集。DataFrame的切片规则也因行列而异。

![](img/ae0e30edaaeb3b24f057beb681e65bc7_86.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_88.png)

### 行切片与列切片

![](img/ae0e30edaaeb3b24f057beb681e65bc7_90.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_92.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_94.png)

以下是进行切片的方法：

![](img/ae0e30edaaeb3b24f057beb681e65bc7_96.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_98.png)

*   **对行进行切片**：在DataFrame的中括号 `[]` 中直接使用切片语法，操作的是行。
    ```python
    rows_slice = df[0:3]  # 切片选取第0行到第2行（不包含第3行）
    ```
*   **对列进行切片**：必须结合 `.iloc` 或 `.loc`，并通过逗号分隔行和列，在列的位置使用切片。
    ```python
    # 使用隐式索引切片列
    cols_slice_iloc = df.iloc[:, 0:2]  # 选取所有行，第0列到第1列（A列和B列）
    # 使用显示索引切片列 (如果列名是连续的，例如 ‘A‘, ‘B‘, ‘C‘)
    # cols_slice_loc = df.loc[:, ‘A‘:‘C‘]  # 选取所有行，A列到C列
    ```

![](img/ae0e30edaaeb3b24f057beb681e65bc7_100.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_102.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_104.png)

**关键点总结**：`df[切片]` 切行，`df.iloc[:, 切片]` 或 `df.loc[:, 切片]` 切列。

![](img/ae0e30edaaeb3b24f057beb681e65bc7_106.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_108.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_110.png)

## 索引与切片总结与注意事项

![](img/ae0e30edaaeb3b24f057beb681e65bc7_112.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_114.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_116.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_118.png)

本节课中我们一起学习了DataFrame索引与切片的核心机制。我们来总结一下关键点：

![](img/ae0e30edaaeb3b24f057beb681e65bc7_120.png)

1.  **索引优先列**：`df[‘col‘]` 或 `df[[‘col1‘, ‘col2‘]]` 用于选取列。
2.  **取行需索引器**：选取行必须使用 `.loc`（基于标签）或 `.iloc`（基于整数位置）。
3.  **切片有区别**：直接切片 `df[m:n]` 针对行；列切片需在 `.iloc`/`.loc` 中指定，如 `df.iloc[:, m:n]`。
4.  **显示与隐式共存**：即使为DataFrame设置了显示索引（自定义的行/列名），隐式的整数位置索引依然有效，可通过 `.iloc` 访问。
5.  **与NumPy不同**：DataFrame的索引切片逻辑与NumPy数组不同，需要分开记忆和练习。

![](img/ae0e30edaaeb3b24f057beb681e65bc7_122.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_124.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_126.png)

## 实战练习：学生成绩管理

![](img/ae0e30edaaeb3b24f057beb681e65bc7_128.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_130.png)

为了巩固所学，我们通过一个学生成绩管理的案例来实践。

![](img/ae0e30edaaeb3b24f057beb681e65bc7_132.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_134.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_136.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_138.png)

```python
# 创建期中、期末成绩DataFrame（此处用相同数据简化演示）
期中 = pd.DataFrame({
    ‘张三‘: [150, 120, 140],
    ‘李四‘: [0, 110, 95],
    ‘王五‘: [120, 130, 125]
}, index=[‘语文‘, ‘数学‘, ‘英语‘])

![](img/ae0e30edaaeb3b24f057beb681e65bc7_140.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_142.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_144.png)

期末 = 期中.copy() # 假设期末成绩与期中相同
print(“期中成绩：“)
print(期中)
print(“\n期末成绩：“)
print(期末)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_146.png)

# 1. 计算期中期末平均分
平均分 = (期中 + 期末) / 2
print(“\n平均分：“)
print(平均分)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_148.png)

# 2. 张三数学作弊，成绩记0分
期中.loc[‘数学‘, ‘张三‘] = 0
print(“\n修改后期中成绩：“)
print(期中)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_150.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_152.png)

# 3. 李四举报有功，所有科目加10分
期中[‘李四‘] += 10
print(“\n奖励李四后期中成绩：“)
print(期中)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_154.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_156.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_158.png)

# 4. 题目出错，全体学生所有科目加5分
期中 += 5
print(“\n全体加分后期中成绩：“)
print(期中)
```

![](img/ae0e30edaaeb3b24f057beb681e65bc7_160.png)

## 补充技能：时间序列转换与索引设置

![](img/ae0e30edaaeb3b24f057beb681e65bc7_162.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_164.png)

在实际数据分析中，经常需要处理时间数据并将其设置为索引。

![](img/ae0e30edaaeb3b24f057beb681e65bc7_166.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_168.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_170.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_172.png)

以下是两个常用操作：

![](img/ae0e30edaaeb3b24f057beb681e65bc7_174.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_176.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_178.png)

```python
# 创建包含日期字符串的DataFrame
df_time = pd.DataFrame({
    ‘time‘: [‘2023-10-10‘, ‘2023-11-20‘, ‘2024-01-10‘],
    ‘temperature‘: [30, 33, 31]
})
print(“原始数据：“)
print(df_time)
print(df_time[‘time‘].dtype) # 查看类型，应为 object (字符串)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_180.png)

# 1. 将字符串列转换为时间序列类型
df_time[‘time‘] = pd.to_datetime(df_time[‘time‘])
print(“\n转换时间类型后：“)
print(df_time[‘time‘].dtype) # 类型变为 datetime64[ns]

![](img/ae0e30edaaeb3b24f057beb681e65bc7_182.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_184.png)

# 2. 将时间列设置为行索引
df_time.set_index(‘time‘, inplace=True)
print(“\n设置时间列为索引后：“)
print(df_time)
```

![](img/ae0e30edaaeb3b24f057beb681e65bc7_186.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_188.png)

![](img/ae0e30edaaeb3b24f057beb681e65bc7_190.png)

本节课中我们一起学习了DataFrame的索引与切片操作，这是数据查询和筛选的基石。理解 `[]`、`.loc`、`.iloc` 在不同场景下的用法，以及行、列在切片时的不同规则至关重要。通过课后练习和结合实际案例，你将能熟练运用这些技巧，从复杂的数据集中快速提取所需信息。在接下来的课程中，我们将利用这些知识进行更深入的数据分析实战。