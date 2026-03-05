# 量化交易：Python入门之数据分析【2/4】：2-1. Python数据分析：Module2 - Pandas Series 📊

在本节课中，我们将要学习Pandas库的核心数据结构之一：Series。Series是构建Pandas数据分析能力的基础，理解它对于后续处理金融数据至关重要。

上一节我们介绍了NumPy数组，本节中我们来看看Pandas Series。Pandas是一个非常重要的数据分析库，在量化金融和数据分析领域应用广泛。它类似于Excel，用于处理面板数据。

首先，我们需要导入Pandas库。

![](img/772fd6094608feb31d2241903369e593_1.png)

![](img/772fd6094608feb31d2241903369e593_2.png)

```python
import pandas as pd
```

## 创建Series对象

![](img/772fd6094608feb31d2241903369e593_4.png)

我们可以用多种方式创建Series。首先，用一个包含字符串的列表来创建。

![](img/772fd6094608feb31d2241903369e593_6.png)

```python
fruits = pd.Series(['Tomato', 'Banana', 'Apple'])
print(fruits)
```

![](img/772fd6094608feb31d2241903369e593_8.png)

Series对象由索引（index）和值（values）两部分组成。如果没有指定索引，Pandas会自动生成从0开始的整数索引。

![](img/772fd6094608feb31d2241903369e593_10.png)

Series也可以存储数字。

![](img/772fd6094608feb31d2241903369e593_12.png)

```python
numbers = pd.Series([1, 2, 3])
print(numbers)
```

![](img/772fd6094608feb31d2241903369e593_14.png)

![](img/772fd6094608feb31d2241903369e593_15.png)

## 处理缺失值

![](img/772fd6094608feb31d2241903369e593_17.png)

在实际数据中，经常会遇到缺失值。Pandas使用`NaN`（Not a Number）来表示数字类型的缺失值，使用`None`来表示对象（如字符串）类型的缺失值。

以下是包含缺失值的Series示例：

![](img/772fd6094608feb31d2241903369e593_19.png)

![](img/772fd6094608feb31d2241903369e593_21.png)

```python
animals_with_none = pd.Series(['Tomato', 'Banana', None])
print(animals_with_none)

numbers_with_nan = pd.Series([1, 2, None])
print(numbers_with_nan)
```

![](img/772fd6094608feb31d2241903369e593_23.png)

需要注意的是，`NaN`和`None`并不相等，甚至两个`NaN`之间也不相等。

![](img/772fd6094608feb31d2241903369e593_25.png)

```python
import numpy as np
print(np.nan == None)   # 输出：False
print(np.nan == np.nan) # 输出：False
```

![](img/772fd6094608feb31d2241903369e593_27.png)

那么，如何判断一个值是否为缺失值呢？我们需要使用NumPy的`isnan()`函数。

![](img/772fd6094608feb31d2241903369e593_29.png)

```python
print(np.isnan(np.nan)) # 输出：True
```

![](img/772fd6094608feb31d2241903369e593_31.png)

![](img/772fd6094608feb31d2241903369e593_33.png)

## 使用字典创建Series并指定索引

![](img/772fd6094608feb31d2241903369e593_34.png)

我们可以使用字典来创建Series，字典的键（key）会自动成为Series的索引。

```python
sports_dict = {'tennis': 'USA', 'baseball': 'Japan', 'soccer': 'Brazil'}
s = pd.Series(sports_dict)
print(s)
print(s.index)  # 查看索引
print(s.values) # 查看值
```

![](img/772fd6094608feb31d2241903369e593_36.png)

我们也可以在创建Series时，显式地指定数据和索引。以下两种方式是等价的。

![](img/772fd6094608feb31d2241903369e593_38.png)

![](img/772fd6094608feb31d2241903369e593_39.png)

方式一：先创建字典，再转换为Series。
```python
animals_dict = {'USA': 'tiger', 'China': 'panda', 'Canada': 'fox'}
s1 = pd.Series(animals_dict)
print(s1)
```

![](img/772fd6094608feb31d2241903369e593_41.png)

![](img/772fd6094608feb31d2241903369e593_43.png)

方式二：在`pd.Series()`函数中直接传入数据列表和索引列表。
```python
s2 = pd.Series(['tiger', 'panda', 'fox'], index=['USA', 'China', 'Canada'])
print(s2)
```

![](img/772fd6094608feb31d2241903369e593_45.png)

## 从字典中选择部分数据创建Series

![](img/772fd6094608feb31d2241903369e593_47.png)

有时，我们可能只想从字典中选择特定的键值对来创建Series。这可以通过在创建时传入一个索引列表来实现，Pandas会只选取该列表中存在的键。

```python
# 原始字典
sports_dict = {'tennis': 'USA', 'baseball': 'Japan', 'soccer': 'Brazil'}
# 只选择 ‘baseball‘ 和 ‘soccer‘ 对应的数据
selected_sports = pd.Series(sports_dict, index=['baseball', 'soccer'])
print(selected_sports)
```

![](img/772fd6094608feb31d2241903369e593_49.png)

运行上述代码，将只输出`baseball`和`soccer`对应的值，而`tennis`被忽略。

![](img/772fd6094608feb31d2241903369e593_51.png)

本节课中我们一起学习了Pandas Series的基本概念和创建方法。我们了解到Series是一种带标签的一维数组，是Pandas处理数据的基石。我们学会了如何从列表和字典创建Series，如何处理缺失值，以及如何有选择地从字典中构建Series。掌握这些是进行高效数据分析的第一步。在下一节，我们将学习另一个更强大的数据结构：DataFrame。