# Python金融量化分析：P11：NumPy数组的布尔型索引 🔍

![](img/a851f601954d78a28c4dca82d4ca2a95_1.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_2.png)

在本节课程中，我们将学习NumPy中一个非常强大且高效的功能——布尔型索引。它允许我们根据条件快速筛选数组中的元素，比传统的Python方法简洁得多。

![](img/a851f601954d78a28c4dca82d4ca2a95_4.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_6.png)

上一节我们介绍了NumPy数组的常规索引和切片，本节中我们来看看如何使用布尔值来“智能”地选择数据。

![](img/a851f601954d78a28c4dca82d4ca2a95_8.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_10.png)

## 布尔型索引的原理

![](img/a851f601954d78a28c4dca82d4ca2a95_12.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_14.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_15.png)

布尔型索引的原理可以分为两步。第一步是进行数组与标量的比较运算，得到一个布尔值数组。第二步是将这个布尔数组传入原始数组的索引中，从而筛选出对应位置为`True`的元素。

![](img/a851f601954d78a28c4dca82d4ca2a95_17.png)

以下是其核心工作原理的代码描述：

![](img/a851f601954d78a28c4dca82d4ca2a95_19.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_21.png)

```python
import numpy as np

# 第一步：数组与标量比较，生成布尔数组
arr = np.array([8, 3, 6, 2, 9])
bool_mask = arr > 5  # 结果为 [True, False, True, False, True]

![](img/a851f601954d78a28c4dca82d4ca2a95_23.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_25.png)

# 第二步：使用布尔数组进行索引，返回True位置对应的元素
result = arr[bool_mask]  # 结果为 [8, 6, 9]
```

## 对比传统方法

![](img/a851f601954d78a28c4dca82d4ca2a95_27.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_29.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_30.png)

为了理解布尔型索引的便捷性，我们先看看使用Python内置`filter`函数如何实现。

![](img/a851f601954d78a28c4dca82d4ca2a95_32.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_33.png)

以下是使用`filter`函数筛选大于5的数的示例：

![](img/a851f601954d78a28c4dca82d4ca2a95_34.png)

```python
# 使用Python列表和filter函数
a_list = [8, 3, 6, 2, 9]
filtered_list = list(filter(lambda x: x > 5, a_list))  # 结果为 [8, 6, 9]
```

![](img/a851f601954d78a28c4dca82d4ca2a95_36.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_38.png)

可以看到，使用NumPy的布尔型索引 `arr[arr > 5]` 更加简洁直观。

![](img/a851f601954d78a28c4dca82d4ca2a95_40.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_42.png)

## 组合条件筛选

![](img/a851f601954d78a28c4dca82d4ca2a95_44.png)

布尔型索引的强大之处在于可以方便地组合多个筛选条件。

![](img/a851f601954d78a28c4dca82d4ca2a95_46.png)

### “与”条件筛选

![](img/a851f601954d78a28c4dca82d4ca2a95_48.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_50.png)

例如，要筛选出数组中所有**大于5的偶数**，这需要同时满足两个条件。

![](img/a851f601954d78a28c4dca82d4ca2a95_52.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_54.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_56.png)

以下是实现“与”条件筛选的代码：

![](img/a851f601954d78a28c4dca82d4ca2a95_58.png)

```python
arr = np.array([8, 3, 6, 2, 9, 4, 7])
# 使用 & 运算符组合条件，注意每个条件要用括号括起来
result = arr[(arr > 5) & (arr % 2 == 0)]  # 结果为 [8, 6]
```

![](img/a851f601954d78a28c4dca82d4ca2a95_59.png)

**注意**：必须使用位运算符 `&` 来表示“与”关系，而不是Python关键字 `and`。同时，每个条件必须用括号括起来，以确保运算优先级正确。

### “或”条件筛选

如果要筛选出数组中**大于5的数或所有的偶数**，即满足任一条件即可。

以下是实现“或”条件筛选的代码：

![](img/a851f601954d78a28c4dca82d4ca2a95_61.png)

```python
arr = np.array([8, 3, 6, 2, 9, 4, 7])
# 使用 | 运算符组合“或”条件
result = arr[(arr > 5) | (arr % 2 == 0)]  # 结果为 [8, 6, 2, 9, 4]
```

![](img/a851f601954d78a28c4dca82d4ca2a95_63.png)

这里使用位运算符 `|` 来表示“或”关系。

![](img/a851f601954d78a28c4dca82d4ca2a95_65.png)

## 总结

![](img/a851f601954d78a28c4dca82d4ca2a95_67.png)

![](img/a851f601954d78a28c4dca82d4ca2a95_68.png)

本节课中我们一起学习了NumPy的布尔型索引。我们了解了它的两步工作原理：先通过比较运算生成布尔掩码，再利用该掩码索引原数组。我们对比了其与传统Python过滤方法的优劣，并重点学习了如何使用 `&` 和 `|` 运算符来组合多个条件进行复杂筛选。掌握布尔型索引能极大提升数据处理的效率和代码的可读性，是量化分析中的一项基础而重要的技能。