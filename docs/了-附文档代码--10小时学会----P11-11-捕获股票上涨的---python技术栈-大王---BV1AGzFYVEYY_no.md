# Python金融量化：P11：捕获股票上涨的日期 📈

![](img/da44fe9830dcc41a0eaadd8657dd0417_0.png)

![](img/da44fe9830dcc41a0eaadd8657dd0417_2.png)

![](img/da44fe9830dcc41a0eaadd8657dd0417_4.png)

![](img/da44fe9830dcc41a0eaadd8657dd0417_6.png)

在本节课中，我们将学习如何从股票历史交易数据中，找出所有收盘价比开盘价上涨超过3%的日期。我们将利用Pandas进行数据处理和逻辑判断，并掌握一个重要的数据分析技巧。

![](img/da44fe9830dcc41a0eaadd8657dd0417_8.png)

---

![](img/da44fe9830dcc41a0eaadd8657dd0417_10.png)

上一节我们介绍了如何对股票历史交易数据进行预处理，包括将日期列转换为时间序列并设置为行索引。本节中我们来看看如何利用处理好的数据，实现一个具体的分析需求。

![](img/da44fe9830dcc41a0eaadd8657dd0417_12.png)

## 需求分析

![](img/da44fe9830dcc41a0eaadd8657dd0417_14.png)

我们的目标是找出特定股票（例如茅台）所有收盘价比开盘价上涨超过3%的日期。分析这类数据有助于我们了解股票的上涨规律，例如某些月份上涨概率更高，从而为未来的投资决策提供参考。

## 实现思路

![](img/da44fe9830dcc41a0eaadd8657dd0417_16.png)

要判断收盘价是否比开盘价上涨超过3%，我们可以使用以下逻辑公式：

![](img/da44fe9830dcc41a0eaadd8657dd0417_18.png)

![](img/da44fe9830dcc41a0eaadd8657dd0417_20.png)

**公式：`(收盘价 - 开盘价) / 开盘价 > 0.03`**

![](img/da44fe9830dcc41a0eaadd8657dd0417_22.png)

这个公式计算的是涨幅百分比。如果结果大于0.03（即3%），则说明该交易日满足条件。

![](img/da44fe9830dcc41a0eaadd8657dd0417_24.png)

## 代码实现步骤

![](img/da44fe9830dcc41a0eaadd8657dd0417_26.png)

以下是实现该需求的具体步骤，我们将使用Pandas的DataFrame进行操作。

![](img/da44fe9830dcc41a0eaadd8657dd0417_28.png)

![](img/da44fe9830dcc41a0eaadd8657dd0417_30.png)

1.  **构建布尔序列**
    首先，我们需要根据上述公式，对DataFrame中的每一行数据进行计算，得到一个布尔值序列（Series）。序列中的`True`表示该日期满足上涨条件，`False`则表示不满足。

    ```python
    # 假设df是我们的DataFrame，包含‘open’（开盘价）和‘close’（收盘价）列
    bool_series = (df[‘close‘] - df[‘open‘]) / df[‘open‘] > 0.03
    ```

    执行这行代码后，`bool_series`就是一个由`True`和`False`组成的序列，其索引与原始数据的日期索引一致。

![](img/da44fe9830dcc41a0eaadd8657dd0417_32.png)

![](img/da44fe9830dcc41a0eaadd8657dd0417_34.png)

2.  **利用布尔序列筛选数据**
    在数据分析中，一个非常实用的技巧是：**将布尔序列作为原始DataFrame的行索引，可以快速筛选出所有`True`对应的行数据。**

    这是因为，当使用布尔列表进行索引时，Pandas会返回所有对应位置为`True`的行。

    ```python
    # 使用布尔序列筛选出满足条件的行
    filtered_df = df.loc[bool_series]
    ```

    执行后，`filtered_df`只包含那些收盘价比开盘价上涨超过3%的日期的所有数据（开盘价、收盘价、最高价、最低价等）。

![](img/da44fe9830dcc41a0eaadd8657dd0417_36.png)

3.  **提取目标日期**
    我们的最终目标是日期，而不是整行数据。由于之前已将日期设置为行索引，因此满足条件的日期就是筛选后DataFrame的索引。

    ```python
    # 获取满足条件的日期列表
    up_dates = filtered_df.index
    ```

    这样，`up_dates`就是我们需要找的所有上涨日期。

![](img/da44fe9830dcc41a0eaadd8657dd0417_38.png)

![](img/da44fe9830dcc41a0eaadd8657dd0417_40.png)

## 核心技巧总结

![](img/da44fe9830dcc41a0eaadd8657dd0417_42.png)

在本节实现中，最关键的一步是 **“将布尔序列作为行索引进行数据筛选”**。这个技巧可以总结为：
> 在数据分析过程中，如果得到一组布尔值，下一步应立即将其作为原数据的行索引，从而直接提取出所有条件为真的数据行。

![](img/da44fe9830dcc41a0eaadd8657dd0417_44.png)

![](img/da44fe9830dcc41a0eaadd8657dd0417_46.png)

这个方法简洁高效，是使用Pandas进行数据查询和筛选的常用手段。

---

![](img/da44fe9830dcc41a0eaadd8657dd0417_48.png)

![](img/da44fe9830dcc41a0eaadd8657dd0417_50.png)

![](img/da44fe9830dcc41a0eaadd8657dd0417_51.png)

本节课中我们一起学习了如何从股票数据中筛选出满足特定价格条件的日期。我们掌握了通过公式计算涨幅、生成布尔序列，并利用该序列高效筛选数据的完整流程。这个“布尔索引”的技巧在数据分析中应用非常广泛，请务必熟练掌握并勤加练习。