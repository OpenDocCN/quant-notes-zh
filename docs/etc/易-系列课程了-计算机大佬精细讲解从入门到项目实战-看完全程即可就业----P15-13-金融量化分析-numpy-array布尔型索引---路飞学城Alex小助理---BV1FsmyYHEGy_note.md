# Python金融量化分析：P15：13 金融量化分析-numpy-array布尔型索引 📊

![](img/659a815e9baa6e3fa6ee5061afd1fc47_1.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_3.png)

在本节课中，我们将学习NumPy中一个非常强大且高效的功能——布尔型索引。通过它，我们可以轻松地根据条件筛选数组中的元素，这比使用Python内置的`filter`函数要简洁得多。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_5.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_6.png)

上一节我们介绍了NumPy数组的常规索引和切片，本节中我们来看看如何使用布尔值作为索引来筛选数据。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_8.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_10.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_12.png)

## 布尔型索引的原理

![](img/659a815e9baa6e3fa6ee5061afd1fc47_14.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_16.png)

布尔型索引的原理可以分为两步。首先，通过数组与标量的比较运算，得到一个布尔数组。然后，将这个布尔数组传入原数组的索引中，即可筛选出对应位置为`True`的元素。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_18.png)

以下是其核心原理的公式化描述：
1.  **生成布尔数组**：`bool_array = (array > value)`。这是一个数组与标量的比较运算。
2.  **应用布尔索引**：`result = array[bool_array]`。将布尔数组作为索引传入，返回所有`True`位置对应的元素。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_20.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_22.png)

## 基础用法示例

![](img/659a815e9baa6e3fa6ee5061afd1fc47_24.png)

让我们通过一个具体的例子来理解。首先，我们创建一个数组。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_26.png)

```python
import numpy as np

![](img/659a815e9baa6e3fa6ee5061afd1fc47_28.png)

# 创建一个示例数组
A = np.array([8, 3, 6, 9, 2, 7])
print("原始数组 A:", A)
```

![](img/659a815e9baa6e3fa6ee5061afd1fc47_30.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_31.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_33.png)

假设我们需要筛选出数组中所有大于5的数。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_35.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_37.png)

**使用Python的`filter`函数**（传统方法）：
```python
# 使用filter和lambda函数
result_filter = list(filter(lambda x: x > 5, A))
print("使用filter筛选结果:", result_filter)
```
这种方法代码较长，且需要转换为列表。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_39.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_41.png)

**使用NumPy的布尔型索引**：
```python
# 使用NumPy布尔型索引
result_numpy = A[A > 5]
print("使用NumPy布尔索引筛选结果:", result_numpy)
```
可以看到，NumPy的方法 `A[A > 5]` 一行代码就完成了任务，非常简洁高效。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_43.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_45.png)

## 深入理解操作步骤

![](img/659a815e9baa6e3fa6ee5061afd1fc47_47.png)

为了更清晰地理解 `A[A > 5]` 是如何工作的，我们可以将其拆解：

![](img/659a815e9baa6e3fa6ee5061afd1fc47_49.png)

1.  **执行比较运算**：`A > 5` 会将数组`A`中的每个元素与5比较，返回一个布尔数组。
    ```python
    bool_mask = A > 5
    print("布尔掩码 (A > 5):", bool_mask) # 输出: [ True False  True  True False  True]
    ```
2.  **应用布尔索引**：将上一步得到的布尔数组 `bool_mask` 作为索引传递给`A`。
    ```python
    final_result = A[bool_mask]
    print("应用布尔索引后的结果:", final_result) # 输出: [8 6 9 7]
    ```
`A[A > 5]` 实际上就是这两个步骤的合并。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_51.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_53.png)

## 组合条件筛选

![](img/659a815e9baa6e3fa6ee5061afd1fc47_55.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_56.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_58.png)

布尔型索引的强大之处在于可以方便地组合多个筛选条件。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_60.png)

以下是实现组合条件筛选的方法：

![](img/659a815e9baa6e3fa6ee5061afd1fc47_62.png)

**示例：筛选数组中所有大于5的偶数。**

*   **方法一：分步筛选**（逻辑清晰）
    ```python
    # 第一步：筛选大于5的数
    B = A[A > 5]
    print("大于5的数:", B)
    # 第二步：从结果中筛选偶数
    C = B[B % 2 == 0]
    print("大于5的偶数:", C)
    ```

*   **方法二：单步组合条件**（使用位运算符`&`表示“且”，`|`表示“或”）
    ```python
    # 注意：每个条件必须用括号括起来
    D = A[(A > 5) & (A % 2 == 0)]
    print("单步筛选大于5的偶数:", D)
    ```
    **重要提示**：必须使用位运算符 `&`（与）、`|`（或），而不是Python关键字 `and`、`or`。同时，每个独立的条件都需要用括号括起来，以确保运算优先级正确。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_64.png)

**示例：筛选大于5的数或所有的偶数（满足任一条件即可）。**
```python
# 使用 | 表示“或”关系
E = A[(A > 5) | (A % 2 == 0)]
print(“大于5的数或偶数:”, E)
```

![](img/659a815e9baa6e3fa6ee5061afd1fc47_66.png)

## 注意事项与技巧

![](img/659a815e9baa6e3fa6ee5061afd1fc47_68.png)

在使用布尔型索引时，有几点需要特别注意：
1.  **运算符选择**：组合条件时，务必使用 `&`（按位与）、`|`（按位或），而不是 `and`、`or`。
2.  **括号必不可少**：每个条件表达式都应放在括号内，例如 `(A > 5) & (A < 10)`。
3.  **长度一致原则**：直接用于索引的布尔数组，其长度必须与原数组一致。这也是为什么不能将分步筛选的结果直接链式调用（如 `A[A>5][(A[A>5])%2==0]` 是错误的），因为第二步的布尔数组长度与第一步筛选后的数组长度不匹配。

![](img/659a815e9baa6e3fa6ee5061afd1fc47_70.png)

![](img/659a815e9baa6e3fa6ee5061afd1fc47_72.png)

本节课中我们一起学习了NumPy的布尔型索引。我们掌握了其两步工作原理：先通过比较运算生成布尔数组，再将其作为索引进行筛选。我们重点练习了如何使用简洁的 `A[A > 5]` 语法进行条件筛选，并学习了如何通过 `&` 和 `|` 运算符组合多个复杂条件。这个功能在数据清洗、条件过滤等量化分析场景中极为实用，能极大提升代码的简洁性和执行效率。