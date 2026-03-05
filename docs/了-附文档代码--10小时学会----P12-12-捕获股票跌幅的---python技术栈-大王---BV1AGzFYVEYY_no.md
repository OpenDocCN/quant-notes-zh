# Python金融量化：P12：捕获股票跌幅的日期 📉

![](img/e4efc2f147b411aceac67affa3bd8df1_1.png)

在本节课中，我们将学习如何利用Pandas数据分析库，从股票数据中筛选出开盘价较前一日收盘价跌幅超过2%的日期。这是一个典型的数据筛选与计算任务，我们将通过清晰的步骤和代码演示来完成它。

![](img/e4efc2f147b411aceac67affa3bd8df1_3.png)

## 需求分析

![](img/e4efc2f147b411aceac67affa3bd8df1_5.png)

上一节我们介绍了数据处理的基本操作，本节中我们来看看一个具体的分析需求：找到某支股票所有开盘价较前一日收盘价跌幅超过2%的日期。

这个需求的核心是计算每日的涨跌幅，并进行条件筛选。涨跌幅的计算公式为：
**涨跌幅 = (当日开盘价 - 前一日收盘价) / 前一日收盘价**

我们的目标是找出所有 **涨跌幅 < -0.02** 的日期。

## 核心思路与伪代码

![](img/e4efc2f147b411aceac67affa3bd8df1_7.png)

要计算“前一日收盘价”，我们需要将收盘价序列整体向下移动一位，这样每个交易日对应的就是其前一日的收盘价。在Pandas中，这可以通过 `shift(1)` 方法实现。

以下是实现该需求的逻辑步骤（伪代码）：
1.  计算前一日收盘价：`前日收盘价 = df[‘close’].shift(1)`
2.  计算涨跌幅：`涨跌幅 = (df[‘open’] - 前日收盘价) / 前日收盘价`
3.  筛选条件：`涨跌幅 < -0.02`
4.  使用上述布尔条件筛选原始数据，得到满足条件的行。
5.  从筛选结果中提取日期索引。

![](img/e4efc2f147b411aceac67affa3bd8df1_9.png)

## 代码实现与分步讲解

让我们将伪代码转化为实际的Python代码。首先，我们导入必要的库并加载数据（假设数据已加载到DataFrame `df` 中）。

```python
import pandas as pd
# 假设 df 是包含 ‘open’, ‘close’ 列的股票数据DataFrame
```

第一步，计算前一日收盘价。`shift(1)` 方法将 `close` 列的所有值向下移动一行，第一行由于没有前一日数据，会自动填充为 `NaN`。

```python
pre_close = df[‘close’].shift(1)
```

![](img/e4efc2f147b411aceac67affa3bd8df1_11.png)

第二步，计算每日开盘价相对于前一日收盘价的涨跌幅。

![](img/e4efc2f147b411aceac67affa3bd8df1_13.png)

```python
pct_change = (df[‘open’] - pre_close) / pre_close
```

![](img/e4efc2f147b411aceac67affa3bd8df1_15.png)

第三步，创建布尔序列，标识出跌幅超过2%的日期。注意，跌幅为负值，跌幅越大（如-3%），其数值越小（-0.03），因此我们使用 **小于** 运算符。

![](img/e4efc2f147b411aceac67affa3bd8df1_17.png)

```python
condition = pct_change < -0.02
```

第四步，使用这个布尔序列作为行索引来筛选原始数据，得到所有满足条件的行数据。

![](img/e4efc2f147b411aceac67affa3bd8df1_19.png)

![](img/e4efc2f147b411aceac67affa3bd8df1_21.png)

```python
result_df = df.loc[condition]
```

最后，我们只需要日期信息，可以从筛选结果的索引中获取。

![](img/e4efc2f147b411aceac67affa3bd8df1_23.png)

```python
drop_dates = result_df.index
```

![](img/e4efc2f147b411aceac67affa3bd8df1_25.png)

以上步骤可以合并为一行简洁的代码：

![](img/e4efc2f147b411aceac67affa3bd8df1_27.png)

```python
drop_dates = df.loc[(df[‘open’] - df[‘close’].shift(1)) / df[‘close’].shift(1) < -0.02].index
```

## 关键点回顾

![](img/e4efc2f147b411aceac67affa3bd8df1_29.png)

以下是本教程的几个关键操作：
*   **`shift(1)` 方法**：用于获取时间序列中前一个周期的值，是金融数据分析中的常用操作。
*   **布尔索引**：`df.loc[boolean_series]` 是Pandas中根据条件筛选数据的核心方法。
*   **比较运算符的方向**：计算跌幅时，要清楚 `-0.03 < -0.02` 成立，因此使用 `<` 号。

![](img/e4efc2f147b411aceac67affa3bd8df1_31.png)

## 总结

![](img/e4efc2f147b411aceac67affa3bd8df1_33.png)

本节课中我们一起学习了如何捕获股票特定跌幅的日期。我们首先明确了需求，然后将其拆解为计算前日收盘价、计算涨跌幅、条件筛选和结果提取几个步骤，并最终用一行高效的Pandas代码实现了整个逻辑。这个方法不仅适用于此需求，也是处理任何基于时间序列条件筛选问题的通用思路。