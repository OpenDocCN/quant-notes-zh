# Python金融量化投资分析与股票交易：P19：Series缺失值处理 🧹

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_1.png)

在本节课中，我们将要学习如何处理Pandas Series数据结构中的缺失值。缺失值是数据分析中常见的问题，学会正确处理它们对于后续的数据清洗、分析和可视化至关重要。

上一节我们介绍了Series的基本操作，本节中我们来看看如何处理其中的缺失数据。

## 什么是缺失值？

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_3.png)

在Series中，缺失数据通常表示为`NaN`（Not a Number）。出现缺失数据时，有时我们可以放任不管，但在进行进一步运算或生成图表时，这些缺失值可能会带来问题，因此需要处理。

## 缺失值处理方法

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_5.png)

处理缺失值主要有两种思路：删除缺失值或填充缺失值。

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_7.png)

### 方法一：删除缺失值

第一种方法是将包含缺失数据的行直接删除。

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_9.png)

以下是判断和删除缺失值的相关函数：

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_11.png)

*   **`sr.isnull()`**：判断每个元素是否为缺失值。返回一个布尔型Series，是`NaN`的为`True`，不是的为`False`。
*   **`sr.notnull()`**：与`isnull()`相反，不是缺失值的地方返回`True`。
*   **`sr.dropna()`**：直接删除所有包含缺失值的行。

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_13.png)

我们可以利用布尔索引来过滤数据，例如 `sr[sr.notnull()]` 可以保留所有非缺失值。`dropna()`函数则提供了更直接的方式。

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_15.png)

### 方法二：填充缺失值

第二种方法是为缺失数据赋予一个具体的值。

填充缺失值的函数是 **`sr.fillna(value)`**。例如，`sr.fillna(0)`会将所有`NaN`值替换为0。

**请注意**：Pandas的许多操作（包括`fillna`）默认不会修改原始Series，而是返回一个新的Series。如果你想保存处理后的结果，需要将其赋值给一个新变量，例如 `sr_filled = sr.fillna(0)`。

## 填充策略的选择

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_17.png)

我们可以根据实际情况决定填充什么值。例如，缺失可能表示没有记录，填充为0是合理的。但有时，为了保持数据的连续性（如观察趋势），填充平均值可能更合适。

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_19.png)

Pandas的Series可以直接计算平均值（`sr.mean()`），并且在计算时会自动忽略`NaN`值，这为填充操作提供了便利。例如，`sr.fillna(sr.mean())`会用该Series的平均值来填充所有缺失值。

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_21.png)

这种方法相比手动处理列表或字典中的缺失值要方便得多，这也是许多人喜欢使用Pandas进行数据处理的原因之一。

## 总结

![](img/0f89fba3d39a6f833bafaf5ee2bc9542_23.png)

本节课中我们一起学习了Pandas Series缺失值处理的两种核心方法：删除（`dropna`）与填充（`fillna`）。我们了解了如何判断缺失值（`isnull`, `notnull`），并掌握了根据数据背景选择合适的值（如0或平均值）进行填充的技巧。正确处理缺失值是数据清洗的关键步骤，为后续的量化分析打下坚实基础。