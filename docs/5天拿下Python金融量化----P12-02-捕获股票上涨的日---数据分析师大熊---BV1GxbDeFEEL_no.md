# Python金融量化：P12：02 捕获股票上涨的日期 📈

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_0.png)

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_2.png)

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_4.png)

在本节课中，我们将学习如何从股票历史交易数据中，找出所有收盘价比开盘价上涨超过3%的日期。这是量化分析中一个基础但重要的步骤，可以帮助我们识别股票的强势上涨日。

上一节我们介绍了如何对数据进行预处理，包括将日期列转换为时间序列并设置为索引。本节中我们来看看如何利用处理好的数据，通过逻辑判断来筛选出满足特定条件的交易日。

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_6.png)

## 为什么需要转换日期格式？ ⏰

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_8.png)

在开始具体操作前，需要明确一点：为什么需要将`date`列转换为时间序列并设置为行索引？

因为后续的分析需求（例如查找特定日期）会频繁用到日期。将原始数据中的日期字符串转换为Pandas的`DatetimeIndex`后，我们就可以利用时间序列特有的方法和属性来高效地完成这些需求。因此，请先跟随步骤完成数据预处理。

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_10.png)

## 核心需求：找出上涨超过3%的日期 📊

我们的目标是找出贵州茅台这支股票所有“收盘价比开盘价上涨3%”的日期。分析这些日期分布（例如集中在某几个月），可能对未来的投资时机选择有参考意义。

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_12.png)

以下是实现此需求的核心逻辑与步骤。

### 步骤1：理解计算逻辑

首先，我们需要用公式来描述“收盘价比开盘价上涨3%”这个条件：

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_14.png)

**公式：** `(close - open) / open > 0.03`

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_16.png)

其中：
*   `open` 代表股票的开盘价。
*   `close` 代表股票的收盘价。
*   `0.03` 即3%。

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_18.png)

这个公式计算的是当日涨幅，并判断是否超过3%。

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_20.png)

### 步骤2：将逻辑转化为Pandas代码

假设我们的数据已加载到名为`df`的DataFrame中，并且`open`和`close`是其列名。我们可以将上述公式直接转化为Pandas的向量化运算：

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_22.png)

**代码：**
```python
condition = (df[‘close‘] - df[‘open‘]) / df[‘open‘] > 0.03
```

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_24.png)

执行这行代码后，`condition`变量将得到一个布尔值（Boolean）序列（Series）。序列中的每个值（`True`或`False`）对应原始数据中的一行（即一个交易日）。

*   `True` 表示该行数据满足“收盘价比开盘价上涨3%”的条件。
*   `False` 表示该行数据不满足条件。

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_26.png)

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_28.png)

### 步骤3：一个重要的数据处理经验 🧠

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_30.png)

在数据分析中，有一个非常实用的经验：

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_32.png)

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_34.png)

**如果产生了布尔值序列，下一步可以立即将其作为原DataFrame的行索引，来提取出`True`对应的数据行。**

这是因为，在Pandas中，使用布尔序列对DataFrame进行索引（`df[boolean_series]`）时，会返回所有对应`True`的行，而自动过滤掉对应`False`的行。

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_36.png)

以下是应用此经验的具体操作：

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_38.png)

**代码：**
```python
# 使用布尔条件筛选出行数据
filtered_df = df[condition]
# 获取这些行数据的索引，即我们想要的日期
result_dates = filtered_df.index
```

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_40.png)

第一行代码 `df[condition]` 利用布尔序列`condition`对原数据`df`进行索引，得到了一个只包含满足条件（即上涨超3%）的那些交易日的**新DataFrame**。
第二行代码 `.index` 则提取出这个新DataFrame的索引，这些索引就是我们要找的**具体日期**。

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_42.png)

### 步骤4：代码整合与结果

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_44.png)

将以上步骤整合，完整的代码如下：

**代码：**
```python
# 计算布尔条件：涨幅超过3%
condition = (df[‘close‘] - df[‘open‘]) / df[‘open‘] > 0.03
# 利用布尔条件筛选数据，并获取其索引（日期）
up_dates = df[condition].index
print(up_dates)
```

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_46.png)

运行后，`up_dates`就是一个包含了所有满足条件的日期的`DatetimeIndex`对象，你可以进一步查看、分析或保存这些日期。

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_48.png)

![](img/d24a9ed07e8f489f429ffb8e8c4ce4e9_49.png)

本节课中我们一起学习了如何从股票数据中筛选特定交易日。核心是掌握利用**布尔条件** `(df[‘close‘] - df[‘open‘]) / df[‘open‘] > 0.03` 进行计算，并运用 **`df[boolean_series]`** 这一技巧来高效提取目标数据行。这个方法在数据筛选和分析中会反复用到，请务必勤加练习。