# 量化交易：Python入门之数据分析【1／4】：1.4 数组的复制与迭代 🔄

![](img/d3cd592e4396ecb8b3270a5da950de0d_1.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_3.png)

在本节课中，我们将要学习NumPy中两个重要的操作：数组的复制与迭代。理解如何正确复制数组可以避免数据被意外修改，而掌握迭代方法则是高效处理数组数据的基础。

![](img/d3cd592e4396ecb8b3270a5da950de0d_5.png)

## 数组的深度复制

![](img/d3cd592e4396ecb8b3270a5da950de0d_7.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_9.png)

上一节我们介绍了数组的切片操作。本节中我们来看看直接使用切片赋值可能带来的问题，以及如何通过深度复制来避免它。

如果让一个变量 `R2` 等于数组 `R` 的一部分切片。

![](img/d3cd592e4396ecb8b3270a5da950de0d_11.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_12.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_13.png)

```python
R2 = R[:2, :3]
```

![](img/d3cd592e4396ecb8b3270a5da950de0d_15.png)

那么 `R2` 是 `R` 矩阵的一个子集。例如，`R` 可能是一个从0开始计数的矩阵。

![](img/d3cd592e4396ecb8b3270a5da950de0d_17.png)

如果我们将 `R2` 中所有的数值都变为0。

![](img/d3cd592e4396ecb8b3270a5da950de0d_19.png)

```python
R2[:] = 0
```

![](img/d3cd592e4396ecb8b3270a5da950de0d_21.png)

按照一般逻辑，这应该只影响 `R2`，而不影响原始数组 `R`。但在Python的NumPy中，**切片创建的是原数组的一个视图（view），而非副本**。因此，修改 `R2` 会导致原始数组 `R` 中对应的部分也被修改。

![](img/d3cd592e4396ecb8b3270a5da950de0d_22.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_24.png)

为了让 `R2` 成为一个完全独立的新数组，我们需要使用 `copy` 函数进行深度拷贝。

![](img/d3cd592e4396ecb8b3270a5da950de0d_26.png)

```python
R2_copy = R.copy()
```

![](img/d3cd592e4396ecb8b3270a5da950de0d_28.png)

这样，对 `R2_copy` 的修改就不会影响到 `R`。例如，将 `R2_copy` 的所有数都换成100。

![](img/d3cd592e4396ecb8b3270a5da950de0d_30.png)

```python
R2_copy[:] = 100
```

![](img/d3cd592e4396ecb8b3270a5da950de0d_32.png)

此时，`R2_copy` 的值都变成了100，但 `R` 没有发生任何变化。

![](img/d3cd592e4396ecb8b3270a5da950de0d_33.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_35.png)

**核心要点**：当你需要基于一个数组的切片建立一个新的、独立的矩阵时，务必使用 `copy()` 函数进行深度拷贝，而不要直接使用切片赋值。

## 迭代数组

![](img/d3cd592e4396ecb8b3270a5da950de0d_37.png)

接下来，我们学习如何遍历（迭代）数组中的元素。这是数据处理中非常常见的操作。

![](img/d3cd592e4396ecb8b3270a5da950de0d_39.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_41.png)

首先，我们创建一个4行3列的随机整数数组作为示例。

```python
test = np.random.randint(0, 10, size=(4, 3))
```

![](img/d3cd592e4396ecb8b3270a5da950de0d_43.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_45.png)

以下是几种常见的迭代方法：

![](img/d3cd592e4396ecb8b3270a5da950de0d_46.png)

### 按行迭代

![](img/d3cd592e4396ecb8b3270a5da950de0d_48.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_50.png)

如果你想逐行处理数组，可以这样写：

![](img/d3cd592e4396ecb8b3270a5da950de0d_52.png)

```python
for row in test:
    print(row)
```
在这个 `for` 循环中，变量 `row` 会依次代表数组的每一行。循环结束后，变量 `row` 会保留最后一行的值。

![](img/d3cd592e4396ecb8b3270a5da950de0d_54.png)

### 按索引迭代

如果你想在迭代时同时获得行的索引，可以使用 `range` 函数。

![](img/d3cd592e4396ecb8b3270a5da950de0d_56.png)

```python
for i in range(len(test)):
    print(i)          # 打印行索引
    print(test[i])    # 打印对应行的数据
```
这里，`len(test)` 获取数组的行数（4），`range(4)` 生成序列 `[0, 1, 2, 3]`，`i` 依次取这些值，从而通过 `test[i]` 访问每一行。

![](img/d3cd592e4396ecb8b3270a5da950de0d_58.png)

### 使用 enumerate 函数（推荐）

![](img/d3cd592e4396ecb8b3270a5da950de0d_60.png)

更Pythonic的方式是使用 `enumerate` 函数，它可以同时返回索引和值。

![](img/d3cd592e4396ecb8b3270a5da950de0d_62.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_64.png)

```python
for i, row in enumerate(test):
    print(i, row)
```
这种方式代码更简洁，是迭代时同时需要索引和值的**最佳实践**。

![](img/d3cd592e4396ecb8b3270a5da950de0d_66.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_68.png)

### 同时迭代多个数组

![](img/d3cd592e4396ecb8b3270a5da950de0d_70.png)

使用 `zip` 函数可以同时循环两个或多个数组。

![](img/d3cd592e4396ecb8b3270a5da950de0d_72.png)

```python
test_two = test**2  # 创建test中每个元素的平方组成的数组
for a, b in zip(test, test_two):
    print(a, b)
```
这样，在每次循环中，`a` 和 `b` 分别对应 `test` 和 `test_two` 的同一行。

![](img/d3cd592e4396ecb8b3270a5da950de0d_74.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_75.png)

### 扁平化迭代与跨步迭代

![](img/d3cd592e4396ecb8b3270a5da950de0d_77.png)

对于多维数组，有时我们需要将其扁平化（展平为一维）后进行迭代，或者按特定步长跳过一些元素。

![](img/d3cd592e4396ecb8b3270a5da950de0d_79.png)

![](img/d3cd592e4396ecb8b3270a5da950de0d_81.png)

```python
arr = np.arange(36).reshape((6, 6))  # 创建一个6x6的数组
# 扁平化迭代
for element in arr.flat:
    print(element)
# 跨步迭代（例如，打印对角线元素）
for i in range(0, 36, 7):
    print(arr.flat[i])
```
`arr.flat` 是数组的扁平迭代器。`range(0, 36, 7)` 指定从0开始，到36结束，步长为7，这正好取出了6x6矩阵主对角线上的元素。

![](img/d3cd592e4396ecb8b3270a5da950de0d_83.png)

## 总结

![](img/d3cd592e4396ecb8b3270a5da950de0d_84.png)

本节课中我们一起学习了NumPy数组的两个关键操作：
1.  **数组复制**：理解了直接切片赋值会创建视图，修改视图会影响原数组。要创建独立副本，必须使用 `copy()` 方法。
2.  **数组迭代**：掌握了按行迭代、按索引迭代、使用 `enumerate` 同时获取索引和值、使用 `zip` 同时迭代多个数组，以及进行扁平化和跨步迭代等多种方法。

![](img/d3cd592e4396ecb8b3270a5da950de0d_86.png)

这些是进行数据分析前必须掌握的基础操作。第一个模块的内容到此结束，后续会提供相应的练习作业供大家巩固所学知识。