# Python机器学习与量化交易：P14：13 金融量化分析-NumPy数组布尔型索引 📊

![](img/6bb45d741ff0afd332af0b7f6c887f59_1.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_3.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_5.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_6.png)

在本节课中，我们将学习NumPy中一个非常强大且高效的功能——布尔型索引。它允许我们根据条件快速筛选数组中的元素，比传统的Python方法简洁得多。

![](img/6bb45d741ff0afd332af0b7f6c887f59_8.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_10.png)

## 概述

![](img/6bb45d741ff0afd332af0b7f6c887f59_12.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_14.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_16.png)

上一节我们介绍了NumPy数组的常规索引和切片。本节中，我们来看看如何使用布尔型索引进行条件筛选。其核心思想是：通过一个布尔值（True/False）数组来选取原数组中对应位置为True的元素。

![](img/6bb45d741ff0afd332af0b7f6c887f59_18.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_20.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_22.png)

## 布尔型索引的原理

布尔型索引的原理分为两步：
1.  **生成布尔数组**：通过数组与标量的比较运算，得到一个与原数组形状相同、元素为布尔值的数组。
2.  **应用索引**：将这个布尔数组放入原数组的方括号 `[]` 中，NumPy会自动返回所有对应位置为 `True` 的元素。

![](img/6bb45d741ff0afd332af0b7f6c887f59_24.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_26.png)

### 基础示例

![](img/6bb45d741ff0afd332af0b7f6c887f59_28.png)

假设我们有一个数组 `A`，目标是选出所有大于5的数。

![](img/6bb45d741ff0afd332af0b7f6c887f59_30.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_31.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_33.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_35.png)

**传统Python方法（使用 `filter` 和 `lambda`）：**
```python
A_list = [8, 3, 6, 2, 9, 1]
result = list(filter(lambda x: x > 5, A_list))
print(result)  # 输出: [8, 6, 9]
```

![](img/6bb45d741ff0afd332af0b7f6c887f59_37.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_39.png)

**NumPy布尔型索引方法：**
```python
import numpy as np
A = np.array([8, 3, 6, 2, 9, 1])
result = A[A > 5]
print(result)  # 输出: [8 6 9]
```
可以看到，NumPy的方法 `A[A > 5]` 更加简洁直观。

![](img/6bb45d741ff0afd332af0b7f6c887f59_41.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_43.png)

### 分步解析

![](img/6bb45d741ff0afd332af0b7f6c887f59_45.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_47.png)

1.  `A > 5` 是一个数组与标量的比较运算，它会返回一个布尔数组：
    ```python
    bool_array = A > 5
    print(bool_array)  # 输出: [ True False  True False  True False]
    ```
2.  将布尔数组 `bool_array` 作为索引传递给原数组 `A`：
    ```python
    result = A[bool_array]
    print(result)  # 输出: [8 6 9]
    ```
    这行代码的含义是：取出 `A` 中所有在 `bool_array` 里对应位置为 `True` 的元素。

![](img/6bb45d741ff0afd332af0b7f6c887f59_49.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_51.png)

## 组合条件筛选

![](img/6bb45d741ff0afd332af0b7f6c887f59_53.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_55.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_56.png)

布尔型索引的强大之处在于可以方便地组合多个筛选条件。

![](img/6bb45d741ff0afd332af0b7f6c887f59_58.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_60.png)

### “与”条件 (AND)

![](img/6bb45d741ff0afd332af0b7f6c887f59_62.png)

例如，要筛选出数组中**大于5的偶数**。
我们不能写成 `A[A > 5][A % 2 == 0]`，因为第二次索引时数组长度已经改变，会导致错误。

正确的方法是使用 `&` 运算符组合条件，并**注意用括号将每个条件括起来**：
```python
# 筛选大于5的偶数
result = A[(A > 5) & (A % 2 == 0)]
print(result)  # 输出: [6]
```
**注意**：这里必须使用位运算符 `&` 来表示“与”逻辑，而不是Python关键字 `and`。同时，每个条件必须用括号括起来，以确保运算优先级正确。

![](img/6bb45d741ff0afd332af0b7f6c887f59_64.png)

### “或”条件 (OR)

如果要筛选出**大于5的数或者所有的偶数**（即满足任一条件即可），可以使用 `|` 运算符。
```python
# 筛选大于5的数或偶数
result = A[(A > 5) | (A % 2 == 0)]
print(result)  # 输出: [8 6 2 9]
```
这行代码会选出所有大于5的元素，以及所有是偶数的元素（包括那些不大于5的偶数，如2）。

![](img/6bb45d741ff0afd332af0b7f6c887f59_66.png)

## 总结

![](img/6bb45d741ff0afd332af0b7f6c887f59_68.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_70.png)

![](img/6bb45d741ff0afd332af0b7f6c887f59_72.png)

本节课我们一起学习了NumPy的布尔型索引。我们掌握了其两步原理：先通过比较运算生成布尔数组，再将其作为索引进行筛选。我们还学习了如何使用 `&` 和 `|` 运算符来组合多个筛选条件，实现复杂的“与”、“或”逻辑。这个功能在数据清洗和条件筛选场景中非常高效，能极大简化代码。