# Python金融量化分析：P22：Series缺失值处理 🧩

![](img/99f80d9cffaeedcb531d37492e960c6d_1.png)

在本节课中，我们将要学习如何处理Pandas Series中的缺失数据。缺失数据在数据分析中非常常见，学会处理它们是进行有效分析的关键一步。

上一节我们介绍了Series的基本操作，本节中我们来看看当数据不完整时，我们有哪些应对策略。

## 概述：什么是缺失数据

在Series中，可能会出现缺失数据，通常用`NaN`（Not a Number）值表示。

![](img/99f80d9cffaeedcb531d37492e960c6d_3.png)

![](img/99f80d9cffaeedcb531d37492e960c6d_1.png)

有时我们可以忽略这些缺失值，但在进行数学运算或生成图表时，它们可能会带来问题。因此，我们需要掌握处理缺失数据的方法。

## 缺失值处理方法

处理缺失数据主要有两种思路：删除缺失值或填充缺失值。

![](img/99f80d9cffaeedcb531d37492e960c6d_5.png)

### 方法一：删除缺失值

第一种方法是直接删除包含缺失数据的行。

![](img/99f80d9cffaeedcb531d37492e960c6d_7.png)

首先，我们需要判断哪些行是缺失的。以下是相关的函数：

*   **`SR.isnull()`**：判断每个元素是否为缺失值。返回一个布尔型Series，是`NaN`的为`True`，不是的为`False`。
    ![](img/99f80d9cffaeedcb531d37492e960c6d_3.png)

![](img/99f80d9cffaeedcb531d37492e960c6d_9.png)

*   **`SR.notnull()`**：与`isnull()`相反，判断每个元素是否不是缺失值。不是`NaN`的为`True`，是的为`False`。
    ![](img/99f80d9cffaeedcb531d37492e960c6d_5.png)

利用布尔索引，我们可以过滤掉缺失值：

![](img/99f80d9cffaeedcb531d37492e960c6d_11.png)

```python
SR[SR.notnull()]
```
![](img/99f80d9cffaeedcb531d37492e960c6d_7.png)

![](img/99f80d9cffaeedcb531d37492e960c6d_13.png)

此外，Pandas提供了一个更直接的方法来删除缺失值：

*   **`SR.dropna()`**：直接删除所有包含缺失值的行。
    ![](img/99f80d9cffaeedcb531d37492e960c6d_9.png)

![](img/99f80d9cffaeedcb531d37492e960c6d_15.png)

> **注意**：`NA`是`NaN`的一个别称，都代表缺失值。

### 方法二：填充缺失值

第二种方法不是删除，而是为缺失值赋予一个具体的数值。

填充缺失值的函数是：

*   **`SR.fillna(value)`**：将所有的`NaN`值替换为指定的`value`。例如，填充为0：
    ![](img/99f80d9cffaeedcb531d37492e960c6d_11.png)
    ![](img/99f80d9cffaeedcb531d37492e960c6d_13.png)

**重要提示**：Pandas和NumPy中的数据处理方法通常不会直接修改原始数据。执行`fillna()`后，原始Series`SR`并没有改变。如果你想保存填充后的结果，需要将其赋值给一个新变量。

```python
SR_filled = SR.fillna(0)
```

![](img/99f80d9cffaeedcb531d37492e960c6d_17.png)

除了填充固定值（如0），根据分析场景，我们还可以用更有意义的数值进行填充。例如，用整列数据的平均值来填充缺失值，可以使数据趋势更平滑。

```python
# 计算Series的平均值（自动忽略NaN值）
mean_value = SR.mean()
# 用平均值填充缺失值
SR_filled_with_mean = SR.fillna(mean_value)
```
![](img/99f80d9cffaeedcb531d37492e960c6d_17.png)
![](img/99f80d9cffaeedcb531d37492e960c6d_19.png)
![](img/99f80d9cffaeedcb531d37492e960c6d_21.png)

![](img/99f80d9cffaeedcb531d37492e960c6d_19.png)

Pandas的`.mean()`等方法在计算时会自动跳过`NaN`值，这为数据处理带来了极大的便利。

![](img/99f80d9cffaeedcb531d37492e960c6d_21.png)

## 总结

本节课中我们一起学习了Pandas Series缺失值处理的两种核心方法。

1.  **删除缺失值**：使用`SR.dropna()`或通过布尔索引`SR[SR.notnull()]`进行过滤。
2.  **填充缺失值**：使用`SR.fillna(value)`，可以根据需求填充固定值（如0）或统计值（如平均值）。

掌握这些方法，你就能有效地清理Series中的数据，为后续的金融量化分析打下坚实的基础。

![](img/99f80d9cffaeedcb531d37492e960c6d_23.png)

![](img/99f80d9cffaeedcb531d37492e960c6d_23.png)