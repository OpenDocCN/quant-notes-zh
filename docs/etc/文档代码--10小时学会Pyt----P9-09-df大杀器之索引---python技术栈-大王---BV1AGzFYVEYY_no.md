# Python金融量化：P9：DataFrame的索引与切片操作 📊

![](img/a2b67f42bd0e803cc2dc22b067504706_0.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_2.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_4.png)

在本节课中，我们将深入学习Pandas中DataFrame这一核心数据结构的索引与切片操作。DataFrame是一个二维表格型数据结构，虽然与NumPy数组同为二维，但其索引和切片机制有显著不同。掌握这些操作是进行高效数据分析的基础。

![](img/a2b67f42bd0e803cc2dc22b067504706_6.png)

## 创建示例DataFrame

![](img/a2b67f42bd0e803cc2dc22b067504706_8.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_10.png)

首先，我们创建一个DataFrame作为后续操作的示例数据。

![](img/a2b67f42bd0e803cc2dc22b067504706_12.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_14.png)

```python
import pandas as pd
import numpy as np

# 使用NumPy数组生成随机数据，并指定列索引
data = np.random.randint(60, 100, size=(8, 4))
df = pd.DataFrame(data, columns=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(df)
```

![](img/a2b67f42bd0e803cc2dc22b067504706_16.png)

## DataFrame的索引操作

![](img/a2b67f42bd0e803cc2dc22b067504706_18.png)

上一节我们创建了DataFrame，本节中我们来看看如何从中提取数据。索引操作的核心在于理解DataFrame中括号 `[]` 的行为。

![](img/a2b67f42bd0e803cc2dc22b067504706_20.png)

### 取单列与多列

![](img/a2b67f42bd0e803cc2dc22b067504706_22.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_24.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_26.png)

在DataFrame中，直接在中括号内放置索引，默认操作的是**列**。

![](img/a2b67f42bd0e803cc2dc22b067504706_28.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_30.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_32.png)

*   **取单列**：返回一个Series对象。
    ```python
    df[‘A‘]  # 取出名为‘A‘的列
    ```
*   **取多列**：传入一个列名的列表，返回一个新的DataFrame。
    ```python
    df[[‘A‘, ‘C‘]]  # 取出‘A‘和‘C‘两列
    ```
**注意**：如果DataFrame有**显示的列索引**，则必须使用显示索引名（如‘A‘）来取列，不能使用隐式的整数位置（如0）。

![](img/a2b67f42bd0e803cc2dc22b067504706_34.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_36.png)

### 取单行与多行

![](img/a2b67f42bd0e803cc2dc22b067504706_38.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_40.png)

取行操作需要使用 `.loc[]` 或 `.iloc[]` 方法。

*   **取单行**：
    ```python
    df.iloc[0]  # 使用.iloc，按隐式整数位置取第0行
    # df.loc[0]  # 如果存在显示的行索引名为0，可以使用.loc
    ```
*   **取多行**：
    ```python
    df.iloc[[0, 3, 5]]  # 取出第0、3、5行
    ```

![](img/a2b67f42bd0e803cc2dc22b067504706_42.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_44.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_46.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_48.png)

### `.loc` 与 `.iloc` 的区别

![](img/a2b67f42bd0e803cc2dc22b067504706_50.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_52.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_54.png)

这是两个最重要的索引器，它们的区别必须牢记：
*   **`.iloc[index]`**：基于**隐式整数位置**进行索引。`i` 代表 `integer`。
*   **`.loc[index]`**：基于**显示索引标签**进行索引。

![](img/a2b67f42bd0e803cc2dc22b067504706_56.png)

### 取单个与多个元素

![](img/a2b67f42bd0e803cc2dc22b067504706_58.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_60.png)

结合行和列，可以精确提取某个或某些单元格的值。

![](img/a2b67f42bd0e803cc2dc22b067504706_62.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_64.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_66.png)

*   **取单个元素**：格式为 `df.iloc[行位置, 列位置]` 或 `df.loc[行标签, 列标签]`。
    ```python
    df.iloc[0, 3]    # 取第0行，第3列（隐式索引）
    df.loc[0, ‘D‘]   # 取行标签为0，列标签为‘D‘的值（显示索引）
    ```
*   **取多个元素**：可以同时指定多行和多列的位置/标签。
    ```python
    df.iloc[[1, 3, 5], 2]  # 取出第1、3、5行的第2列的值
    ```

![](img/a2b67f42bd0e803cc2dc22b067504706_68.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_70.png)

## DataFrame的切片操作

![](img/a2b67f42bd0e803cc2dc22b067504706_72.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_74.png)

切片操作用于获取数据的连续子集。DataFrame的切片规则与索引类似，但中括号内的切片默认作用于**行**。

![](img/a2b67f42bd0e803cc2dc22b067504706_76.png)

### 切行与切列

![](img/a2b67f42bd0e803cc2dc22b067504706_78.png)

*   **切行**：直接在中括号内使用切片。
    ```python
    df[0:2]  # 切取第0行到第1行（前两行）
    ```
*   **切列**：必须结合 `.iloc` 或 `.loc`，在逗号后指定列的切片。
    ```python
    df.iloc[:, 0:2]  # 切取所有行的前两列（0列和1列）
    # df.loc[:, ‘A‘:‘C‘]  # 切取所有行，从‘A‘列到‘C‘列（显示索引切片）
    ```

