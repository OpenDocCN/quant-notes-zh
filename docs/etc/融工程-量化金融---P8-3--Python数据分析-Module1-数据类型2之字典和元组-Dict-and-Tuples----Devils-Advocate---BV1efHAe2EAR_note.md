# 量化交易：Python入门之数据分析【1／4】：3. Python数据分析：Module1-数据类型2之字典和元组(Dict and Tuples) 📚

在本节课中，我们将要学习Python中两种重要的数据结构：字典（Dict）和元组（Tuples）。理解它们与之前学过的列表（List）的区别和各自的应用场景，是进行数据分析的基础。

## 概述

上一节我们介绍了数据类型和列表。本节中，我们来看看字典和元组。字典是Python中非常常用的功能，它以键值对的形式存储数据。元组则与列表类似，但具有不可变的特性。掌握这三种数据结构（列表、字典、元组）对于后续学习至关重要。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_1.png)

## 字典（Dict）的结构与创建

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_2.png)

字典使用大括号 `{}` 定义，这与列表使用的中括号 `[]` 不同。字典由**键（Key）**和**值（Value）**两部分组成，形成键值对。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_4.png)

例如，创建一个存储联系人邮箱的字典：

```python
x = {
    "小明李": "xiaomingli@xxuniversity.edu",
    "Aone Mask": "aone.mask@tesla.com"
}
```

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_6.png)

在这个字典中，`"小明李"` 和 `"Aone Mask"` 是键（Key），后面的邮箱地址是值（Value）。值可以是字符串、数字、列表等多种数据类型。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_8.png)

## 字典的基本操作

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_10.png)

### 访问值
通过键来访问对应的值。

```python
print(x["小明李"])  # 输出：xiaomingli@xxuniversity.edu
```

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_12.png)

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_13.png)

### 添加或修改键值对
直接为新的键赋值即可添加新条目；为已存在的键赋值则会修改其对应的值。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_15.png)

```python
x["alice王"] = None  # 添加新键值对，值为None
x["小明李"] = "???"   # 修改已有键对应的值
```

`None` 在Python中表示“无”或“空”。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_17.png)

## 遍历字典

以下是遍历字典的几种常用方法。

### 遍历所有键（Key）
使用 `for key in dict` 循环可以遍历字典的所有键。

```python
for name in x:
    print(x[name])
```
或者更明确地使用 `.keys()` 方法：
```python
for key in x.keys():
    print(x[key])
```

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_19.png)

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_21.png)

### 遍历所有值（Value）
使用 `.values()` 方法可以遍历字典的所有值。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_23.png)

```python
for email in x.values():
    print(email)
```
注意，`.values()` 返回的结果不能直接用索引选取，必须通过循环遍历。

### 同时遍历键和值
使用 `.items()` 方法可以同时获取键和值。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_25.png)

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_27.png)

```python
for key, value in x.items():
    print(key)
    print(value)
```
`.items()` 方法返回一个由元组组成的特殊视图，每个元组包含一个键值对。

**重要提示**：字典是无序的数据结构，不能像列表那样使用数字索引（如 `x[0]`）来选取元素，只能通过键或遍历来访问。

## 序列的打包与拆包

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_29.png)

Python允许将多个值“打包”到一个元组中，也可以将一个序列“拆包”到多个变量里。这在数据赋值时非常方便。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_31.png)

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_33.png)

```python
# 打包：将三个值放入一个元组
info = ("alice王", "王", "alice.wang@disruptive-algo.com")

# 拆包：将元组中的值分别赋给三个变量
first_name, last_name, email = info
```
拆包时，变量的数量必须与序列中元素的数量严格一致，否则会报错。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_35.png)

## 元组（Tuples）简介

元组使用圆括号 `()` 定义，它与列表相似，都是有序的、有索引的序列。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_37.png)

```python
x = (1, "A", 2, "B")
```

### 元组与列表、字典的核心区别
以下是三种数据结构的核心特性对比：

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_39.png)

1.  **列表（List）**: 使用方括号 `[]`。**有顺序，有索引，可以更改**。
    ```python
    a = [1, 2, 3]
    a[0] = 99  # 允许修改
    ```

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_40.png)

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_42.png)

2.  **字典（Dict）**: 使用大括号 `{}`。**无顺序，无索引，可以更改**。通过键值对访问。
    ```python
    b = {"key1": "value1"}
    b["key1"] = "new_value"  # 允许修改
    ```

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_43.png)

3.  **元组（Tuples）**: 使用圆括号 `()`。**有顺序，有索引，不可以更改**。
    ```python
    c = (1, 2, 3)
    # c[0] = 99  # 这行代码会报错，因为元组不可变
    ```

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_45.png)

元组的不可变性意味着一旦创建，其内容就不能被修改、添加或删除。这使得元组在某些需要数据保护或作为字典键的场景中非常有用。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_47.png)

## 总结

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_49.png)

本节课中我们一起学习了Python中两种关键的数据结构：
*   **字典（Dict）**：一种通过**键（Key）**来访问**值（Value）**的无序集合，使用大括号 `{}` 定义。我们学习了如何创建、访问、修改字典，以及如何遍历其键、值和键值对。
*   **元组（Tuples）**：一种有序但**不可变**的序列，使用圆括号 `()` 定义。我们了解了序列的打包与拆包操作，并重点比较了列表、字典和元组三者在**顺序、索引和可变性**上的核心区别。

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_51.png)

![](img/2ecdf0e6b3f5e5d17fa88b97fb8eb5e7_53.png)

理解并熟练运用列表、字典和元组这三种基本数据结构，是编写高效Python代码和进行数据分析的基石。在接下来的Module2中，我们将学习更复杂的数据结构，如类似Excel表格的面板数据。