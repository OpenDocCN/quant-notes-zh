# Python数据分析：P10：DataFrame索引与切片操作详解 📊

![](img/62f67244cd72900082dac0bd01c0e08e_0.png)

![](img/62f67244cd72900082dac0bd01c0e08e_2.png)

在本节课中，我们将要学习Pandas库中DataFrame数据结构的核心操作之一：索引与切片。DataFrame是一个二维表格型数据结构，与之前学习的NumPy二维数组在索引和切片方式上存在显著区别。我们将通过创建示例、对比演示和实际练习，系统地掌握DataFrame的索引与切片机制。

![](img/62f67244cd72900082dac0bd01c0e08e_4.png)

## 创建示例DataFrame

![](img/62f67244cd72900082dac0bd01c0e08e_6.png)

![](img/62f67244cd72900082dac0bd01c0e08e_8.png)

首先，我们创建一个DataFrame作为后续操作的基础。数据由一个NumPy数组生成，并指定列索引。

![](img/62f67244cd72900082dac0bd01c0e08e_10.png)

![](img/62f67244cd72900082dac0bd01c0e08e_12.png)

```python
import pandas as pd
import numpy as np

![](img/62f67244cd72900082dac0bd01c0e08e_14.png)

# 创建一个8行4列的DataFrame，数值范围在60-100之间
df = pd.DataFrame(np.random.randint(60, 100, size=(8, 4)), columns=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(df)
```

## DataFrame的索引操作

![](img/62f67244cd72900082dac0bd01c0e08e_16.png)

上一节我们创建了DataFrame，本节中我们来看看如何从中获取数据。DataFrame的索引操作主要分为取列、取行和取单个元素。

### 取列操作

![](img/62f67244cd72900082dac0bd01c0e08e_18.png)

![](img/62f67244cd72900082dac0bd01c0e08e_20.png)

在DataFrame中，直接使用中括号 `[]` 并传入索引，默认是进行列操作。

![](img/62f67244cd72900082dac0bd01c0e08e_22.png)

*   **取单列**：传入列名（显示索引），返回一个Series。
    ```python
    # 取名为‘A‘的列
    series_a = df[‘A‘]
    print(series_a)
    ```
*   **取多列**：传入一个包含多个列名的列表，返回一个新的DataFrame。
    ```python
    # 取名为‘A‘和‘C‘的列
    df_ac = df[[‘A‘, ‘C‘]]
    print(df_ac)
    ```
**注意事项**：如果DataFrame有显示的列索引，则必须使用显示索引（列名）来取列。直接使用数字0会报错（KeyError），因为0被视为隐式索引。

![](img/62f67244cd72900082dac0bd01c0e08e_24.png)

![](img/62f67244cd72900082dac0bd01c0e08e_26.png)

![](img/62f67244cd72900082dac0bd01c0e08e_28.png)

### 取行操作

![](img/62f67244cd72900082dac0bd01c0e08e_30.png)

![](img/62f67244cd72900082dac0bd01c0e08e_32.png)

取行操作需要使用 `.loc[]` 或 `.iloc[]` 方法。

![](img/62f67244cd72900082dac0bd01c0e08e_34.png)

![](img/62f67244cd72900082dac0bd01c0e08e_36.png)

*   **取单行**：使用 `.iloc[行索引]` 或 `.loc[行索引]`。
    ```python
    # 使用.iloc取第0行（隐式索引）
    row_0_iloc = df.iloc[0]
    print(row_0_iloc)
    ```
*   **取多行**：传入一个索引列表。
    ```python
    # 取第0, 3, 5行
    rows_multi = df.iloc[[0, 3, 5]]
    print(rows_multi)
    ```

![](img/62f67244cd72900082dac0bd01c0e08e_38.png)

![](img/62f67244cd72900082dac0bd01c0e08e_40.png)

### `.loc` 与 `.iloc` 的区别

这是两个核心方法，它们的区别在于使用的索引类型不同：
*   **`.iloc[]`**：基于**隐式索引**（即整数位置，从0开始）进行选择。`i` 代表 integer。
*   **`.loc[]`**：基于**显示索引**（即行/列的标签名）进行选择。

