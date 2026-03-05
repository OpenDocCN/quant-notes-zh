# 数据分析+金融量化+数据清洗：P30：03 DataFrame的构造 📊

在本节课中，我们将要学习Pandas中一个核心的数据结构——DataFrame。我们将了解它是什么，它与Series的关系，以及如何通过多种方式来构造一个DataFrame。

## 概述

DataFrame是Pandas提供的第二种数据类型，它本质上是基于Series构建的。Series只能处理一维数据，而DataFrame则将其扩展到了二维，使其能够处理像业务报表这样的二维表格数据。本节我们将深入探讨DataFrame的构造方法。

## DataFrame是什么？

上一节我们介绍了Series，它是一种一维带标签的数组。本节中我们来看看DataFrame，它是Series的二维扩展。

DataFrame是一个二维表格型数据结构。它由行和列组成，其中：
*   **行** 通常代表一条条数据记录。
*   **列** 通常代表数据的不同字段或属性。

例如，一个学生信息表，每一行是一个学生的信息，每一列是“学号”、“姓名”、“年龄”、“成绩”等字段。

### 与Series的关系

我们可以把DataFrame看作是一个**Series的容器**，这些Series是**竖着存放**的。理解这一点至关重要：
1.  每个Series成为DataFrame的一**列**。
2.  所有Series**共用相同的行索引（index）**。
3.  每个Series的**名字（name属性）** 成为DataFrame的**列索引（columns）**。

假设我们有两个Series，分别存储了Python和Java的成绩，它们有相同的行索引（学生姓名）。

```python
# 假设的Python成绩Series
python_series = pd.Series([90, 85, 78], index=['Lucy', 'Mary', 'Tom'], name='Python')
# 假设的Java成绩Series
java_series = pd.Series([88, 92, 80], index=['Lucy', 'Mary', 'Tom'], name='Java')
```

当我们将这两个Series组合成一个DataFrame时，结构如下：

| (行索引) | Python (列索引) | Java (列索引) |
| :------- | :-------------- | :------------ |
| Lucy     | 90              | 88            |
| Mary     | 85              | 92            |
| Tom      | 78              | 80            |

这样，我们就从一个一维的Series，获得了一个二维的DataFrame。DataFrame有三个核心部分：
*   **`index`**: 行索引/标签。
*   **`columns`**: 列索引/标签。
*   **`values`**: 表格中的值（数据部分）。

**重要提示**：DataFrame的每一列（即每个Series）的数据类型可以是不同的。例如，“姓名”列可能是字符串（object类型），“年龄”列是整数（int类型），“收入”列是浮点数（float类型）。这与NumPy的ndarray要求类型统一不同，更符合实际业务数据的多样性。

---

## 构造DataFrame的方法

了解了DataFrame的基本概念后，我们来看看如何创建它。以下是几种常见的构造方法。

### 方法一：使用列表或数组直接构造

这是最基础的方法，需要明确指定数据、行索引和列索引。

**核心参数**：
*   `data`: 二维数据，如列表的列表或NumPy的ndarray。
*   `index`: 行索引。
*   `columns`: 列索引。

**注意**：`data`的形状必须与`index`和`columns`构成的形状匹配。

```python
import pandas as pd
import numpy as np

# 1. 准备数据、行索引和列索引
data = np.random.randint(0, 100, size=(6, 2))  # 生成6行2列的随机整数数组
index = ['Lucy', 'Mary', 'Tom', 'Jack', 'Rose', 'Tony']  # 行索引
columns = ['Python', 'Java']  # 列索引

# 2. 构造DataFrame
df1 = pd.DataFrame(data=data, index=index, columns=columns)
print(df1)
```

我们可以使用`.dtypes`属性查看每一列的数据类型。
```python
print(df1.dtypes)  # 可能输出：Python int32, Java int32
```

### 方法二：使用字典构造

这种方法更直观，字典的键会成为DataFrame的列索引，字典的值（通常是一维数组）会成为对应列的数据。

```python
# 使用字典构造，键是列名，值是列数据
data_dict = {
    'Python': np.random.randint(0, 100, 6),  # 长度为6的一维数组
    'Java': np.random.random(6)  # 长度为6的一维浮点数数组，类型与Python列不同
}

![](img/296f8c6a958150ea0be14b86095bf183_1.png)

![](img/296f8c6a958150ea0be14b86095bf183_3.png)

df2 = pd.DataFrame(data=data_dict, index=index)  # index同上
print(df2)
print(df2.dtypes)  # Python列是int，Java列是float64
```

