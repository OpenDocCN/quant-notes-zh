# 金融量化分析：P14：13 金融量化分析-numpy-array布尔型索引 🔍

![](img/b6655ab931d17d8b46664aa2209c21c8_1.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_3.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_5.png)

在本节课中，我们将要学习NumPy中一个非常强大且实用的功能——布尔型索引。它允许我们根据条件快速、简洁地从数组中筛选出所需的数据，是数据分析中不可或缺的工具。

![](img/b6655ab931d17d8b46664aa2209c21c8_6.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_8.png)

上一节我们介绍了常规的索引和切片方法，本节中我们来看看如何使用布尔型索引进行更灵活的数据筛选。

![](img/b6655ab931d17d8b46664aa2209c21c8_10.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_12.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_14.png)

## 布尔型索引的原理

![](img/b6655ab931d17d8b46664aa2209c21c8_16.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_18.png)

布尔型索引的核心原理分为两步。第一步，通过数组与标量的比较运算，生成一个布尔数组。第二步，将这个布尔数组作为索引传入原数组，即可筛选出对应位置为`True`的元素。

![](img/b6655ab931d17d8b46664aa2209c21c8_20.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_22.png)

以下是其工作原理的公式化描述：
1.  生成布尔数组：`bool_array = (array > value)`
2.  应用索引筛选：`result = array[bool_array]`

## 基础用法示例

![](img/b6655ab931d17d8b46664aa2209c21c8_24.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_26.png)

让我们通过一个简单的例子来理解其用法。首先，我们创建一个数组。

![](img/b6655ab931d17d8b46664aa2209c21c8_28.png)

```python
import numpy as np

![](img/b6655ab931d17d8b46664aa2209c21c8_30.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_31.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_33.png)

# 创建一个示例数组
A = np.array([8, 3, 6, 2, 9, 1])
print("原始数组 A:", A)
```

![](img/b6655ab931d17d8b46664aa2209c21c8_35.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_37.png)

假设我们需要筛选出数组中所有大于5的数。使用Python基础的`filter`函数方法较为繁琐。

![](img/b6655ab931d17d8b46664aa2209c21c8_39.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_41.png)

```python
# 使用Python的filter方法
result_filter = list(filter(lambda x: x > 5, A))
print("使用filter筛选结果:", result_filter)
```

![](img/b6655ab931d17d8b46664aa2209c21c8_43.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_45.png)

而使用NumPy的布尔型索引，则异常简洁。

![](img/b6655ab931d17d8b46664aa2209c21c8_47.png)

```python
# 使用NumPy布尔型索引
result_numpy = A[A > 5]
print("使用布尔型索引筛选结果:", result_numpy)
```

![](img/b6655ab931d17d8b46664aa2209c21c8_49.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_51.png)

可以看到，`A[A > 5]`这一行代码就完成了所有工作。其内部执行过程正是我们前面提到的两步：`A > 5`生成布尔数组`[True, False, True, False, True, False]`，然后用这个数组对`A`进行索引。

![](img/b6655ab931d17d8b46664aa2209c21c8_53.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_55.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_56.png)

## 组合条件筛选

![](img/b6655ab931d17d8b46664aa2209c21c8_58.png)

在实际应用中，我们常常需要根据多个条件进行筛选。NumPy支持使用位运算符来组合多个布尔条件。

![](img/b6655ab931d17d8b46664aa2209c21c8_60.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_62.png)

以下是组合多个筛选条件的方法：

*   **与运算 (`&`)**：筛选同时满足多个条件的元素。例如，筛选大于5的偶数。
    ```python
    # 筛选大于5的偶数
    condition = (A > 5) & (A % 2 == 0)
    result = A[condition]
    print("大于5的偶数:", result)
    ```
    **注意**：必须使用位运算符`&`，而不是关键字`and`，并且每个条件需要用括号括起来。

*   **或运算 (`|`)**：筛选满足任意一个条件的元素。例如，筛选所有大于5的数或所有偶数。
    ```python
    # 筛选大于5的数或偶数
    condition = (A > 5) | (A % 2 == 0)
    result = A[condition]
    print("大于5的数或偶数:", result)
    ```
    **注意**：必须使用位运算符`|`，而不是关键字`or`。

![](img/b6655ab931d17d8b46664aa2209c21c8_64.png)

*   **非运算 (`~`)**：筛选不满足某个条件的元素。例如，筛选所有不大于5的数。
    ```python
    # 筛选不大于5的数（即小于等于5）
    condition = ~(A > 5)
    result = A[condition]
    print("不大于5的数:", result)
    ```

![](img/b6655ab931d17d8b46664aa2209c21c8_66.png)

## 总结

本节课中我们一起学习了NumPy的布尔型索引。我们首先了解了其两步工作原理：先通过比较运算生成布尔数组，再将其作为索引进行筛选。然后，我们通过实例对比了其相对于传统`filter`方法的简洁性。最后，我们重点学习了如何使用位运算符`&`、`|`、`~`来组合多个复杂的筛选条件。

![](img/b6655ab931d17d8b46664aa2209c21c8_68.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_70.png)

![](img/b6655ab931d17d8b46664aa2209c21c8_72.png)

掌握布尔型索引能极大地提升数据处理的效率和代码的可读性，是进行金融量化分析中数据清洗和特征提取的利器。