# Python量化分析：P1：八行代码计算RSI指标 📈

在本节课中，我们将学习如何使用Python快速计算一个重要的技术分析指标——相对强弱指数。RSI指标用于衡量资产价格变动的速度和幅度，是判断市场超买或超卖状态的常用工具。我们将通过一个简洁的八行代码示例，来理解其核心计算逻辑。

## 核心概念与数据准备

在开始编写代码之前，我们需要理解RSI的计算原理。RSI的计算基于价格在一段时间内的涨跌幅度。其核心公式如下：

**RSI = 100 - 100 / (1 + RS)**

其中，**RS = 平均上涨幅度 / 平均下跌幅度**。

为了计算RSI，我们首先需要一组价格数据。在Python中，我们通常使用列表或NumPy数组来存储这些数据。

以下是计算RSI所需的数据准备步骤：

```python
# 示例：假设我们有一组收盘价数据
close_prices = [100, 102, 101, 105, 107, 106, 110, 108]
```

## 计算价格变化

上一节我们介绍了RSI的公式基础，本节中我们来看看如何从原始价格数据中计算出关键的变化值。

![](img/541ad5e0f55df75ae599d6c7a821a685_0.png)

计算的第一步是求出相邻两个交易日之间的价格差值。这个差值将用于区分上涨和下跌的幅度。

以下是计算价格变化的具体步骤：

1.  使用列表推导式计算每日的价格变化。
2.  将变化值存储在一个新的列表中。

```python
deltas = [close_prices[i] - close_prices[i-1] for i in range(1, len(close_prices))]
```

## 分离上涨与下跌幅度

在得到价格变化值之后，我们需要将这些变化值分离为上涨部分和下跌部分。这是计算平均上涨和平均下跌幅度的前提。

以下是分离上涨与下跌幅度的逻辑：

1.  遍历所有价格变化值。
2.  如果变化值为正，则视为上涨幅度，下跌幅度记为0。
3.  如果变化值为负，则取其绝对值视为下跌幅度，上涨幅度记为0。

```python
gains = [delta if delta > 0 else 0 for delta in deltas]
losses = [-delta if delta < 0 else 0 for delta in deltas]
```

## 计算平均涨跌幅与RSI值

现在我们已经有了上涨和下跌的幅度列表，接下来需要计算它们的平均值，并最终套用公式得到RSI值。这里我们使用简单移动平均的方法。

![](img/541ad5e0f55df75ae599d6c7a821a685_2.png)

以下是计算平均涨跌幅与RSI值的步骤：

1.  计算指定周期内（例如14天）上涨幅度的平均值。
2.  计算相同周期内下跌幅度的平均值。
3.  计算相对强度RS。
4.  将RS代入RSI公式，得到最终指标值。

```python
# 假设我们计算14日RSI
period = 14
avg_gain = sum(gains[-period:]) / period
avg_loss = sum(losses[-period:]) / period

rs = avg_gain / avg_loss if avg_loss != 0 else float('inf')
rsi = 100 - (100 / (1 + rs))
```

## 代码整合与运行

我们将前面分解的步骤整合成一个完整的函数。这个函数接受价格列表和计算周期作为参数，并返回对应的RSI值。

以下是完整的八行代码函数：

```python
def calculate_rsi(prices, period=14):
    deltas = [prices[i] - prices[i-1] for i in range(1, len(prices))]
    gains = [d if d > 0 else 0 for d in deltas]
    losses = [-d if d < 0 else 0 for d in deltas]
    avg_gain = sum(gains[-period:]) / period
    avg_loss = sum(losses[-period:]) / period
    rs = avg_gain / avg_loss if avg_loss != 0 else float('inf')
    return 100 - (100 / (1 + rs))

# 使用示例
rsi_value = calculate_rsi(close_prices)
print(f"RSI值为: {rsi_value}")
```

本节课中我们一起学习了RSI指标的核心概念与计算方法。我们从公式推导开始，逐步完成了数据准备、计算价格变化、分离涨跌幅度以及最终整合代码的完整流程。通过这个简洁的八行代码示例，你可以快速掌握计算RSI指标的基本技能，并应用于自己的量化分析项目中。