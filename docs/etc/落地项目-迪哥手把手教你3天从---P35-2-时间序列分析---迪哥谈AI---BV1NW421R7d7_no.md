# 金融量化：P35：2-时间序列分析 📈

在本节课中，我们将学习如何使用Python进行时间序列分析，并以微软股票数据为例，从数据读取、可视化到初步分析，手把手带你完成一个金融时间序列项目的入门实践。

---

## 数据读取与初步观察 📊

首先，我们需要读取股票数据。这里选择微软的股票进行分析。

![](img/7d725903bda4192289ed2838f69620c8_1.png)

```python
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt

![](img/7d725903bda4192289ed2838f69620c8_2.png)

# 读取微软股票数据
stock = yf.Ticker("MSFT")
hist = stock.history(period="max")
```

![](img/7d725903bda4192289ed2838f69620c8_4.png)

![](img/7d725903bda4192289ed2838f69620c8_5.png)

执行上述代码后，我们获得了微软股票的历史数据。数据中包含多个指标，例如日期、开盘价、收盘价、最高价、最低价和交易量等。我们主要关注的是收盘价，即`Close`列，它将作为我们预测的目标变量。

---

## 数据探索与预处理 🔍

上一节我们介绍了如何读取数据，本节中我们来看看数据的结构和基本属性。

以下是数据探索的关键步骤：

1.  **查看数据基本信息**：使用`hist.info()`和`hist.head()`查看数据的结构、数据类型和前几行。
2.  **提取关键序列**：我们将日期列`Date`作为时间索引`ds`，收盘价列`Close`作为目标变量`y`。
    ```python
    df = hist.reset_index()
    df['ds'] = df['Date']
    df['y'] = df['Close']
    ```
3.  **计算时间范围**：确定数据集的起始日期和结束日期。
    ```python
    start_date = df['ds'].min()
    end_date = df['ds'].max()
    ```
4.  **计算极值点**：找出历史最高价、最低价及其发生的日期。
    ```python
    max_price = df['y'].max()
    min_price = df['y'].min()
    max_date = df.loc[df['y'].idxmax(), 'ds']
    min_date = df.loc[df['y'].idxmin(), 'ds']
    ```

通过这些步骤，我们对数据有了基本的了解，为后续的可视化和分析奠定了基础。

---

## 数据可视化 📉

在掌握了数据的基本情况后，接下来我们通过绘图来直观地观察股票价格随时间的变化趋势。

以下是绘制股票价格走势图的方法：

1.  **绘制整体趋势图**：使用Matplotlib绘制从起始日期到结束日期的收盘价曲线。
    ```python
    plt.figure(figsize=(12,6))
    plt.plot(df['ds'], df['y'], label='Close Price', color='red', linewidth=1)
    plt.xlabel('Date')
    plt.ylabel('Price (USD)')
    plt.title('Microsoft Stock Price History')
    plt.legend()
    plt.grid(True)
    plt.show()
    ```
2.  **绘制特定时间段**：可以指定起始和结束日期，只观察特定时期（如2001年至2018年）的走势。
3.  **多指标对比**：除了收盘价，还可以将交易量`Volume`或价格日变化`Change`等指标绘制在同一图中进行对比分析。

从绘制的图中可以看出，微软股票价格在长期内呈现上升趋势，但在某些时期（如2000年左右）也存在明显的下跌。

---

## 简单的回测模拟 💹

![](img/7d725903bda4192289ed2838f69620c8_7.png)

为了更深入地理解时间序列数据在金融中的应用，我们可以进行一个简单的“买入并持有”策略回测。

![](img/7d725903bda4192289ed2838f69620c8_9.png)

以下是模拟买入持有策略的步骤：

![](img/7d725903bda4192289ed2838f69620c8_11.png)

![](img/7d725903bda4192289ed2838f69620c8_12.png)

![](img/7d725903bda4192289ed2838f69620c8_13.png)

![](img/7d725903bda4192289ed2838f69620c8_14.png)

1.  **定义策略**：假设在某个起始日期`buy_date`买入一定数量（如100股）的股票，并持有到终止日期`sell_date`。
2.  **计算收益**：
    ```python
    buy_price = df.loc[df['ds'] == buy_date, 'y'].values[0]
    sell_price = df.loc[df['ds'] == sell_date, 'y'].values[0]
    profit = (sell_price - buy_price) * shares_held
    ```
3.  **可视化结果**：将持有期内的股价走势和买入卖出点标注在图上，直观展示盈亏情况。

通过这个简单的模拟，我们可以感受不同时间点买入对最终收益的影响，例如在1999年高点买入与在1986年低点买入的结果截然不同。

![](img/7d725903bda4192289ed2838f69620c8_16.png)

---

![](img/7d725903bda4192289ed2838f69620c8_18.png)

## 总结与展望 🎯

![](img/7d725903bda4192289ed2838f69620c8_19.png)

![](img/7d725903bda4192289ed2838f69620c8_20.png)

![](img/7d725903bda4192289ed2838f69620c8_21.png)

![](img/7d725903bda4192289ed2838f69620c8_22.png)

![](img/7d725903bda4192289ed2838f69620c8_23.png)

本节课中我们一起学习了金融时间序列分析的基础流程：
1.  使用`yfinance`库获取股票数据。
2.  对数据进行探索性分析，提取关键信息。
3.  使用`matplotlib`绘制时间序列图，观察趋势和模式。
4.  进行简单的“买入持有”策略回测，理解时间点选择的重要性。

![](img/7d725903bda4192289ed2838f69620c8_24.png)

![](img/7d725903bda4192289ed2838f69620c8_26.png)

![](img/7d725903bda4192289ed2838f69620c8_27.png)

这些步骤构成了时间序列分析项目的基石。在接下来的课程中，我们将引入更强大的预测工具（如Facebook Prophet框架），学习如何构建模型来预测未来的股票价格走势。