![](img/a2b67f42bd0e803cc2dc22b067504706_80.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_82.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_84.png)

## 索引与切片总结 📝

![](img/a2b67f42bd0e803cc2dc22b067504706_86.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_88.png)

以下是DataFrame索引与切片的核心方法总结：

![](img/a2b67f42bd0e803cc2dc22b067504706_90.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_92.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_94.png)

**索引操作：**
*   **取列**：`df[‘col‘]` (单列) 或 `df[[‘col1‘, ‘col2‘]]` (多列)。
*   **取行**：`df.iloc[row_index]` (隐式) 或 `df.loc[row_label]` (显示)。
*   **取元素**：`df.iloc[row_idx, col_idx]` 或 `df.loc[row_label, col_label]`。

![](img/a2b67f42bd0e803cc2dc22b067504706_96.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_98.png)

**切片操作：**
*   **切行**：`df[start:stop]`
*   **切列**：`df.iloc[:, start:stop]` 或 `df.loc[:, start_label:stop_label]`

![](img/a2b67f42bd0e803cc2dc22b067504706_100.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_102.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_104.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_106.png)

**关键点**：即使为DataFrame设置了显示索引，其隐式整数索引依然存在，可通过 `.iloc` 访问。

![](img/a2b67f42bd0e803cc2dc22b067504706_108.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_110.png)

## 实战练习：学生成绩处理 🧮

![](img/a2b67f42bd0e803cc2dc22b067504706_112.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_114.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_116.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_118.png)

让我们通过一个学生成绩处理的例子来巩固所学操作。

![](img/a2b67f42bd0e803cc2dc22b067504706_120.png)

```python
# 假设df是一个期中考试成绩表
期中 = df.copy()
期末 = df.copy()  # 假设期末成绩相同，仅用于演示

![](img/a2b67f42bd0e803cc2dc22b067504706_122.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_124.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_126.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_128.png)

# 1. 计算期中期末平均分
平均分 = (期中 + 期末) / 2
print(“平均分：\n“, 平均分)

![](img/a2b67f42bd0e803cc2dc22b067504706_130.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_132.png)

# 2. 将‘张三‘的‘数学‘成绩改为0 (假设‘张三‘是行索引，‘数学‘是列名)
# 期中.loc[‘张三‘, ‘数学‘] = 0

![](img/a2b67f42bd0e803cc2dc22b067504706_134.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_136.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_138.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_140.png)

# 3. 将‘李四‘的所有成绩加10分
# 期中[‘李四‘] += 10  # 如果‘李四‘是列名

![](img/a2b67f42bd0e803cc2dc22b067504706_142.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_144.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_146.png)

# 4. 所有学生所有科目加10分 (整体运算)
期中 += 10
print(“调整后期中成绩：\n“, 期中)
```

![](img/a2b67f42bd0e803cc2dc22b067504706_148.png)

## 常用附加操作

![](img/a2b67f42bd0e803cc2dc22b067504706_150.png)

### 转换时间序列

![](img/a2b67f42bd0e803cc2dc22b067504706_152.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_154.png)

在数据分析中，经常需要将字符串格式的日期转换为时间序列类型。

![](img/a2b67f42bd0e803cc2dc22b067504706_156.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_158.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_160.png)

```python
# 创建一个包含日期字符串的DataFrame
df_time = pd.DataFrame({
    ‘time‘: [‘2020-10-10‘, ‘2021-11-20‘, ‘2022-01-10‘],
    ‘value‘: [30, 33, 30]
})
print(df_time[‘time‘].dtype)  # 输出: object (字符串)

![](img/a2b67f42bd0e803cc2dc22b067504706_162.png)

# 使用pd.to_datetime进行转换
df_time[‘time‘] = pd.to_datetime(df_time[‘time‘])
print(df_time[‘time‘].dtype)  # 输出: datetime64[ns]
```

![](img/a2b67f42bd0e803cc2dc22b067504706_164.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_166.png)

### 设置索引

![](img/a2b67f42bd0e803cc2dc22b067504706_168.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_170.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_172.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_174.png)

可以将某一列设置为DataFrame的行索引，提升数据可读性。

![](img/a2b67f42bd0e803cc2dc22b067504706_176.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_178.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_180.png)

```python
# 将‘time‘列设置为行索引
df_time.set_index(‘time‘, inplace=True)
print(df_time)
# 现在，时间变成了行索引，数据更清晰。
```

![](img/a2b67f42bd0e803cc2dc22b067504706_182.png)

## 总结

![](img/a2b67f42bd0e803cc2dc22b067504706_184.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_186.png)

本节课中我们一起深入学习了DataFrame的索引与切片操作。我们明确了以下关键点：
1.  `df[ ]` 默认操作列，`df[]` 内放切片默认操作行。
2.  `.iloc[]` 和 `.loc[]` 是取行和精确索引的核心，前者基于整数位置，后者基于索引标签。
3.  通过逗号分隔可以同时指定行和列（如 `df.iloc[行, 列]`）。
4.  这些操作是数据查询、筛选和清洗的基础，务必通过练习熟练掌握。

![](img/a2b67f42bd0e803cc2dc22b067504706_188.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_190.png)

![](img/a2b67f42bd0e803cc2dc22b067504706_192.png)

在接下来的课程中，我们将通过更多实际案例来巩固这些核心操作技能。