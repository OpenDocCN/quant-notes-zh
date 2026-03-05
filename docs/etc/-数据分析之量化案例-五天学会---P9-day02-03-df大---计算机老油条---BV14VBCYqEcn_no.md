# 数据分析之量化案例：P9：day02-03 DataFrame大杀器之索引与切片 📊

![](img/c3cc0d20a6a1706e7bc6991905db27fb_0.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_2.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_4.png)

在本节课中，我们将要学习Pandas中DataFrame的核心操作：索引与切片。DataFrame是一个二维数据结构，但其索引和切片方式与我们之前学习的NumPy数组有很大不同。我们将通过创建示例、讲解规则和实际练习，帮助你掌握如何精准地获取和操作DataFrame中的数据。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_6.png)

## 创建示例DataFrame

![](img/c3cc0d20a6a1706e7bc6991905db27fb_8.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_10.png)

首先，我们创建一个DataFrame作为后续操作的基础。DataFrame可以由NumPy数组生成，并指定列索引。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_12.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_14.png)

```python
import pandas as pd
import numpy as np

![](img/c3cc0d20a6a1706e7bc6991905db27fb_16.png)

# 创建一个8行4列的随机整数数组，数值范围在60到100之间
data = np.random.randint(60, 100, size=(8, 4))
# 创建DataFrame，并指定列索引为A, B, C, D
df = pd.DataFrame(data, columns=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(df)
```

## DataFrame的索引操作

![](img/c3cc0d20a6a1706e7bc6991905db27fb_18.png)

上一节我们创建了DataFrame，本节中我们来看看如何通过索引获取数据。DataFrame的索引操作因其二维特性而变得独特。

### 取单列与多列

![](img/c3cc0d20a6a1706e7bc6991905db27fb_20.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_22.png)

在DataFrame中，直接在中括号 `[]` 内放置索引，默认操作的是**列索引**。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_24.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_26.png)

*   **取单列**：返回一个Series对象。
    ```python
    # 取列名为‘A‘的列
    series_a = df[‘A‘]
    print(series_a)
    ```
*   **取多列**：需要将多个列名放入一个列表中，返回一个新的DataFrame。
    ```python
    # 取列名为‘A‘和‘C‘的列
    df_ac = df[[‘A‘, ‘C‘]]
    print(df_ac)
    ```

![](img/c3cc0d20a6a1706e7bc6991905db27fb_28.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_30.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_32.png)

**注意事项**：如果DataFrame有**显示的列索引**，则通过此机制取列时**只能使用显示索引**，不能使用隐式索引（如数字0, 1, 2）。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_34.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_36.png)

### 取单行与多行

![](img/c3cc0d20a6a1706e7bc6991905db27fb_38.png)

取行操作需要借助 `.loc` 或 `.iloc` 索引器。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_40.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_42.png)

*   **取单行**：返回一个Series，其索引是列名。
    ```python
    # 使用.iloc取第0行（隐式索引）
    row_0_iloc = df.iloc[0]
    print(row_0_iloc)
    ```
*   **取多行**：将多个行索引放入列表。
    ```python
    # 使用.iloc取第0, 3, 5行
    rows_035 = df.iloc[[0, 3, 5]]
    print(rows_035)
    ```

### `.loc` 与 `.iloc` 的区别

![](img/c3cc0d20a6a1706e7bc6991905db27fb_44.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_46.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_48.png)

这两个索引器是取行的关键，它们的区别在于使用的索引类型不同。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_50.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_52.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_54.png)

*   **`.iloc`**：基于**隐式索引**（即整数位置，从0开始）进行选取。`i` 代表 `integer`。
    ```python
    # 选取第2行（隐式索引）
    df.iloc[2]
    ```
