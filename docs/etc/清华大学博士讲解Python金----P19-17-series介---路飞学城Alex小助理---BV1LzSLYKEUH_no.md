# Python金融量化分析：P19：Series介绍 📊

在本节课中，我们将要学习Pandas库中的第一个核心数据结构——Series。我们将了解它是什么，如何创建它，以及它如何结合了列表（数组）和字典的特性，从而在数据分析中提供强大的灵活性。

上一节我们介绍了NumPy，它是数据分析的基础包。本节中我们来看看Pandas，它在NumPy的基础上构建，提供了更高层次的封装，是数据分析中不可或缺的工具。Pandas的核心功能之一是提供了两种数据结构：DataFrame和Series。本章我们将重点介绍Series。

## 什么是Series？ 🤔

![](img/7ba47046890debfbf3070ae2d2daed5a_1.png)

Series是一种类似于一维数组的对象。它非常像一个数组和字典的结合体。

![](img/7ba47046890debfbf3070ae2d2daed5a_3.png)

我们可以通过导入Pandas并调用`pd.Series()`函数来创建一个Series对象。

```python
import pandas as pd
```

![](img/7ba47046890debfbf3070ae2d2daed5a_5.png)

以下是创建Series的几种方法：

![](img/7ba47046890debfbf3070ae2d2daed5a_7.png)

**1. 从列表创建**
```python
sr = pd.Series([2, 3, 4, 5])
print(sr)
```
输出结果左侧是默认的整数索引（0, 1, 2, 3），右侧是数据值。这类似于一个列表或数组。

**2. 从列表创建并指定索引（标签）**
```python
sr = pd.Series([2, 3, 4, 5], index=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(sr)
```
输出结果左侧变成了我们指定的索引（A, B, C, D），右侧是数据值。这类似于一个字典，索引是键（key），数据值是值（value）。

**3. 从NumPy数组创建**
Series也可以直接从NumPy数组创建，方法与从列表创建类似。

![](img/7ba47046890debfbf3070ae2d2daed5a_9.png)

**4. 从字典创建**
```python
sr = pd.Series({‘A‘: 2, ‘B‘: 3})
print(sr)
```
字典的键（key）会自动成为Series的索引（标签）。

![](img/7ba47046890debfbf3070ae2d2daed5a_11.png)

## Series的特性：继承自数组 📋

Series继承了列表或NumPy数组的许多特性，使其操作起来非常直观。

以下是Series支持的数组类操作：

![](img/7ba47046890debfbf3070ae2d2daed5a_13.png)

![](img/7ba47046890debfbf3070ae2d2daed5a_15.png)

**通过下标访问**
即使我们为Series指定了字母标签（如A, B, C, D），我们仍然可以通过原始的整数下标来访问数据。
```python
sr = pd.Series([2, 3, 4, 5], index=[‘A‘, ‘B‘, ‘C‘, ‘D‘])
print(sr[0])  # 输出：2
```

**与标量运算**
Series可以与一个数字进行运算，运算会应用到每个元素上。
```python
print(sr + 10)
```

**相同大小的Series间运算**
两个长度相同的Series可以进行逐元素的加减乘除等运算。
```python
sr1 = pd.Series([1, 2, 3])
sr2 = pd.Series([4, 5, 6])
print(sr1 + sr2)  # 输出：5, 7, 9
```

**切片操作**
和列表一样，Series支持切片。
```python
print(sr[0:2])  # 输出索引0和1对应的数据
```

**支持通用函数**
Series支持NumPy的通用函数，如取绝对值、最大值、最小值等。

**布尔型索引**
可以通过条件表达式来筛选数据。
```python
print(sr[sr > 4])  # 输出所有大于4的数据
```

## Series的特性：继承自字典 🔑

![](img/7ba47046890debfbf3070ae2d2daed5a_17.png)

![](img/7ba47046890debfbf3070ae2d2daed5a_19.png)

除了数组的特性，Series也融合了字典的一些有用功能。

以下是Series支持的字典类操作：

**通过标签访问**
这是Series作为“带标签的数组”的核心功能，可以通过我们自定义的索引（标签）来访问数据。
```python
print(sr[‘A‘])  # 输出标签‘A‘对应的值
```

**`in`操作**
可以检查某个标签是否存在于Series的索引中。
```python
print(‘A‘ in sr)  # 输出：True
print(‘Z‘ in sr)  # 输出：False
```
**注意**：对Series使用`for`循环时，遍历的是它的**值**，而不是键（索引）。这与遍历字典不同。

**获取索引和值**
可以通过`.index`和`.values`属性分别获取Series的索引和值。
```python
print(sr.index)  # 输出索引对象
print(sr.values) # 输出值数组
```

**花式索引与切片（按标签）**
可以通过传入一个标签列表来获取多个值，也支持按标签进行切片。
```python
# 花式索引
print(sr[[‘A‘, ‘C‘]])
# 按标签切片（注意：按标签切片是“前包后也包”的）
print(sr[‘A‘:‘C‘])  # 输出标签A、B、C对应的数据
```
**重要区别**：按整数下标切片是“前包后不包”，而按标签切片是“前包后也包”。

## Series的应用场景 💡

![](img/7ba47046890debfbf3070ae2d2daed5a_21.png)

Series结合了有序数组和快速键值查找的优点，在实际工作中非常有用。例如：
*   **时间序列数据**：记录一支股票每日的收盘价。索引是日期（标签），值是价格。我们既可以通过日期（标签）快速查找某天的价格，也可以通过整数位置（下标）获取前N天的数据切片。
*   **带标签的测量数据**：记录一系列实验样本在不同条件下的观测值。样本名称作为索引，观测值作为数据。这样既保持了顺序，又能通过样本名快速定位。

这解决了之前可能需要用“列表嵌套元组”来维护顺序和标签的麻烦，使数据访问更加高效和直观。

本节课中我们一起学习了Pandas的Series对象。我们了解了它是如何作为数组和字典的结合体而被创建的，探索了它从两者继承而来的核心特性，包括索引、切片、运算等。理解Series是学习更复杂的DataFrame结构的基础。在下一节中，我们将介绍Pandas的另一个核心数据结构——DataFrame。