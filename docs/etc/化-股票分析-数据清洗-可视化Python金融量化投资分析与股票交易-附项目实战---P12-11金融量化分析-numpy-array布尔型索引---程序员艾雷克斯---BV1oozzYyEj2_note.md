# Python金融量化投资分析：P12：NumPy数组的布尔型索引 🔍

![](img/bdd8dfde68bd60f80b32c921e792bed4_1.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_3.png)

在本节课中，我们将学习NumPy中一个非常强大且高效的功能——布尔型索引。它允许我们使用布尔值（True/False）构成的数组来快速筛选出目标数据，比传统的Python循环或`filter`函数简洁得多。

![](img/bdd8dfde68bd60f80b32c921e792bed4_5.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_6.png)

上一节我们介绍了数组的常规索引和切片，本节中我们来看看如何使用布尔逻辑进行更灵活的数据筛选。

![](img/bdd8dfde68bd60f80b32c921e792bed4_8.png)

## 布尔型索引的原理

![](img/bdd8dfde68bd60f80b32c921e792bed4_10.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_12.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_14.png)

布尔型索引的原理可以分为两个步骤理解：

![](img/bdd8dfde68bd60f80b32c921e792bed4_16.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_18.png)

1.  **数组与标量的比较运算**：将一个数组与一个值（例如数字5）进行比较，会得到一个**布尔数组**，其中每个元素表示原数组中对应位置的元素是否满足条件（如大于5）。
2.  **使用布尔数组进行索引**：将这个布尔数组放入原数组的方括号 `[]` 中，NumPy会自动返回所有在布尔数组中对应位置为 `True` 的元素。

![](img/bdd8dfde68bd60f80b32c921e792bed4_20.png)

以下是核心概念的公式化描述：
**`result_array = original_array[boolean_array]`**
其中，`boolean_array` 是一个由 `True` 和 `False` 构成的数组，其长度必须与 `original_array` 相同。

![](img/bdd8dfde68bd60f80b32c921e792bed4_22.png)

## 基础用法示例

![](img/bdd8dfde68bd60f80b32c921e792bed4_24.png)

让我们通过一个例子来直观感受它的便捷性。假设我们有一个数组，需要筛选出所有大于5的元素。

![](img/bdd8dfde68bd60f80b32c921e792bed4_26.png)

首先，我们创建一个示例数组：

![](img/bdd8dfde68bd60f80b32c921e792bed4_28.png)

```python
import numpy as np

![](img/bdd8dfde68bd60f80b32c921e792bed4_30.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_31.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_33.png)

# 创建一个示例数组
a = np.array([8, 3, 6, 2, 9, 1])
print(‘原始数组 a：‘, a)
```

![](img/bdd8dfde68bd60f80b32c921e792bed4_35.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_37.png)

如果使用Python基础的`filter`函数，代码会相对繁琐：

![](img/bdd8dfde68bd60f80b32c921e792bed4_39.png)

```python
# 使用 filter 函数
result_filter = list(filter(lambda x: x > 5, a))
print(‘使用 filter 筛选：‘, result_filter)
```

![](img/bdd8dfde68bd60f80b32c921e792bed4_41.png)

而使用NumPy的布尔型索引，只需一行代码：

![](img/bdd8dfde68bd60f80b32c921e792bed4_43.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_45.png)

```python
# 使用 NumPy 布尔型索引
result_bool = a[a > 5]
print(‘使用布尔型索引筛选：‘, result_bool)
```

![](img/bdd8dfde68bd60f80b32c921e792bed4_47.png)

输出结果都是 `[8, 6, 9]`。可以看到，`a[a > 5]` 这种写法非常直观和高效。

![](img/bdd8dfde68bd60f80b32c921e792bed4_49.png)

## 组合条件筛选

![](img/bdd8dfde68bd60f80b32c921e792bed4_51.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_53.png)

在实际应用中，我们经常需要根据多个条件进行筛选。NumPy支持使用位运算符来组合多个布尔条件。

![](img/bdd8dfde68bd60f80b32c921e792bed4_55.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_56.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_58.png)

以下是组合条件时需要注意的要点：
*   **与运算 (AND)**：使用 `&` 运算符。**注意**：每个条件必须用括号 `()` 括起来。
*   **或运算 (OR)**：使用 `|` 运算符。同样，每个条件必须用括号 `()` 括起来。
*   **非运算 (NOT)**：使用 `~` 运算符。

![](img/bdd8dfde68bd60f80b32c921e792bed4_60.png)

**重要提示**：请使用 `&`, `|`, `~` 进行布尔数组的逻辑运算，而不是Python关键字 `and`, `or`, `not`。

![](img/bdd8dfde68bd60f80b32c921e792bed4_62.png)

### 示例1：筛选“大于5的偶数”

这需要同时满足两个条件（“大于5” **并且** “是偶数”），使用 `&` 运算符。

```python
# 筛选大于5的偶数
condition = (a > 5) & (a % 2 == 0)
result = a[condition]
print(‘大于5的偶数：‘, result)  # 输出: [6]
```

![](img/bdd8dfde68bd60f80b32c921e792bed4_64.png)

### 示例2：筛选“大于5的数 或 所有偶数”

这满足任意一个条件即可，使用 `|` 运算符。

![](img/bdd8dfde68bd60f80b32c921e792bed4_66.png)

```python
# 筛选大于5的数 或 偶数
condition = (a > 5) | (a % 2 == 0)
result = a[condition]
print(‘大于5的数或偶数：‘, result)  # 输出: [8, 6, 2, 9]
```

![](img/bdd8dfde68bd60f80b32c921e792bed4_68.png)

## 总结

![](img/bdd8dfde68bd60f80b32c921e792bed4_70.png)

![](img/bdd8dfde68bd60f80b32c921e792bed4_72.png)

本节课中我们一起学习了NumPy的**布尔型索引**。我们掌握了其核心原理：先通过比较运算生成布尔掩码数组，再利用该数组索引原数组。我们重点练习了如何使用 `&`、`|` 运算符组合多个筛选条件，这能让我们用极其简洁的代码完成复杂的数据过滤任务。这个功能是金融量化分析中进行数据清洗和条件选股的利器，请务必熟练掌握。