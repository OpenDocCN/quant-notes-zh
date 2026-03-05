# Python金融分析量化交易：P7：连续指标变化情况分析 📈

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_0.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_2.png)

在本节课中，我们将要学习如何分析金融时间序列的连续变化情况。我们将探讨为什么简单的增长率累加会失效，并引入对数方法来解决这个问题，最终计算出“初始投资相当于未来某一天的价值”这一核心指标。

## 概述

上一节我们介绍了如何计算简单的差异值和增长率。本节中我们来看看，当我们需要分析一个连续时间段内的整体增长情况时，应该如何处理。特别是，我们将解决“增长率能否直接累加”这一问题，并学习如何正确计算连续复合增长率。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_4.png)

## 增长率累加的问题

首先，我们思考一个问题：平均增长率的结果，能否进行加法操作？

例如，观察一只股票的连续价格：
*   第一天价格：100元
*   第二天价格：50元
*   第三天价格：75元

如果分别计算每日的增长率：
*   第二天增长率：(50 - 100) / 100 = **-0.5**
*   第三天增长率：(75 - 50) / 50 = **0.5**

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_6.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_8.png)

若将这两个增长率简单累加：-0.5 + 0.5 = **0**。这个结果为0，似乎意味着从第一天到第三天，股票既没涨也没跌。

但观察实际价格：从100元变为75元，股票实际上是下跌的。这说明，对于连续时间的增长率，**不能直接进行加法运算**，因为简单累加无法反映第一天与最后一天之间的真实整体变化。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_10.png)

## 解决方案：引入对数计算

对于连续时间的计算，我们需要借助数学中的对数公式。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_12.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_14.png)

核心思想是：将对数域内的加法，转换为原始域内的乘法。这样，连续增长率的累加，就等价于计算最终值与初始值的直接比例。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_16.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_18.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_20.png)

计算公式如下：
`log(第二天价格 / 第一天价格) + log(第三天价格 / 第二天价格) = log(第三天价格 / 第一天价格)`

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_22.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_24.png)

**公式推导：**
`ln(P2/P1) + ln(P3/P2) = ln( (P2/P1) * (P3/P2) ) = ln(P3/P1)`

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_26.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_28.png)

这个结果 `ln(P3/P1)` 的含义，正是最后一天价格与第一天价格比值的自然对数，完美地描述了两者之间的整体差异。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_30.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_32.png)

形象地说，这相当于计算：如果初始股价为1个单位（例如1元），经过一系列涨跌后，在最后一天它相当于多少钱。

## 代码实现：计算连续增长率

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_34.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_36.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_38.png)

以下是如何在Python中实现上述计算。

首先，我们需要获取“前一天”的数据。Pandas的`.shift()`函数可以将数据序列向上或向下移动。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_40.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_42.png)

```python
# 计算每日价格相对于前一日价格的比例，并取自然对数
return_rate = np.log(data / data.shift(1))
# 展示结果
print(return_rate.head().round(4))
```

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_44.png)

这段代码中：
*   `data.shift(1)` 将整个数据序列向上移动一格，使得每一天对应的值变为其前一天的值。
*   `data / data.shift(1)` 计算了当日与前一日价格的比值。
*   `np.log()` 对该比值取自然对数，得到每日的连续增长率（或称对数收益率）。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_46.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_48.png)

## 代码实现：计算累积价值

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_50.png)

我们得到了每日的连续增长率（对数收益率）。接下来，我们需要将其累加起来，并还原为实际的资产价值倍数，即回答“最初的一块钱相当于未来某一天值多少钱”。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_52.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_54.png)

这需要两个步骤：
1.  **累加连续增长率**：使用 `np.cumsum()` 函数对 `return_rate` 进行累积求和。
2.  **还原为实际价值**：对累积和的结果取指数函数 `np.exp()`，以抵消之前取对数的操作。

```python
# 对连续增长率进行累积求和
cumulative_return = return_rate.cumsum()
# 通过指数函数还原为实际的价值倍数（初始1单位对应的价值）
cumulative_value = np.exp(cumulative_return)
# 绘制结果图表
cumulative_value.plot(figsize=(16, 8))
plt.show()
```

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_56.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_58.png)

*   `np.cumsum([1,2,3,4])` 的结果是 `[1, 3, 6, 10]`，即序列的累积和。
*   `np.exp(cumulative_return)` 执行了 `e^(cumulative_return)`，将对数域的累积增长还原为实际的价值倍数。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_60.png)

执行上述代码后，我们将得到一张图表。图中每条曲线都从1开始，展示了假设在起始日（如2010年）投资1单位货币于某只股票，到后续每一个交易日，这笔投资所对应的价值。例如，亚马逊（AMZN）的曲线在末期远高于10，意味着2010年的1美元投资，到2018年已增值为超过10美元。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_62.png)

## 本节总结

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_64.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_66.png)

本节课中我们一起学习了金融时间序列中连续变化情况的分析方法：

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_68.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_70.png)

1.  **识别问题**：我们首先明确了简单增长率不能直接累加，因为它无法正确反映时间序列起点与终点的整体变化。
2.  **引入方法**：为了解决这个问题，我们引入了对数计算。通过对数将乘法关系转化为加法关系，使得连续增长率的累加变得有意义，其累加结果直接对应最终值与初始值的比例。
3.  **代码实现**：
    *   使用 `data.shift(1)` 获取滞后一期数据。
    *   使用 `np.log(data / data.shift(1))` 计算每日的连续增长率（对数收益率）。
    *   使用 `.cumsum()` 对连续增长率进行累积。
    *   使用 `np.exp()` 将累积的对数收益率还原为实际的价值倍数曲线。
4.  **结果解读**：最终绘制的曲线直观展示了“初始投资随时间推移的价值增长路径”，这是金融分析中评估长期投资表现的一个强大工具。

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_72.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_74.png)

![](img/aefb8c06d1cfe21a3b086fcbc0b946fe_75.png)

通过本课，你掌握了在Pandas和NumPy中实现金融时间序列关键计算的操作，包括差异值、简单增长率以及更重要的连续复合增长与累积价值计算。