*   **`.loc`**：基于**显示索引**（即行/列的标签名）进行选取。
    ```python
    # 假设df有显示的行索引名‘row_1‘, ‘row_2‘...
    # df.loc[‘row_2‘] # 选取行标签为‘row_2‘的行
    ```
    在我们的示例中，行索引是隐式的（0,1,2…），因此使用 `.loc[0]` 也能取到第0行，但严格来说，`.loc` 是为显示索引设计的。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_56.png)

### 取单个与多个元素

![](img/c3cc0d20a6a1706e7bc6991905db27fb_58.png)

要获取DataFrame中某个特定的值（单元格），需要同时指定行和列。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_60.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_62.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_64.png)

*   **取单个元素**：使用 `[行索引, 列索引]` 的格式。
    ```python
    # 使用.iloc取第0行，第3列的元素（隐式索引）
    value = df.iloc[0, 3]
    print(value)
    # 使用.loc取第0行，列名为‘D‘的元素（显示列索引）
    value_loc = df.loc[0, ‘D‘] # 注意行索引0在这里被视为标签
    print(value_loc)
    ```
*   **取多个元素**：可以同时指定多个行和列的索引。
    ```python
    # 取第1,3,5行，第2列的元素
    values = df.iloc[[1, 3, 5], 2]
    print(values)
    ```

![](img/c3cc0d20a6a1706e7bc6991905db27fb_66.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_68.png)

## DataFrame的切片操作

![](img/c3cc0d20a6a1706e7bc6991905db27fb_70.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_72.png)

切片用于获取连续范围的数据，DataFrame的切片规则也与索引类似。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_74.png)

### 切行

![](img/c3cc0d20a6a1706e7bc6991905db27fb_76.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_78.png)

在DataFrame的中括号 `[]` 内直接使用切片语法，操作的是**行**。

```python
# 切取第0行到第2行（不包含第2行）
slice_rows = df[0:2]
print(slice_rows)
```

![](img/c3cc0d20a6a1706e7bc6991905db27fb_80.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_82.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_84.png)

### 切列

![](img/c3cc0d20a6a1706e7bc6991905db27fb_86.png)

切列必须结合 `.loc` 或 `.iloc` 索引器，并使用 `[行切片, 列切片]` 的格式，其中行切片用 `:` 表示不切。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_88.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_90.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_92.png)

```python
# 使用.iloc切取所有行，前两列（隐式索引）
slice_cols_iloc = df.iloc[:, 0:2]
print(slice_cols_iloc)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_94.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_96.png)

# 如果使用.loc，且列有显示索引，则可以按列名切片
# slice_cols_loc = df.loc[:, ‘A‘:‘C‘] # 切取A列到C列
```

![](img/c3cc0d20a6a1706e7bc6991905db27fb_98.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_100.png)

## 索引与切片总结

![](img/c3cc0d20a6a1706e7bc6991905db27fb_102.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_104.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_106.png)

以下是DataFrame索引与切片的核心方法总结：

![](img/c3cc0d20a6a1706e7bc6991905db27fb_108.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_110.png)

**索引操作：**
*   **取列**：`df[‘col‘]` (单列) 或 `df[[‘col1‘, ‘col2‘]]` (多列)。
*   **取行**：`df.loc[‘label‘]` (显示索引) 或 `df.iloc[index]` (隐式索引)。
*   **取元素**：`df.loc[‘row_label‘, ‘col_label‘]` 或 `df.iloc[row_idx, col_idx]`。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_112.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_114.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_116.png)

**切片操作：**
*   **切行**：`df[start:stop:step]`。
*   **切列**：`df.iloc[:, start:stop:step]` 或 `df.loc[:, ‘start_col‘:‘stop_col‘]`。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_118.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_120.png)

**重要概念**：即使为DataFrame或Series设定了显示索引，其隐式索引（整数位置）依然存在，可以通过 `.iloc` 访问。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_122.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_124.png)

## DataFrame的运算

![](img/c3cc0d20a6a1706e7bc6991905db27fb_126.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_128.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_130.png)

