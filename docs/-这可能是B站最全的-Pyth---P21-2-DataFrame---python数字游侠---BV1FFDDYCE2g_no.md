# Python金融量化与业务数据分析：P21：DataFrame合并操作 📊

![](img/0f485c9903ae290f08f0ff9b4aca74f5_1.png)

在本节课中，我们将要学习Pandas中DataFrame的合并（merge）操作。我们将理解合并与拼接（concat）的核心区别，掌握多种合并方式及其应用场景，并通过代码示例让初学者能够轻松上手。

## 合并与拼接的区别 🤔

上一节我们介绍了DataFrame的拼接操作，本节中我们来看看合并操作。合并与拼接听起来像是近义词，但它们的含义大有区别。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_3.png)

首先，拼接（concat）是将DataFrame进行横向或纵向的拼接，其操作载体是表格本身，即对两个表格进行结构上的连接。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_5.png)

而合并（merge）指的是将两个DataFrame当中的数据进行整合与合并。因此，合并对应的是数据内容的整合，而拼接对应的是表格结构的连接。你可以将合并操作类比为数据库（如MySQL）中的多表联查，它需要先将两张表中的数据基于某个条件合并到一起。

## 一对一数据合并 👥

以下是进行一对一合并的示例。我们有两个DataFrame：`df1` 和 `df2`。

```python
import pandas as pd

![](img/0f485c9903ae290f08f0ff9b4aca74f5_7.png)

# 创建示例DataFrame
df1 = pd.DataFrame({
    'employee': ['Bob', 'Jake', 'Lisa'],
    'group': ['Accounting', 'Engineering', 'Engineering']
})

df2 = pd.DataFrame({
    'employee': ['Bob', 'Jake', 'Lisa'],
    'hire_date': [2008, 2012, 2014]
})

![](img/0f485c9903ae290f08f0ff9b4aca74f5_9.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_11.png)

print("df1:")
print(df1)
print("\ndf2:")
print(df2)
```

![](img/0f485c9903ae290f08f0ff9b4aca74f5_13.png)

这两张表有一个共有的列 `employee`，我们可以基于此列将数据合并。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_15.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_17.png)

```python
# 使用merge函数进行合并，on参数指定合并条件
result = pd.merge(df1, df2, on='employee')
print("\n合并后的结果:")
print(result)
```

![](img/0f485c9903ae290f08f0ff9b4aca74f5_19.png)

运行上述代码，你会得到一个新的DataFrame，它包含三行三列，成功将`df1`中的`group`信息与`df2`中的`hire_date`信息基于`employee`合并到了一起。如果不写`on`参数，`merge`函数默认会将两张表中共有的列（此处是`employee`）作为合并条件。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_21.png)

## 一对多与多对多合并 🔄

![](img/0f485c9903ae290f08f0ff9b4aca74f5_23.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_25.png)

合并操作不仅限于一对一，也支持一对多和多对多的关系。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_27.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_29.png)

以下是一对多合并的示例：

```python
df3 = pd.DataFrame({
    'group': ['Accounting', 'Accounting', 'Engineering', 'Engineering', 'HR', 'HR'],
    'skills': ['math', 'spreadsheets', 'coding', 'linux', 'spreadsheets', 'organization']
})

df4 = pd.DataFrame({
    'group': ['Accounting', 'Engineering', 'HR'],
    'supervisor': ['Carly', 'Guido', 'Steve']
})

![](img/0f485c9903ae290f08f0ff9b4aca74f5_31.png)

print("df3 (一对多左表):")
print(df3)
print("\ndf4 (一对多右表):")
print(df4)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_33.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_35.png)

# 基于‘group’列进行一对多合并
result_one_to_many = pd.merge(df3, df4, on='group')
print("\n一对多合并结果:")
print(result_one_to_many)
```

![](img/0f485c9903ae290f08f0ff9b4aca74f5_37.png)

在这个例子中，右表`df4`中的一个`group`值（如‘Engineering’）对应左表`df3`中的多行数据，合并后会产生多行记录。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_39.png)

接下来是多对多合并的示例：

```python
df5 = pd.DataFrame({
    'group': ['Engineering', 'Engineering', 'HR', 'HR'],
    'hire_date': [2004, 2006, 2007, 2008]
})

![](img/0f485c9903ae290f08f0ff9b4aca74f5_41.png)

print("df1 (多对多左表):")
print(df1)
print("\ndf5 (多对多右表):")
print(df5)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_43.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_45.png)

# 基于‘group’列进行多对多合并
result_many_to_many = pd.merge(df1, df5, on='group')
print("\n多对多合并结果 (内连接):")
print(result_many_to_many)
```

