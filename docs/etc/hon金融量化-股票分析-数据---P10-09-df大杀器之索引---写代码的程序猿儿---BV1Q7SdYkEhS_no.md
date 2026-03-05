# Python金融量化：P10：DataFrame的索引与切片操作 📊

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_0.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_2.png)

在本节课中，我们将要学习Pandas中DataFrame数据结构的核心操作之一：索引与切片。DataFrame是一个二维表格型数据结构，虽然它与NumPy数组同为二维，但其索引和切片机制有显著不同。掌握这些操作是进行数据筛选、清洗和分析的基础。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_4.png)

## DataFrame与NumPy数组的索引差异

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_6.png)

上一节我们介绍了DataFrame的基本概念，本节中我们来看看它的索引操作。首先需要明确，DataFrame和NumPy数组是两种不同的数据结构，因此它们的索引和切片机制存在很大区别。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_8.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_10.png)

## 创建示例DataFrame

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_12.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_14.png)

在开始学习具体操作前，我们先创建一个DataFrame作为示例数据。

```python
import pandas as pd
import numpy as np

# 使用NumPy数组生成随机数据，创建DataFrame
data = np.random.randint(60, 100, size=(8, 4))
df = pd.DataFrame(data, columns=['A', 'B', 'C', 'D'])
print(df)
```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_16.png)

## DataFrame的索引操作

索引操作的核心是准确地选取行或列的数据。DataFrame在这方面的规则较为特殊。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_18.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_20.png)

### 1. 列索引

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_22.png)

在DataFrame中，直接使用中括号 `[]` 并传入索引值，默认操作的是列。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_24.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_26.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_28.png)

*   **取单列**：返回一个Series对象。
    ```python
    df['A']  # 选取名为‘A’的列
    ```
*   **取多列**：传入一个列名的列表，返回一个新的DataFrame。
    ```python
    df[['A', 'C']]  # 选取‘A’和‘C’两列
    ```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_30.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_32.png)

**重要注意事项**：如果DataFrame有显示的列索引（即我们指定的列名），那么通过这种机制取列时**只能使用显示索引**，不能使用隐式索引（如数字0, 1, 2）。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_34.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_36.png)

### 2. 行索引

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_38.png)

要选取行，不能直接使用中括号，而需借助 `.loc` 或 `.iloc` 索引器。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_40.png)

*   **取单行**：
    ```python
    df.iloc[0]  # 使用隐式索引选取第0行
    # df.loc[‘row_name‘] # 如果存在显示的行索引名，可用此方式
    ```
*   **取多行**：
    ```python
    df.iloc[[0, 3, 5]]  # 选取第0、3、5行
    ```

### 3. `.loc` 与 `.iloc` 的区别

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_42.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_44.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_46.png)

这两个索引器是行索引的关键，它们的区别在于：
*   **`.iloc`**：基于**隐式索引**（即整数位置，Integer Location）进行选取。它接收整数或整数列表。
*   **`.loc`**：基于**显示索引**（即行/列的标签名，Label Location）进行选取。它接收索引的标签名。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_48.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_50.png)

当DataFrame只有隐式的行索引（0, 1, 2...）时，两者可能都能取到值，但为了清晰和避免错误，应遵循上述规则。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_52.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_54.png)

### 4. 元素索引（同时指定行和列）

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_56.png)

要精确选取某个单元格的值，需要同时指定行和列。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_58.png)

```python
# 使用 .iloc，参数为 (行隐式索引， 列隐式索引)
df.iloc[0, 3]  # 选取第0行，第3列的元素

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_60.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_62.png)

# 使用 .loc，参数为 (行显示索引， 列显示索引)
# 假设行索引有名字 ‘row0‘， 列名为 ‘D‘
# df.loc[‘row0‘, ‘D‘]
```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_64.png)

也可以同时选取多个元素：
```python
df.iloc[[1, 3, 5], 2]  # 选取第1、3、5行，第2列的元素
```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_66.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_68.png)

## DataFrame的切片操作

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_70.png)

切片用于获取一个连续范围的数据，其规则与索引类似但又有其特点。

### 1. 行切片

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_72.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_74.png)

在DataFrame的中括号 `[]` 中直接放入切片对象，操作的是行。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_76.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_78.png)

```python
df[0:2]  # 切片，选取第0行到第1行（左闭右开）
```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_80.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_82.png)

### 2. 列切片

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_84.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_86.png)

要对列进行切片，必须结合 `.loc` 或 `.iloc` 索引器，并使用逗号分隔行和列的位置。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_88.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_90.png)

```python
# 使用 .iloc 进行列切片
# 语法：df.iloc[行切片, 列切片]
df.iloc[:, 0:2]  # 选取所有行，第0列到第1列

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_92.png)

# 注意：.loc 用于列切片时，需要传入显示的列名
# df.loc[:, ‘A‘:‘C‘] # 选取所有行，A列到C列
```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_94.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_96.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_98.png)

