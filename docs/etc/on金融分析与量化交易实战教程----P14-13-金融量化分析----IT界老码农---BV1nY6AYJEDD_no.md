# Python金融量化分析：P14：13 金融量化分析-numpy-array布尔型索引 🔍

![](img/66eecca7b640e0c61a5f6e72fdcb87df_1.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_3.png)

在本节课中，我们将要学习NumPy中一个非常强大且高效的功能——布尔型索引。通过它，我们可以轻松地根据条件筛选数组中的元素，这比使用Python内置的`filter`函数要简洁得多。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_5.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_6.png)

上一节我们介绍了NumPy数组的常规索引和切片，本节中我们来看看如何使用布尔值作为索引来筛选数据。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_8.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_10.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_12.png)

## 布尔型索引的原理

![](img/66eecca7b640e0c61a5f6e72fdcb87df_14.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_16.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_18.png)

布尔型索引的原理可以分为两步：
1.  首先，通过数组与标量的比较运算，得到一个与原数组长度相同的布尔数组。
2.  然后，将这个布尔数组作为索引传入原数组，即可取出所有对应位置为`True`的元素。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_20.png)

以下是其核心公式：
```python
# 假设 arr 是一个NumPy数组
boolean_mask = arr > 5  # 第一步：生成布尔数组
result = arr[boolean_mask] # 第二步：使用布尔数组索引
# 以上两步通常合并为一行：
result = arr[arr > 5]
```

![](img/66eecca7b640e0c61a5f6e72fdcb87df_22.png)

## 基础用法示例

![](img/66eecca7b640e0c61a5f6e72fdcb87df_24.png)

让我们通过一个例子来直观感受它的便捷性。假设我们有一个数组，需要筛选出所有大于5的数字。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_26.png)

以下是使用Python基础方法的步骤：
*   使用`filter`函数结合`lambda`表达式。
*   将结果转换为列表。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_28.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_30.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_31.png)

以下是使用NumPy布尔型索引的步骤：
*   直接使用 `arr[arr > 5]` 即可完成筛选。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_33.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_35.png)

可以看到，NumPy的方法更加简洁高效。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_37.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_39.png)

## 组合条件筛选

![](img/66eecca7b640e0c61a5f6e72fdcb87df_41.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_43.png)

布尔型索引的强大之处在于可以方便地组合多个筛选条件。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_45.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_47.png)

上一节我们介绍了单一条件的筛选，本节中我们来看看如何组合“与”和“或”条件。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_49.png)

### “与”条件（同时满足多个条件）

![](img/66eecca7b640e0c61a5f6e72fdcb87df_51.png)

例如，要筛选出数组中所有**大于5的偶数**。这需要同时满足“大于5”和“是偶数”两个条件。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_53.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_55.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_56.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_58.png)

核心方法是使用按位与运算符 `&`，并且**每个条件都需要用括号括起来**。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_60.png)

```python
import numpy as np
arr = np.array([1, 8, 3, 10, 5, 7])
result = arr[(arr > 5) & (arr % 2 == 0)]
print(result)  # 输出: [ 8 10]
```

![](img/66eecca7b640e0c61a5f6e72fdcb87df_62.png)

**注意**：这里必须使用 `&`（按位与），而不是Python关键字 `and`。同时，每个条件必须用括号括起来，以确保运算优先级正确。

### “或”条件（满足任一条件）

例如，要筛选出数组中所有**大于5的数或所有的偶数**。这需要满足“大于5”或“是偶数”任一条件。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_64.png)

核心方法是使用按位或运算符 `|`，同样每个条件都需要用括号括起来。

![](img/66eecca7b640e0c61a5f6e72fdcb87df_66.png)

```python
result = arr[(arr > 5) | (arr % 2 == 0)]
print(result)  # 输出: [ 1  8  3 10  7]
```

![](img/66eecca7b640e0c61a5f6e72fdcb87df_68.png)

## 总结

![](img/66eecca7b640e0c61a5f6e72fdcb87df_70.png)

![](img/66eecca7b640e0c61a5f6e72fdcb87df_72.png)

本节课中我们一起学习了NumPy的布尔型索引。我们掌握了其两步工作原理：先通过比较运算生成布尔掩码，再用该掩码索引原数组。我们重点练习了如何使用简洁的 `arr[arr > 5]` 语法进行单条件筛选，并深入学习了如何使用 `&` 和 `|` 运算符结合括号来组合多个筛选条件。这个功能在数据清洗和条件筛选场景中极为高效，是量化分析中处理数组数据的利器。