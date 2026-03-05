# 金融量化分析：P13：NumPy数组布尔型索引 🔍

![](img/7b579e0a8da1682678cd8cd98c4f15e6_1.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_3.png)

在本节课中，我们将学习NumPy中一个非常强大且高效的功能——布尔型索引。它允许我们根据条件快速筛选数组中的元素，比传统的Python方法简洁得多。

![](img/7b579e0a8da1682678cd8cd98c4f15e6_5.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_6.png)

上一节我们介绍了数组的常规索引和切片，本节中我们来看看如何使用布尔值来“智能”地选择数据。

![](img/7b579e0a8da1682678cd8cd98c4f15e6_8.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_10.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_12.png)

## 布尔型索引的原理

![](img/7b579e0a8da1682678cd8cd98c4f15e6_14.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_16.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_18.png)

布尔型索引的原理可以分为两个步骤：
1.  首先，通过数组与标量的比较运算，生成一个与原数组形状相同的**布尔数组**。
2.  然后，将这个布尔数组作为索引传入原数组，NumPy会返回所有在布尔数组中对应位置为`True`的元素。

![](img/7b579e0a8da1682678cd8cd98c4f15e6_20.png)

其核心操作可以用以下代码表示：
```python
# 假设 arr 是一个NumPy数组
condition = arr > 5  # 步骤1：生成布尔数组
result = arr[condition]  # 步骤2：应用布尔索引
# 也可以简写为一行：arr[arr > 5]
```

![](img/7b579e0a8da1682678cd8cd98c4f15e6_22.png)

## 基础用法：筛选大于特定值的元素

![](img/7b579e0a8da1682678cd8cd98c4f15e6_24.png)

让我们通过一个例子来直观感受它的便捷性。假设我们需要从一个数组中筛选出所有大于5的数字。

![](img/7b579e0a8da1682678cd8cd98c4f15e6_26.png)

以下是使用传统Python `filter`函数的方法：
```python
import numpy as np

![](img/7b579e0a8da1682678cd8cd98c4f15e6_28.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_30.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_31.png)

# 创建一个列表
a_list = [8, 3, 6, 2, 9, 1]
# 使用filter和lambda函数
result = list(filter(lambda x: x > 5, a_list))
print(result)  # 输出：[8, 6, 9]
```

![](img/7b579e0a8da1682678cd8cd98c4f15e6_33.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_35.png)

以下是使用NumPy布尔型索引的方法：
```python
# 将列表转换为NumPy数组
a = np.array([8, 3, 6, 2, 9, 1])
# 使用布尔型索引
result = a[a > 5]
print(result)  # 输出：[8 6 9]
```
可以看到，NumPy的语法 `a[a > 5]` 更加简洁直观。

![](img/7b579e0a8da1682678cd8cd98c4f15e6_37.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_39.png)

## 理解底层机制

![](img/7b579e0a8da1682678cd8cd98c4f15e6_41.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_43.png)

为了更深入理解，我们可以手动分解这个过程：
```python
a = np.array([8, 3, 6, 2, 9, 1])
# 第一步：执行比较运算，生成布尔数组
bool_mask = a > 5
print(bool_mask)  # 输出：[ True False  True False  True False]

![](img/7b579e0a8da1682678cd8cd98c4f15e6_45.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_47.png)

# 第二步：将布尔数组作为索引传入
result = a[bool_mask]
print(result)  # 输出：[8 6 9]
```
这清晰地展示了其工作原理：`a[bool_mask]` 会选取 `bool_mask` 中为 `True` 的位置所对应的 `a` 中的元素。

![](img/7b579e0a8da1682678cd8cd98c4f15e6_49.png)

## 组合条件筛选

![](img/7b579e0a8da1682678cd8cd98c4f15e6_51.png)

布尔型索引的强大之处在于可以方便地组合多个条件。NumPy使用位运算符来组合布尔数组，而不是Python的关键字 `and` 和 `or`。

![](img/7b579e0a8da1682678cd8cd98c4f15e6_53.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_55.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_56.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_58.png)

以下是需要使用的运算符：
*   **与操作**：使用 `&`
*   **或操作**：使用 `|`
*   **非操作**：使用 `~`

![](img/7b579e0a8da1682678cd8cd98c4f15e6_60.png)

**重要提示**：每个条件必须用括号括起来，因为位运算符的优先级高于比较运算符。

![](img/7b579e0a8da1682678cd8cd98c4f15e6_62.png)

### 案例一：筛选“大于5的偶数”

这需要同时满足两个条件，是“与”的关系。
```python
a = np.array([8, 3, 6, 2, 9, 1])
# 条件：大于5 且 是偶数 (模2等于0)
result = a[(a > 5) & (a % 2 == 0)]
print(result)  # 输出：[8 6]
```

### 案例二：筛选“大于5的数 或 所有偶数”

![](img/7b579e0a8da1682678cd8cd98c4f15e6_64.png)

这满足任一条件即可，是“或”的关系。
```python
a = np.array([8, 3, 6, 2, 9, 1])
# 条件：大于5 或 是偶数
result = a[(a > 5) | (a % 2 == 0)]
print(result)  # 输出：[8 6 2 9]
```

![](img/7b579e0a8da1682678cd8cd98c4f15e6_66.png)

请注意，不能分步执行“或”操作，因为第一步筛选会改变数组的长度和内容，导致第二步的条件无法对应到原始数据。

![](img/7b579e0a8da1682678cd8cd98c4f15e6_68.png)

---

![](img/7b579e0a8da1682678cd8cd98c4f15e6_70.png)

![](img/7b579e0a8da1682678cd8cd98c4f15e6_72.png)

本节课中我们一起学习了NumPy的布尔型索引。我们掌握了其两步工作原理：先通过比较运算生成布尔掩码，再利用掩码筛选数据。重点学习了如何使用 `&`、`|` 运算符组合多个条件进行复杂筛选，并强调了使用括号的必要性。这个功能是数据清洗和条件筛选的利器，能极大提升代码的简洁性和效率。