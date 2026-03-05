# Python金融量化+业务数据分析：P9：3.df大杀器之索引&切片

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_1.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_3.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_5.png)

在本节课中，我们将要学习Pandas中DataFrame的索引与切片操作。DataFrame是二维数据结构，但其索引和切片方式与我们之前学习的NumPy二维数组有很大不同。我们将通过创建示例、讲解核心方法并进行实际练习，来掌握如何精确地选取DataFrame中的数据。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_7.png)

## 创建示例DataFrame

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_9.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_11.png)

首先，我们创建一个DataFrame作为后续操作的示例数据。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_13.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_15.png)

```python
import pandas as pd
import numpy as np

# 使用NumPy生成随机数据，创建一个8行4列的DataFrame
df = pd.DataFrame(
    np.random.randint(60, 100, size=(8, 4)),
    columns=['A', 'B', 'C', 'D']  # 指定显示的列索引
)
print(df)
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_17.png)

以上代码创建了一个DataFrame，其行索引是隐式的（0-7），列索引是显示的（‘A’, ‘B’, ‘C’, ‘D’）。

## DataFrame的索引操作

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_19.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_21.png)

上一节我们创建了示例数据，本节中我们来看看如何对DataFrame进行索引，即选取特定的行、列或元素。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_23.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_25.png)

DataFrame的索引操作有其独特规则，核心在于区分是操作行还是列。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_27.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_29.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_31.png)

### 选取列

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_33.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_35.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_37.png)

在DataFrame中，直接使用中括号 `[]` 并传入索引，默认是进行**列选取**。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_39.png)

**选取单列**：传入列名，返回一个Series。
```python
df[‘A’]  # 选取名为‘A’的列
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_41.png)

**选取多列**：传入一个包含多个列名的列表，返回一个新的DataFrame。
```python
df[[‘A’, ‘C’]]  # 选取‘A’和‘C’两列
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_43.png)

**重要注意事项**：当DataFrame有显示的列索引时，在中括号内进行索引操作**只能使用显示索引（列名）**，不能直接使用隐式索引（如数字0）。如果想用隐式索引取列，需要使用 `.iloc`，这将在后面介绍。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_45.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_47.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_49.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_51.png)

### 选取行

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_53.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_55.png)

要选取行，不能直接使用中括号，而需借助 `.loc` 或 `.iloc` 属性。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_57.png)

**选取单行**：
```python
df.iloc[0]  # 使用隐式索引选取第0行
# 或（如果设置了显示的行索引名）
# df.loc[‘row_name’]  # 使用显示索引选取行
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_59.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_61.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_63.png)

**选取多行**：
```python
df.iloc[[0, 3, 5]]  # 选取第0、3、5行
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_65.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_67.png)

### `.loc` 与 `.iloc` 的区别

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_69.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_71.png)

这是DataFrame索引的核心概念，两者的区别在于使用的索引类型不同：
*   **`.iloc`**：基于**隐式索引（整数位置）** 进行选取。`i` 代表 `integer`。
*   **`.loc`**：基于**显示索引（索引标签）** 进行选取。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_73.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_75.png)

在我们的示例中，因为只指定了显示的列索引，行索引是隐式的，所以目前只能用 `.iloc` 来取行。如果为DataFrame也指定了显示的行索引，则可以使用 `.loc` 通过行索引名来取行。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_77.png)

### 选取特定元素

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_79.png)

要选取单个或多个特定位置的元素，需要同时指定行和列。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_81.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_83.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_85.png)

**选取单个元素**：使用逗号分隔行和列索引。
```python
df.iloc[0, 3]  # 选取第0行，第3列（隐式索引）
# 如果行列均有显示索引，则可用.loc
# df.loc[‘row_name’, ‘col_name’]
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_87.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_89.png)