## 索引与切片总结

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_100.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_102.png)

以下是DataFrame索引与切片的核心方法总结：

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_104.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_106.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_108.png)

**索引操作：**
*   **取列**：`df[‘col‘]` (单列) 或 `df[[‘col1‘, ‘col2‘]]` (多列)。
*   **取行**：`df.loc[‘label‘]` 或 `df.iloc[index]`。
*   **取元素**：`df.loc[‘row_label‘, ‘col_label‘]` 或 `df.iloc[row_index, col_index]`。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_110.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_112.png)

**切片操作：**
*   **切行**：`df[start:stop:step]`。
*   **切列**：`df.iloc[:, start:stop:step]` 或 `df.loc[:, ‘start_label‘:‘stop_label‘]`。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_114.png)

**关键点**：即使为DataFrame设置了显示索引，其隐式索引依然存在，可以通过 `.iloc` 访问。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_116.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_118.png)

## DataFrame的运算

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_120.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_122.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_124.png)

DataFrame的运算规则与Series一致：**只有行列索引完全对齐的元素才会进行运算**。如果索引不一致，结果中会出现NaN（非数字）值。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_126.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_128.png)

```python
df1 = pd.DataFrame(np.arange(4).reshape(2,2), columns=[‘A‘, ‘B‘])
df2 = pd.DataFrame(np.arange(4).reshape(2,2), columns=[‘B‘, ‘A‘]) # 列顺序不同
print(df1 + df2) # 对应A列与A列相加，B列与B列相加
```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_130.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_132.png)

## 实践练习：学生成绩表操作

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_134.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_136.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_138.png)

让我们通过一个学生成绩表的案例来巩固索引和切片操作。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_140.png)

假设我们有一个期中考试的成绩表 `df_mid`。

```python
# 创建期中、期末成绩表（本例使用相同数据简化）
df_mid = pd.DataFrame(np.random.randint(50, 100, (3, 3)),
                      index=[‘张三‘, ‘李四‘, ‘王五‘],
                      columns=[‘语文‘, ‘数学‘, ‘英语‘])
df_final = df_mid.copy() # 期末成绩表
```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_142.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_144.png)

**需求实现：**

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_146.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_148.png)

1.  **计算平均成绩**：
    ```python
    df_avg = (df_mid + df_final) / 2
    ```
2.  **修改单个成绩**（张三数学作弊记0分）：
    ```python
    df_mid.loc[‘张三‘, ‘数学‘] = 0
    ```
3.  **修改单列所有成绩**（李四所有科目加10分）：
    ```python
    df_mid.loc[:, ‘李四‘] += 10 # 注意：这里‘李四‘是列名。更常见的需求是按行操作。
    # 如果是给“李四”这个人（行）的所有成绩加10分，应为：
    # df_mid.loc[‘李四‘] += 10
    ```
4.  **全体加分**（每题出错，全体学生每科加5分）：
    ```python
    df_mid += 5
    ```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_150.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_152.png)

## 常用补充操作

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_154.png)

### 1. 转换时间序列

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_156.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_158.png)

在数据分析中，经常需要将字符串格式的日期列转换为时间序列类型。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_160.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_162.png)

```python
df = pd.DataFrame({‘time‘: [‘2020-10-10‘, ‘2021-11-20‘, ‘2022-01-10‘],
                   ‘value‘: [30, 31, 30]})
# 将‘time‘列转换为datetime类型
df[‘time‘] = pd.to_datetime(df[‘time‘])
print(df[‘time‘].dtype) # 输出：datetime64[ns]
```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_164.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_166.png)

### 2. 设置索引

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_168.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_170.png)

可以将某一列设置为DataFrame的行索引，以方便基于时间的查询等操作。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_172.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_174.png)

```python
# 将‘time‘列设置为索引，inplace=True表示直接修改原df
df.set_index(‘time‘, inplace=True)
print(df)
```

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_176.png)

## 总结

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_178.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_180.png)

本节课中我们一起学习了DataFrame的索引与切片操作。我们了解到：
1.  直接使用 `df[ ]` 默认操作列，而操作行需使用 `.loc` 或 `.iloc`。
2.  `.loc` 基于显示标签索引，`.iloc` 基于隐式整数位置索引。
3.  切片操作中，直接切片针对行，列切片需结合 `.loc`/`.iloc`。
4.  DataFrame的运算要求索引对齐。
5.  掌握了 `pd.to_datetime()` 和 `df.set_index()` 两个实用方法。

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_182.png)

![](img/720fdc7d66f2a088bcb2c1e01b2774ca_184.png)

这些操作是Pandas数据处理的基石，务必通过练习熟练掌握。在接下来的课程中，我们将通过更多案例来巩固这些核心技能。