在多对多合并中，左表和右表的`group`列都有重复值，合并时会生成所有可能的组合。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_47.png)

## 合并方式：`how` 参数详解 ⚙️

![](img/0f485c9903ae290f08f0ff9b4aca74f5_49.png)

`merge`函数中的`how`参数用于指定合并方式，类似于SQL中的连接类型。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_51.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_53.png)

以下是`how`参数的主要选项：
*   **`inner` (默认)**：内连接，只保留键值在两个表中都存在的行。
*   **`outer`**：外连接，保留两个表中所有的行，缺失值用NaN填充。
*   **`left`**：左连接，保留左表所有行，右表无匹配则填充NaN。
*   **`right`**：右连接，保留右表所有行，左表无匹配则填充NaN。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_55.png)

让我们用之前的`df1`和`df5`来演示不同连接方式的区别：

![](img/0f485c9903ae290f08f0ff9b4aca74f5_57.png)

```python
# 外连接 (outer)
result_outer = pd.merge(df1, df5, on='group', how='outer')
print("外连接 (outer) 结果:")
print(result_outer)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_59.png)

# 左连接 (left)
result_left = pd.merge(df1, df5, on='group', how='left')
print("\n左连接 (left) 结果:")
print(result_left)

# 右连接 (right)
result_right = pd.merge(df1, df5, on='group', how='right')
print("\n右连接 (right) 结果:")
print(result_right)
```

![](img/0f485c9903ae290f08f0ff9b4aca74f5_61.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_63.png)

通过对比输出结果，你可以清晰地看到不同连接方式对最终数据的影响。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_65.png)

## 指定左右键合并 🔑

![](img/0f485c9903ae290f08f0ff9b4aca74f5_67.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_69.png)

当两个需要合并的DataFrame没有同名的列，但包含可以关联的数据时，可以使用`left_on`和`right_on`参数分别指定左右表的键。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_71.png)

请看以下示例：

```python
df6 = pd.DataFrame({
    'name': ['Bob', 'Jake', 'Lisa', 'Sue'],
    'salary': [70000, 80000, 120000, 90000]
})

print("df1 (使用‘employee’列):")
print(df1)
print("\ndf6 (使用‘name’列):")
print(df6)

# 使用left_on和right_on指定不同名称的键进行合并
result_diff_keys = pd.merge(df1, df6, left_on='employee', right_on='name')
print("\n使用不同列名作为键的合并结果:")
print(result_diff_keys)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_73.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_75.png)

# 合并后可以删除多余的‘name’列
result_diff_keys = result_diff_keys.drop('name', axis=1)
print("\n删除多余列后的结果:")
print(result_diff_keys)
```

![](img/0f485c9903ae290f08f0ff9b4aca74f5_77.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_79.png)

## 总结与回顾 📝

![](img/0f485c9903ae290f08f0ff9b4aca74f5_81.png)

本节课中我们一起学习了DataFrame的核心操作之一：合并（merge）。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_83.png)

![](img/0f485c9903ae290f08f0ff9b4aca74f5_85.png)

我们首先明确了**合并与拼接（concat）的本质区别**：合并是对数据的整合，而拼接是对表格结构的连接。

接着，我们深入探讨了**多种合并场景**的实现方法：
*   使用 `pd.merge(left, right, on=‘key’)` 进行基于共同列的合并。
*   处理**一对一、一对多、多对多**的数据关系。
*   通过 `how` 参数灵活选择**内连接（inner）、外连接（outer）、左连接（left）、右连接（right）**。
*   当左右表键名不同时，使用 `left_on` 和 `right_on` 参数分别指定。

最后，我们了解到在实际数据分析中，选择合并还是拼接取决于具体需求。通常，如果两张表结构相似（列索引大部分相同），可能适合拼接；如果表结构不同但需要通过某些共同数据关联，则必须使用合并。

![](img/0f485c9903ae290f08f0ff9b4aca74f5_87.png)

在接下来的课程中，我们将在一个实际的人口数据分析案例中，综合运用合并、拼接以及之前学过的各种DataFrame操作来解决实际问题。