**提示**：在实际业务表中，像“姓名”这样的信息通常作为一列数据（字段），而不是行索引。

### 方法三：从文件读取（最常用）

在实际数据分析工作中，数据通常存储在文件中。Pandas可以轻松读取多种格式。

以下是读取Excel文件的示例：

```python
# 读取Excel文件
# `io`参数是文件路径
# `sheet_name`参数指定要读取的工作表，可以用索引（如0,1）或名称
df_excel = pd.read_excel(io='hero_list.xlsx', sheet_name=0)
print(df_excel.head())  # 查看前几行
```

`read_excel`函数还有一些常用参数：
*   `header`: 指定哪一行（从0开始）作为列名。`header=None`表示没有列名。
*   `index_col`: 指定哪一列（从0开始）作为行索引。

```python
# 假设文件第一行是数据，我们想指定第一行（索引0）为列名
df_excel2 = pd.read_excel('file.xlsx', header=0)
# 假设我们想把第一列作为行索引
df_excel3 = pd.read_excel('file.xlsx', index_col=0)
```

除了Excel，读取CSV文件也非常普遍：
```python
df_csv = pd.read_csv('data.csv')
```

### 方法四：由Series转换或扩展创建

单个Series可以转换成一个单列的DataFrame。

```python
# 创建一个Series
python_s = pd.Series([90, 85, 78], index=['Lucy', 'Mary', 'Tom'], name='Python成绩')
# 转换为DataFrame
df_from_series = pd.DataFrame(python_s)
print(df_from_series)  # 列名就是Series的name
```

我们也可以先创建一个空的或已有的DataFrame，然后像字典一样扩展新的列。

```python
# 创建一个空的DataFrame，指定行索引
df_empty = pd.DataFrame(index=['Lucy', 'Mary', 'Tom'])
# 像字典一样添加新列
df_empty['Python'] = [90, 85, 78]
df_empty['Java'] = [88, 92, 80]
print(df_empty)
```

类似地，Series也支持通过赋值新索引来扩展数据。

---

## 练习与示例

让我们通过一个练习来巩固以上方法。目标是根据下面的成绩表创建一个DataFrame。

|        | 张三 | 李四 |
| :----- | :--- | :--- |
| 语文   | 150  | 0    |
| 数学   | 150  | 0    |
| 英语   | 150  | 0    |
| 理综   | 300  | 0    |

**解法1：直接构造**
```python
data = [[150, 0], [150, 0], [150, 0], [300, 0]]
index = ['语文', '数学', '英语', '理综']
columns = ['张三', '李四']
df_exercise1 = pd.DataFrame(data=data, index=index, columns=columns)
```

**解法2：字典构造**
```python
data_dict = {
    '张三': [150, 150, 150, 300],
    '李四': [0, 0, 0, 0]
}
df_exercise2 = pd.DataFrame(data=data_dict, index=index)
```

**解法3：文件读取**（假设已将表格保存为`score.xlsx`）
```python
df_exercise3 = pd.read_excel('score.xlsx', index_col=0) # 第一列作为行索引
```

**解法4：扩展创建**
```python
df_exercise4 = pd.DataFrame(index=index)
df_exercise4['张三'] = [150, 150, 150, 300]
df_exercise4['李四'] = [0, 0, 0, 0]
```

---

## 总结

本节课中我们一起学习了Pandas DataFrame的核心概念与构造方法。
*   **DataFrame的本质**：是一个二维表格，可视为共用行索引的Series的容器，每列数据类型可不同。
*   **四种主要构造方法**：
    1.  **直接构造**：使用`pd.DataFrame(data, index, columns)`，需注意形状匹配。
    2.  **字典构造**：键为列名，值为列数据，非常直观。
    3.  **文件读取**：使用`pd.read_excel()`或`pd.read_csv()`，是实际工作中最主要的数据来源。
    4.  **转换与扩展**：将Series转为DataFrame，或向现有DataFrame中添加新列。

![](img/296f8c6a958150ea0be14b86095bf183_5.png)

掌握如何创建DataFrame是进行后续数据操作、清洗和分析的第一步。请务必熟悉这几种方法，并尝试在练习中灵活运用。