# 数据分析+金融量化+数据清洗：P28：Series基本概念及访问 📊

![](img/d32859d2abd2f6cb2c16fedac759039a_0.png)

![](img/d32859d2abd2f6cb2c16fedac759039a_2.png)

在本节课中，我们将要学习Pandas库中一个核心的数据结构——Series。我们将了解它的基本概念、如何创建Series对象，以及如何通过索引和切片来访问其中的数据。掌握Series是进行数据分析的基础。

## Series的基本概念

上一节我们介绍了数据分析的常用工具，本节中我们来看看Pandas库中的Series类型。

Series是一个类似于一维数组的对象。它由两部分组成：一组数据（values）和与之关联的索引（index）。与普通数组或列表不同，Series的索引可以不是数字，而是有意义的标签（如姓名、科目），这使得数据的可读性更高，更贴合业务分析场景。

![](img/d32859d2abd2f6cb2c16fedac759039a_4.png)

![](img/d32859d2abd2f6cb2c16fedac759039a_6.png)

![](img/d32859d2abd2f6cb2c16fedac759039a_8.png)

Series具有以下三个核心特征：
1.  数据是有序的。
2.  可以通过位置索引（隐式索引）访问。
3.  可以通过标签索引（显示索引）访问。

## 如何构造Series

了解了Series的概念后，我们来看看如何创建它。Series的构造非常灵活。

以下是几种常见的构造方法：

*   **由列表或NumPy数组构造**
    这是最直接的方式。如果没有指定索引（index），Pandas会自动用0, 1, 2...作为其显示索引。
    ```python
    import pandas as pd
    import numpy as np

    # 使用列表构造
    data_list = [10, 20, 30, 40]
    s1 = pd.Series(data=data_list)
    print(s1)

    # 使用NumPy数组构造
    data_array = np.array([5, 15, 25, 35])
    s2 = pd.Series(data=data_array)
    print(s2)
    ```
    需要注意的是，用NumPy数组构造的Series与原始数组是**引用关系**，修改数组会影响Series。而用列表构造则是**副本关系**，互不影响。

*   **构造时指定索引和名称**
    我们可以显式地指定索引，让数据含义更清晰。还可以为整个Series设置一个名称。
    ```python
    data = np.random.randint(60, 100, size=5)
    index = ['Lucy', 'Mary', 'Tom', 'Jack', 'Tony']
    s3 = pd.Series(data=data, index=index, name='Score')
    print(s3)
    ```

*   **由字典构造**
    字典的键（key）会自动成为Series的显示索引，字典的值（value）成为数据。
    ```python
    data_dict = {'语文': 150, '数学': 150, '英语': 120, '理综': 300}
    s4 = pd.Series(data_dict)
    print(s4)
    ```
    如果同时指定了`index`参数，则会以指定的`index`为准。如果指定的索引在字典中不存在，对应位置会用`NaN`（空值）填充。

## Series的索引与切片

创建了Series之后，最重要的操作就是访问其中的数据。Series支持灵活多样的索引和切片方式。

上一节我们介绍了Series的构造，本节中我们来看看如何访问Series中的数据。

### 通过显示索引（标签）访问

显示索引使用我们定义的标签（如‘Lucy’， ‘语文’）进行访问。

以下是几种显示索引的访问方式：

*   **类似字典的访问**：`s[‘label’]`
*   **类似属性的访问**：`s.label`（仅当索引名是有效的变量名时可用）
*   **使用`.loc[]`访问（推荐）**：`s.loc[‘label’]`
*   **访问多个元素**：传入一个标签列表，如 `s.loc[[‘label1‘， ‘label2’]]`，返回一个新的Series。
*   **布尔索引**：通过一个布尔值列表或Series来筛选数据。这是数据分析中非常强大的功能。

```python
s = pd.Series(data=[85, 92, 78, 88], index=[‘A‘， ‘B‘， ‘C‘， ‘D’])

# 单元素访问
print(s[‘B‘]) # 输出：92
print(s.loc[‘B‘]) # 输出：92 (推荐)

# 多元素访问
print(s.loc[[‘A‘， ‘C‘]]) # 输出 A:85， C:78

# 布尔索引：找出大于80分的成绩
print(s[s > 80])
# 找出高于平均分的成绩
print(s[s > s.mean()])
```

### 通过隐式索引（位置）访问

隐式索引使用数据所在的位置（0， 1， 2...）进行访问，类似于列表或数组。

以下是隐式索引的访问方式：

*   **类似数组的访问**：`s[0]`
*   **使用`.iloc[]`访问（推荐）**：`s.iloc[0]`
*   **访问多个元素**：传入一个位置列表，如 `s.iloc[[0， 2]]`

```python
s = pd.Series(data=[85, 92, 78, 88], index=[‘A‘， ‘B‘， ‘C‘， ‘D’])

# 单元素访问
print(s.iloc[0]) # 输出：85 (第一个元素)
print(s[0]) # 输出：85

# 多元素访问
print(s.iloc[[0， 2]]) # 输出位置0和2的元素：85， 78
```

### Series的切片操作

切片用于获取一个连续的数据子集。

这里有一个关键区别：
*   使用 **`.loc[]`** 进行切片时，是基于**标签**的，且切片范围是**闭区间** `[start:end]`，即包含结束标签。
*   使用 **`.iloc[]`** 进行切片时，是基于**位置**的，且切片范围是**左闭右开区间** `[start:end)`，与Python列表切片规则一致。

```python
s = pd.Series(data=[10, 20, 30, 40, 50], index=[‘a‘， ‘b‘， ‘c‘， ‘d‘， ‘e’])

# 使用.loc[]标签切片 (闭区间)
print(s.loc[‘b‘：‘d‘]) # 输出 b， c， d 对应的值：20， 30， 40

# 使用.iloc[]位置切片 (左闭右开)
print(s.iloc[1:4]) # 输出位置1，2，3的值：20， 30， 40
```

**特别注意**：当Series的显示索引也是数字（如0，1，2...）时，`s.loc[1:3]` 和 `s.iloc[1:3]` 的结果可能不同，因为前者是标签闭区间，后者是位置左闭右开区间。

## 总结

![](img/d32859d2abd2f6cb2c16fedac759039a_10.png)

本节课中我们一起学习了Pandas核心数据结构Series。我们首先了解了Series是一个带标签的一维数组，它结合了数组的有序性和字典的键值对可读性。接着，我们掌握了通过列表、数组、字典等多种方式构造Series对象。最后，我们深入探讨了如何通过显示索引（`.loc[]`）和隐式索引（`.iloc[]`）来访问和切片Series中的数据，并特别强调了二者在切片时的区间差异。熟练掌握Series是后续学习更复杂的DataFrame和进行数据操作的基础。