**选取多个元素**：可以传入列表来选取多个位置。
```python
df.iloc[[1, 3, 5], 2]  # 选取第1、3、5行的第2列
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_91.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_93.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_95.png)

## DataFrame的切片操作

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_97.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_99.png)

了解了索引之后，我们来看看如何进行切片操作，即选取一个连续范围的数据。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_101.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_103.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_105.png)

**行切片**：在中括号 `[]` 内直接使用切片语法，操作的是行。
```python
df[0:2]  # 切片选取前两行（0和1行）
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_107.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_109.png)

**列切片**：要切片选取列，必须结合 `.loc` 或 `.iloc`，并通过逗号语法指定。
```python
df.iloc[:, 0:2]  # 选取所有行，前两列（0和1列）
# 如果使用显示列索引，且列名有序，可用.loc
# df.loc[:, ‘A’:‘C’]
```
这里 `:` 表示选取所有行，`0:2` 表示选取从第0列到第1列（左闭右开）。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_111.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_113.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_115.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_117.png)

## 索引与切片总结

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_119.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_121.png)

以下是DataFrame索引和切片操作的要点总结：

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_123.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_125.png)

**索引操作总结：**
*   **取列**：`df[‘col’]` (单列) 或 `df[[‘col1‘, ’col2‘]]` (多列)。
*   **取行**：`df.loc[‘index’]` (显示索引) 或 `df.iloc[i]` (隐式索引)。
*   **取元素**：`df.loc[‘row‘, ’col‘]` 或 `df.iloc[i, j]`。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_127.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_129.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_131.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_133.png)

**切片操作总结：**
*   **切行**：`df[start:end]`
*   **切列**：`df.iloc[:, start:end]` 或 `df.loc[:, ‘col_start‘:’col_end‘]`

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_135.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_137.png)

DataFrame的索引和切片规则与NumPy数组不同，需要单独记忆和练习。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_139.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_141.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_143.png)

## 巩固练习：学生成绩表操作

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_145.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_147.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_149.png)

让我们通过一个实际案例来巩固所学知识。假设我们有一个学生期中考试成绩的DataFrame。

```python
# 假设df是一个学生成绩DataFrame，行索引是学生名，列索引是科目名
# 例如：
#        数学  语文  英语
# 张三    90   85   88
# 李四    78   92   95
# 王五    85   88   90

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_151.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_153.png)

期中 = df.copy()
期末 = df.copy()  # 假设期末成绩相同，仅用于演示
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_155.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_157.png)

**需求1：计算每位学生期中期末的平均分**
```python
平均分 = (期中 + 期末) / 2
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_159.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_161.png)

**需求2：张三期中考试数学作弊，成绩记为零分**
```python
期中.loc[‘张三‘, ’数学‘] = 0
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_163.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_165.png)

**需求3：李四举报有功，所有科目成绩加10分**
```python
期中[‘李四‘] += 10  # 选取‘李四’这一列（Series），整体加10
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_167.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_169.png)

**需求4：老师发现题目出错，所有学生所有科目加5分**
```python
期中 += 5  # DataFrame与标量运算，每个元素都加5
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_171.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_173.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_175.png)

## 补充技能：时间序列处理

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_177.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_179.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_181.png)

在实际数据分析中，经常需要处理时间数据。Pandas提供了便捷的功能。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_183.png)

**1. 将字符串列转换为时间类型**
```python
# 假设df有一列‘time’是字符串格式的日期
df[‘time‘] = pd.to_datetime(df[‘time’])
```

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_185.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_187.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_189.png)

**2. 将时间列设置为行索引**
```python
df.set_index(‘time‘, inplace=True)  # inplace=True使更改生效于原DataFrame
```
设置后，时间数据将成为DataFrame的索引，便于进行基于时间的重采样、切片等高级操作。

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_191.png)

![](img/75e6d6916dbc7fb27c8905ea0fa1c39f_193.png)

本节课中我们一起学习了DataFrame强大的索引与切片操作。我们明确了直接索引与 `.loc`/`.iloc` 索引的区别，掌握了选取行、列、元素以及进行切片的方法。最后，我们还通过成绩管理案例巩固了索引修改数据的技巧，并了解了处理时间数据的基本流程。这些是进行高效数据筛选和操作的基础，请务必熟练掌握。