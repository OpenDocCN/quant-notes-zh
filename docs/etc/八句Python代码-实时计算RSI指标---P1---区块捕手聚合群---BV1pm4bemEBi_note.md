# Python量化交易：P1：用八句代码实时计算RSI指标

在本节课中，我们将学习如何使用Python，仅用八行核心代码，实时计算一个重要的技术分析指标——相对强弱指数（RSI）。RSI是衡量价格变动速度和变化的动量震荡指标，常用于判断市场的超买或超卖状态。

## 核心概念与公式

RSI的计算基于特定周期内（通常为14天）价格的平均涨幅和平均跌幅。其核心公式如下：

**RSI = 100 - (100 / (1 + RS))**

其中，**RS（相对强度）** 的计算公式为：

**RS = 平均涨幅 / 平均跌幅**

平均涨幅和平均跌幅通常使用指数移动平均（EMA）或简单移动平均（SMA）来计算，以平滑数据并赋予近期价格更大的权重。

## 代码实现步骤

理解了RSI的计算原理后，我们来看看如何用Python代码实现它。以下是实现实时RSI计算的八个关键步骤。

### 1. 导入必要库

首先，我们需要导入用于数据处理和计算的库。

```python
import pandas as pd
import numpy as np
```

![](img/170ff83a0efaff5807f2c3cad49f6b50_0.png)

### 2. 准备价格数据

假设我们有一个包含收盘价的历史数据序列 `close_prices`。这是计算的基础。

```python
close_prices = [100, 102, 101, 105, 107, 106, 110, 108]
```

### 3. 计算价格变化

计算相邻两个交易日之间的价格差值，即涨跌幅度。

```python
delta = pd.Series(close_prices).diff()
```

### 4. 分离涨幅与跌幅

将价格变化分离为上涨部分和下跌部分。上涨值取正数，下跌值取其绝对值。

```python
gain = delta.where(delta > 0, 0)
loss = -delta.where(delta < 0, 0)
```

### 5. 计算平均涨幅与平均跌幅

这里我们使用简单移动平均（SMA）来计算过去N个周期（例如14）的平均涨幅和平均跌幅。`rolling(window).mean()` 函数可以实现移动平均计算。

```python
avg_gain = gain.rolling(window=14).mean()
avg_loss = loss.rolling(window=14).mean()
```

### 6. 计算相对强度（RS）

根据公式，用平均涨幅除以平均跌幅得到相对强度RS。为避免除以零的错误，需要处理平均损失为零的情况。

```python
rs = avg_gain / avg_loss
```

![](img/170ff83a0efaff5807f2c3cad49f6b50_2.png)

### 7. 计算RSI值

最后，将RS代入RSI的主公式，计算出0到100之间的RSI值。

```python
rsi = 100 - (100 / (1 + rs))
```

### 8. 处理初始数据

由于使用了移动平均，前13个周期（当窗口为14时）可能没有足够的计算数据，会产生空值（NaN）。我们可以用后续的有效值向前填充。

```python
rsi = rsi.fillna(method=‘bfill’)
```

## 代码整合与示例

将以上步骤整合，就得到了一个简洁的RSI计算函数。以下是完整的代码示例：

```python
import pandas as pd
import numpy as np

def calculate_rsi(prices, window=14):
    """计算RSI指标"""
    delta = pd.Series(prices).diff()
    gain = delta.where(delta > 0, 0)
    loss = -delta.where(delta < 0, 0)
    avg_gain = gain.rolling(window=window).mean()
    avg_loss = loss.rolling(window=window).mean()
    rs = avg_gain / avg_loss
    rsi = 100 - (100 / (1 + rs))
    return rsi.fillna(method=‘bfill’)

# 使用示例
close_prices = [100, 102, 101, 105, 107, 106, 110, 108, 112, 115, 114, 116, 118, 120, 119, 121]
rsi_values = calculate_rsi(close_prices, window=14)
print(rsi_values.tail())
```

## 总结

本节课中，我们一起学习了RSI指标的核心原理和计算公式，并通过八个清晰的步骤，用Python实现了RSI的实时计算。从导入库、处理数据，到分离涨跌、计算移动平均，最终得到RSI值，整个过程逻辑严密且代码简洁。掌握这个方法后，你就可以将其应用于自己的量化分析或交易策略中，作为判断市场动量的一种有效工具。