![](img/62f67244cd72900082dac0bd01c0e08e_42.png)

![](img/62f67244cd72900082dac0bd01c0e08e_44.png)

如果DataFrame没有设置显示的行索引（如本例），那么行索引就是隐式的0,1,2…。此时，用 `.loc[0]` 也能取到第0行，因为0既是隐式索引，也被当作显示索引的标签。但为了清晰和避免混淆，建议遵循：取行时，想用位置就用 `.iloc`，想用标签就用 `.loc`。

![](img/62f67244cd72900082dac0bd01c0e08e_46.png)

![](img/62f67244cd72900082dac0bd01c0e08e_48.png)

![](img/62f67244cd72900082dac0bd01c0e08e_50.png)

### 取单个或多个元素

![](img/62f67244cd72900082dac0bd01c0e08e_52.png)

![](img/62f67244cd72900082dac0bd01c0e08e_54.png)

要取DataFrame中某个具体的值（单元格），需要同时指定行和列。

![](img/62f67244cd72900082dac0bd01c0e08e_56.png)

*   **取单个元素**：使用 `df.iloc[行索引, 列索引]` 或 `df.loc[行标签, 列标签]`。
    ```python
    # 使用.iloc取第1行，第2列的值（隐式索引）
    value_iloc = df.iloc[1, 2]
    print(value_iloc)
    # 使用.loc取第1行（假设行标签为1），‘B‘列的值
    # value_loc = df.loc[1, ‘B‘] # 如果行标签是数字
    ```
*   **取多个元素**：可以同时传入行索引列表和列索引列表。
    ```python
    # 取第1,3,5行，第2列的值
    values = df.iloc[[1, 3, 5], 2]
    print(values)
    ```

![](img/62f67244cd72900082dac0bd01c0e08e_58.png)

## DataFrame的切片操作

![](img/62f67244cd72900082dac0bd01c0e08e_60.png)

![](img/62f67244cd72900082dac0bd01c0e08e_62.png)

![](img/62f67244cd72900082dac0bd01c0e08e_64.png)

切片操作用于获取连续的行或列子集。DataFrame的切片语法有其特殊性。

![](img/62f67244cd72900082dac0bd01c0e08e_66.png)

![](img/62f67244cd72900082dac0bd01c0e08e_68.png)

### 行切片

![](img/62f67244cd72900082dac0bd01c0e08e_70.png)

在DataFrame的中括号 `[]` 中直接使用切片语法，是对**行**进行切片。

![](img/62f67244cd72900082dac0bd01c0e08e_72.png)

![](img/62f67244cd72900082dac0bd01c0e08e_74.png)

```python
# 切片获取前3行（0, 1, 2行）
df_slice_rows = df[0:3]
print(df_slice_rows)
```

### 列切片

![](img/62f67244cd72900082dac0bd01c0e08e_76.png)

![](img/62f67244cd72900082dac0bd01c0e08e_78.png)

要对列进行切片，必须结合 `.iloc[]` 或 `.loc[]` 方法，并使用逗号分隔行和列的位置。通常将行部分设为 `:` 表示所有行。

![](img/62f67244cd72900082dac0bd01c0e08e_80.png)

![](img/62f67244cd72900082dac0bd01c0e08e_82.png)

```python
# 使用.iloc对所有行，取前2列（隐式索引）
df_slice_cols_iloc = df.iloc[:, 0:2]
print(df_slice_cols_iloc)

![](img/62f67244cd72900082dac0bd01c0e08e_84.png)

![](img/62f67244cd72900082dac0bd01c0e08e_86.png)

# 使用.loc对所有行，取从‘A‘列到‘C‘列（显示索引，注意末端包含）
# df_slice_cols_loc = df.loc[:, ‘A‘:‘C‘]
```

![](img/62f67244cd72900082dac0bd01c0e08e_88.png)

![](img/62f67244cd72900082dac0bd01c0e08e_90.png)

![](img/62f67244cd72900082dac0bd01c0e08e_92.png)

**关键点总结**：
*   `df[切片]` -> 行切片
*   `df.iloc[行切片, 列切片]` -> 基于位置的切片
*   `df.loc[行标签切片, 列标签切片]` -> 基于标签的切片

