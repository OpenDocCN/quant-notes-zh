# 量化交易实战：P21：Series对象核心总结 📊

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_1.png)

在本节课中，我们将对Pandas库中的第一个核心数据结构——Series对象进行全面的回顾与总结。我们将梳理其核心特性、操作方式以及数据处理技巧。



![](img/9cbd089e6630fbc8a8a2dd98e5432b97_3.png)

上一节我们详细讲解了Series的创建与基本操作，本节中我们来看看Series的核心特性总结。

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_5.png)

Series是一个一维的、带标签的数组。它可以被视为一个结合了**字典**（通过标签访问值）和**数组**（通过整数位置下标访问值）的集合体。



![](img/9cbd089e6630fbc8a8a2dd98e5432b97_7.png)

以下是Series的核心特性总结：

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_9.png)

*   **灵活的索引访问**：既可以通过类似数组的整数下标（如 `s[0]`）访问元素，也可以通过类似字典的标签（如 `s[‘a’]`）访问元素。
*   **支持向量化运算**：Series支持与NumPy数组类似的向量化操作，这使得数据处理高效且简洁。
    *   **与标量运算**：`s * 2` 会对Series中的每个元素乘以2。
    *   **Series间运算**：`s1 + s2` 会对两个Series进行对应元素的加法。
*   **继承NumPy功能**：Series继承了NumPy数组的许多强大功能，例如布尔索引、花式索引等。



![](img/9cbd089e6630fbc8a8a2dd98e5432b97_11.png)

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_13.png)

## 整数索引的歧义与解决方案 🔍

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_15.png)

当Series的索引标签本身也是整数时，使用中括号`[]`进行索引可能会产生歧义：它可能被解释为按**位置**索引，也可能被解释为按**标签**索引。

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_17.png)

为了解决这个问题，Pandas提供了两个明确的属性来消除歧义：

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_19.png)

*   **`.iloc`**：用于**纯基于整数位置**的索引。例如，`s.iloc[0]` 总是获取第一个元素，无论索引标签是什么。
*   **`.loc`**：用于**基于标签**的索引。例如，`s.loc[3]` 是获取索引标签为`3`的元素，而不是第四个元素。



![](img/9cbd089e6630fbc8a8a2dd98e5432b97_21.png)

## 数据对齐与缺失值处理 ⚖️

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_23.png)

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_25.png)

在上一部分我们解决了索引问题，本节中我们来看看Series运算中的一个重要特性：数据对齐。

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_27.png)

当两个Series对象进行算术运算（如加减乘除）时，Pandas会自动根据它们的**索引标签**进行对齐。只有在两个Series中拥有相同标签的数据才会进行运算。

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_29.png)

**对齐规则公式**：
`result = series1 + series2`
运算时，Pandas会先按标签对齐`series1`和`series2`，然后对标签匹配的值进行相加。

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_31.png)

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_33.png)

如果某个标签只存在于其中一个Series中，那么在结果Series中，该标签对应的值将是缺失值，表示为`NaN`（Not a Number）。



![](img/9cbd089e6630fbc8a8a2dd98e5432b97_34.png)

### 缺失值处理方法

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_36.png)

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_38.png)

出现缺失值`NaN`后，我们通常有两种处理方式：

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_40.png)

*   **删除缺失值**：使用`.dropna()`方法移除所有包含`NaN`的行。
    ```python
    cleaned_series = series_with_nan.dropna()
    ```
*   **填充缺失值**：使用`.fillna(value)`方法，将`NaN`替换为指定的值（如0、平均值等）。
    ```python
    filled_series = series_with_nan.fillna(0)
    ```



![](img/9cbd089e6630fbc8a8a2dd98e5432b97_42.png)

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_44.png)

## 总结 📝

![](img/9cbd089e6630fbc8a8a2dd98e5432b97_46.png)

本节课中我们一起学习了Pandas Series对象的核心知识。我们首先明确了Series是结合了字典和数组特性的带标签一维数据结构。接着，我们探讨了整数索引可能带来的歧义，并学习了使用`.iloc`和`.loc`来明确索引意图。然后，我们深入了解了Series运算中的“数据对齐”机制，以及由此产生的缺失值`NaN`问题，并掌握了使用`.dropna()`和`.fillna()`处理缺失数据的两种基本方法。掌握这些概念是熟练运用Pandas进行数据分析的基础。