DataFrame的运算规则与Series一致：**只有行列索引完全对齐的元素才会进行运算**。索引不一致的位置会产生`NaN`值。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_132.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_134.png)

```python
df1 = pd.DataFrame(np.random.rand(3,3), columns=[‘A‘,‘B‘,‘C‘])
df2 = pd.DataFrame(np.random.rand(3,3), columns=[‘A‘,‘B‘,‘D‘]) # 列索引不同
result = df1 + df2 # C列和D列因索引不匹配，结果将为NaN
print(result)
```

![](img/c3cc0d20a6a1706e7bc6991905db27fb_136.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_138.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_140.png)

## 综合练习：学生成绩表操作

![](img/c3cc0d20a6a1706e7bc6991905db27fb_142.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_144.png)

让我们通过一个学生成绩表的案例来巩固索引操作。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_146.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_148.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_150.png)

```python
# 创建期中成绩表
期中 = pd.DataFrame({
    ‘张三‘: [150, 120, 130],
    ‘李四‘: [100, 110, 90],
    ‘王五‘: [140, 130, 120]
}, index=[‘语文‘, ‘数学‘, ‘英语‘])
期末 = 期中.copy() # 假设期末成绩与期中相同

# 1. 求期中期末平均值
平均成绩 = (期中 + 期末) / 2
print(“平均成绩:\n“, 平均成绩)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_152.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_154.png)

# 2. 张三期中数学作弊，成绩记0分
期中.loc[‘数学‘, ‘张三‘] = 0
print(“修改后期中成绩:\n“, 期中)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_156.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_158.png)

# 3. 李四举报有功，所有成绩加10分
期中[‘李四‘] += 10
print(“奖励后期中成绩:\n“, 期中)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_160.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_162.png)

# 4. 题目出错，所有学生所有科目加5分
期中 += 5
print(“最终期中成绩:\n“, 期中)
```

![](img/c3cc0d20a6a1706e7bc6991905db27fb_164.png)

## 常用操作：时间序列转换与索引设置

![](img/c3cc0d20a6a1706e7bc6991905db27fb_166.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_168.png)

在实际数据分析中，经常需要处理时间数据并将其设置为索引。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_170.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_172.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_174.png)

```python
# 创建包含日期字符串的DataFrame
df_time = pd.DataFrame({
    ‘time‘: [‘2020-10-10‘, ‘2021-11-20‘, ‘2022-01-10‘],
    ‘temperature‘: [30, 31, 30]
})
print(“原始数据:\n“, df_time)
print(“time列类型:“, df_time[‘time‘].dtype)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_176.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_178.png)

# 1. 将‘time‘列转换为时间序列类型
df_time[‘time‘] = pd.to_datetime(df_time[‘time‘])
print(“转换后time列类型:“, df_time[‘time‘].dtype)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_180.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_182.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_184.png)

# 2. 将‘time‘列设置为行索引
df_time.set_index(‘time‘, inplace=True)
print(“设置时间索引后:\n“, df_time)
```

![](img/c3cc0d20a6a1706e7bc6991905db27fb_186.png)

## 总结

![](img/c3cc0d20a6a1706e7bc6991905db27fb_188.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_190.png)

本节课中我们一起学习了DataFrame强大的索引与切片功能。我们认识到：
1.  DataFrame通过中括号 `[]` 直接操作的是列。
2.  取行必须使用 `.loc`（基于标签）或 `.iloc`（基于整数位置）索引器。
3.  切片规则也遵循“直接切行，需用索引器切列”的原则。
4.  运算时要求行列索引对齐。
5.  掌握了 `pd.to_datetime()` 转换时间格式和 `set_index()` 设置新索引的实用技巧。

![](img/c3cc0d20a6a1706e7bc6991905db27fb_192.png)

![](img/c3cc0d20a6a1706e7bc6991905db27fb_194.png)

这些操作是后续进行数据筛选、清洗和分析的基础，务必熟练掌握。在下一节，我们将通过更多案例来深化对这些知识的理解。