![](img/62f67244cd72900082dac0bd01c0e08e_94.png)

## 索引与切片机制总结

![](img/62f67244cd72900082dac0bd01c0e08e_96.png)

![](img/62f67244cd72900082dac0bd01c0e08e_98.png)

以下是DataFrame索引与切片操作的要点总结：

![](img/62f67244cd72900082dac0bd01c0e08e_100.png)

![](img/62f67244cd72900082dac0bd01c0e08e_102.png)

![](img/62f67244cd72900082dac0bd01c0e08e_104.png)

![](img/62f67244cd72900082dac0bd01c0e08e_106.png)

**索引操作**
*   **取列**：`df[‘列名‘]` 取单列；`df[[‘列名1‘, ‘列名2‘]]` 取多列。
*   **取行**：`df.iloc[行索引]` (隐式索引) 或 `df.loc[行标签]` (显示索引)。
*   **取元素**：`df.iloc[行索引, 列索引]` 或 `df.loc[行标签, 列标签]`。

![](img/62f67244cd72900082dac0bd01c0e08e_108.png)

**切片操作**
*   **切行**：`df[起始索引:结束索引]`
*   **切列**：`df.iloc[:, 起始索引:结束索引]` 或 `df.loc[:, 起始标签:结束标签]`

![](img/62f67244cd72900082dac0bd01c0e08e_110.png)

![](img/62f67244cd72900082dac0bd01c0e08e_112.png)

![](img/62f67244cd72900082dac0bd01c0e08e_114.png)

**重要概念**：即使为DataFrame或Series设置了显示索引，其隐式索引（整数位置）依然存在，可以通过 `.iloc` 访问。

![](img/62f67244cd72900082dac0bd01c0e08e_116.png)

![](img/62f67244cd72900082dac0bd01c0e08e_118.png)

## DataFrame的运算

![](img/62f67244cd72900082dac0bd01c0e08e_120.png)

![](img/62f67244cd72900082dac0bd01c0e08e_122.png)

DataFrame之间的运算规则与Series类似：**索引对齐**。两个DataFrame进行加减乘除等运算时，只有行索引和列索引都匹配的元素才会参与计算，不匹配的位置会得到 `NaN`（非数字）值。

![](img/62f67244cd72900082dac0bd01c0e08e_124.png)

![](img/62f67244cd72900082dac0bd01c0e08e_126.png)

![](img/62f67244cd72900082dac0bd01c0e08e_128.png)

```python
df1 = pd.DataFrame({‘A‘: [1, 2], ‘B‘: [3, 4]})
df2 = pd.DataFrame({‘A‘: [5, 6], ‘B‘: [7, 8]})
# 对应位置元素相加
result = df1 + df2
print(result)
```

![](img/62f67244cd72900082dac0bd01c0e08e_130.png)

![](img/62f67244cd72900082dac0bd01c0e08e_132.png)

## 实战练习：学生成绩表操作

![](img/62f67244cd72900082dac0bd01c0e08e_134.png)

![](img/62f67244cd72900082dac0bd01c0e08e_136.png)

![](img/62f67244cd72900082dac0bd01c0e08e_138.png)

让我们通过一个综合练习来巩固索引和切片操作。

![](img/62f67244cd72900082dac0bd01c0e08e_140.png)

![](img/62f67244cd72900082dac0bd01c0e08e_142.png)

![](img/62f67244cd72900082dac0bd01c0e08e_144.png)

假设我们有一个期中考试成绩表 `df_mid`。

![](img/62f67244cd72900082dac0bd01c0e08e_146.png)

```python
# 创建期中成绩表
df_mid = pd.DataFrame({
    ‘张三‘: [150, 120, 135],
    ‘李四‘: [90, 145, 120],
    ‘王五‘: [115, 130, 140]
}, index=[‘语文‘, ‘数学‘, ‘英语‘])
print(“期中成绩：“)
print(df_mid)
```

![](img/62f67244cd72900082dac0bd01c0e08e_148.png)

**需求实现：**

![](img/62f67244cd72900082dac0bd01c0e08e_150.png)

