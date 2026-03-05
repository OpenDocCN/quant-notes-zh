# Python量化分析：P1：获取KDJ与MACD指标数值 📈

在本节课中，我们将学习如何使用Python计算金融市场中两个常用的技术分析指标：KDJ和MACD。我们将从理解指标的基本概念开始，逐步讲解其计算公式，并最终用Python代码实现数值的获取。通过本教程，即使是编程初学者也能掌握这两个核心指标的计算方法。

## KDJ指标：概念与计算 🧮

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_1.png)

KDJ指标是一个用于判断市场超买和超卖状态的常用技术指标，尤其适用于中短期趋势分析。

上一节我们介绍了课程概述，本节中我们来看看KDJ指标的具体计算方法。

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_3.png)

KDJ指标的计算主要依赖于特定周期内的最高价、最低价和收盘价。其核心是计算未成熟随机值（RSV），并在此基础上得出K线、D线和J线。

以下是KDJ指标的计算步骤：

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_5.png)

1.  计算最近N日（通常N=9）的最高价（`HIGH_N`）和最低价（`LOW_N`）。
2.  计算第N日的未成熟随机值（RSV），公式为：
    **RSV = (CLOSE - LOW_N) / (HIGH_N - LOW_N) * 100**
    其中，`CLOSE`为当日收盘价。
3.  计算当日的K值、D值和J值。通常，当日K值为前一日K值的2/3加上当日RSV的1/3；当日D值为前一日D值的2/3加上当日K值的1/3；J值为`3*K - 2*D`。初始值可设为50。
    **K = (2/3) * PREV_K + (1/3) * RSV**
    **D = (2/3) * PREV_D + (1/3) * K**
    **J = 3 * K - 2 * D**

## KDJ指标：Python实现 💻

理解了KDJ的计算原理后，本节我们将通过Python代码来实现它。我们将使用`pandas`库来处理金融时间序列数据。

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_7.png)

以下是获取KDJ指标数值的Python代码示例：

```python
import pandas as pd

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_9.png)

def calculate_kdj(data, n=9):
    """
    计算KDJ指标
    :param data: DataFrame，包含‘high’, ‘low’, ‘close’列
    :param n: 计算周期，默认为9
    :return: 添加了‘K’，‘D’，‘J’列的DataFrame
    """
    # 计算N日内最高价和最低价
    low_list = data[‘low’].rolling(window=n, min_periods=1).min()
    high_list = data[‘high’].rolling(window=n, min_periods=1).max()
    
    # 计算RSV
    data[‘RSV’] = (data[‘close’] - low_list) / (high_list - low_list) * 100
    
    # 初始化K，D值（这里简单以RSV的EMA计算，实际初始值处理方式多样）
    data[‘K’] = data[‘RSV’].ewm(alpha=1/3, adjust=False).mean()
    data[‘D’] = data[‘K’].ewm(alpha=1/3, adjust=False).mean()
    data[‘J’] = 3 * data[‘K’] - 2 * data[‘D’]
    
    return data

# 示例：假设df是包含股价数据的DataFrame
# df = pd.read_csv(‘your_stock_data.csv’)
# kdj_df = calculate_kdj(df)
# print(kdj_df[[‘K‘， ’D‘， ’J‘]].tail())
```
运行上述代码后，你将得到包含K值、D值和J值的数据。例如，最新一行的K、D、J值可能分别为32.26、44.00和8.78（示例数值，基于特定数据计算得出）。

## MACD指标：概念与计算 📊

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_11.png)

接下来，我们学习另一个极其重要的趋势指标——MACD。MACD通过计算不同周期指数移动平均线（EMA）之间的关系，来研判股票价格趋势的强度、方向、动量和周期。

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_13.png)

上一节我们完成了KDJ指标的计算，本节中我们来看看MACD指标是如何构建的。

MACD指标由三部分组成：DIF线、DEA线（或称MACD线）和MACD柱状图。其计算围绕短期和长期的指数移动平均线展开。

以下是MACD指标的计算步骤：

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_15.png)

1.  计算短期（通常为12日）指数移动平均线（`EMA_SHORT`）和长期（通常为26日）指数移动平均线（`EMA_LONG`）。
2.  计算差离值（DIF线），公式为：
    **DIF = EMA_SHORT - EMA_LONG**
3.  计算DIF线的M日（通常为9日）指数移动平均线，得到DEA线（或称MACD信号线）。
    **DEA = EMA(DIF, M)**
4.  计算MACD柱状图（BAR），公式为：
    **BAR = (DIF - DEA) * 2**

## MACD指标：Python实现 🖥️

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_17.png)

掌握了MACD的计算逻辑后，我们现在用Python代码来获取它的数值。同样，`pandas`库的`ewm`函数将帮助我们高效计算指数移动平均线。

以下是获取MACD指标数值的Python代码示例：

```python
def calculate_macd(data, short=12, long=26, m=9):
    """
    计算MACD指标
    :param data: DataFrame，需包含‘close’列
    :param short: 短期EMA周期
    :param long: 长期EMA周期
    :param m: DEA的计算周期
    :return: 添加了‘DIF’，‘DEA’，‘MACD’列的DataFrame
    """
    # 计算短期和长期EMA
    data[‘EMA_SHORT’] = data[‘close’].ewm(span=short, adjust=False).mean()
    data[‘EMA_LONG’] = data[‘close’].ewm(span=long, adjust=False).mean()
    
    # 计算DIF线
    data[‘DIF’] = data[‘EMA_SHORT’] - data[‘EMA_LONG’]
    
    # 计算DEA线
    data[‘DEA’] = data[‘DIF’].ewm(span=m, adjust=False).mean()
    
    # 计算MACD柱状图
    data[‘MACD’] = (data[‘DIF’] - data[‘DEA’]) * 2
    
    return data

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_19.png)

# 示例：假设df是包含股价数据的DataFrame
# macd_df = calculate_macd(df)
# print(macd_df[[‘DIF‘， ’DEA‘， ’MACD‘]].tail())
```
运行代码后，你将得到DIF、DEA和MACD柱的数值。例如，结果可能显示DIF为-1.49，DEA为-1.70，MACD柱为0.42（示例数值），这些数据可用于分析价格趋势的动量和潜在转折点。

![](img/4bcfe703bd51f287ea5d55fd732c9ae9_21.png)

## 总结 📝

本节课中我们一起学习了两个关键的技术分析指标。我们首先探讨了用于判断超买超卖的KDJ指标，理解了其基于最高价、最低价和收盘价计算RSV，进而推导出K、D、J线的过程，并用Python实现了该计算。随后，我们学习了用于识别趋势强度和方向的MACD指标，掌握了通过短期与长期EMA计算DIF、DEA及MACD柱状图的方法，并同样用代码进行了实现。掌握这两个指标的获取方法，是进行量化分析和策略构建的重要基础。