1.  **计算平均分**：假设期末成绩表 `df_final` 与期中相同，计算平均分。
    ```python
    df_final = df_mid # 假设期末成绩相同
    average = (df_mid + df_final) / 2
    print(“平均分：“)
    print(average)
    ```
2.  **修改成绩**：张三数学考试作弊，成绩记为零分。
    ```python
    # 使用.loc定位行标签‘数学‘，列标签‘张三‘，并修改其值
    df_mid.loc[‘数学‘, ‘张三‘] = 0
    print(“修改后期中成绩：“)
    print(df_mid)
    ```
3.  **批量加分**：李四举报有功，所有科目加10分。
    ```python
    # 取‘李四‘这一列（一个Series），然后整体加10
    df_mid[‘李四‘] += 10
    print(“李四加分后：“)
    print(df_mid)
    ```
4.  **全体加分**：题目出错，全体学生所有科目加5分。
    ```python
    # 整个DataFrame加上一个标量，每个元素都加5
    df_mid += 5
    print(“全体加分后：“)
    print(df_mid)
    ```

![](img/62f67244cd72900082dac0bd01c0e08e_152.png)

![](img/62f67244cd72900082dac0bd01c0e08e_154.png)

## 常用补充操作

![](img/62f67244cd72900082dac0bd01c0e08e_156.png)

![](img/62f67244cd72900082dac0bd01c0e08e_158.png)

最后，介绍两个处理数据时非常实用的操作。

![](img/62f67244cd72900082dac0bd01c0e08e_160.png)

### 1. 转换列数据类型

![](img/62f67244cd72900082dac0bd01c0e08e_162.png)

![](img/62f67244cd72900082dac0bd01c0e08e_164.png)

例如，将字符串格式的日期列转换为Pandas的日期时间类型。

![](img/62f67244cd72900082dac0bd01c0e08e_166.png)

![](img/62f67244cd72900082dac0bd01c0e08e_168.png)

![](img/62f67244cd72900082dac0bd01c0e08e_170.png)

```python
df_temp = pd.DataFrame({
    ‘time‘: [‘2020-10-10‘, ‘2021-11-20‘, ‘2022-01-10‘],
    ‘temperature‘: [‘30‘, ‘31‘, ‘30‘]
})
print(“原始数据：“)
print(df_temp)
print(df_temp[‘time‘].dtype) # 查看类型，通常是 object (字符串)

![](img/62f67244cd72900082dac0bd01c0e08e_172.png)

![](img/62f67244cd72900082dac0bd01c0e08e_174.png)

# 使用 pd.to_datetime 进行转换
df_temp[‘time‘] = pd.to_datetime(df_temp[‘time‘])
print(“\n转换后：“)
print(df_temp)
print(df_temp[‘time‘].dtype) # 类型变为 datetime64[ns]
```

![](img/62f67244cd72900082dac0bd01c0e08e_176.png)

![](img/62f67244cd72900082dac0bd01c0e08e_178.png)

![](img/62f67244cd72900082dac0bd01c0e08e_180.png)

### 2. 设置某列为行索引

![](img/62f67244cd72900082dac0bd01c0e08e_182.png)

使用 `set_index` 方法可以将DataFrame的某一列设置为新的行索引。

![](img/62f67244cd72900082dac0bd01c0e08e_184.png)

![](img/62f67244cd72900082dac0bd01c0e08e_186.png)

```python
# 将‘time‘列设置为行索引，inplace=True表示直接修改原DataFrame
df_temp.set_index(‘time‘, inplace=True)
print(“设置‘time‘为索引后：“)
print(df_temp)
# 现在行索引是日期，原来的‘time‘列消失了
```

![](img/62f67244cd72900082dac0bd01c0e08e_188.png)

![](img/62f67244cd72900082dac0bd01c0e08e_190.png)

本节课中我们一起学习了DataFrame的索引与切片，这是进行数据筛选和定位的基础。关键要区分 `.loc`（标签索引）和 `.iloc`（位置索引）的用法，并理解取列、取行、切片的不同语法。通过练习，我们掌握了如何灵活运用这些操作来处理实际数据。在接下来的课程中，我们将利用这些知识进行更复杂的数据处理。