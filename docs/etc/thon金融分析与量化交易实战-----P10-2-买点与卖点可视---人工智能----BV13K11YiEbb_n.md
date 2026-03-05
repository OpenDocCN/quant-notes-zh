# Python金融分析与量化交易实战：P10：2-买点与卖点可视化分析 📈

![](img/e541b7e04794df94d076be67198f72d2_0.png)

![](img/e541b7e04794df94d076be67198f72d2_2.png)

![](img/e541b7e04794df94d076be67198f72d2_4.png)

在本节课中，我们将学习如何利用Python进行股票数据的可视化分析，并基于移动平均线策略识别潜在的买入和卖出信号。我们将从数据预处理开始，计算短期和长期移动平均线，并最终通过图表直观地展示这些信号点。

![](img/e541b7e04794df94d076be67198f72d2_6.png)

![](img/e541b7e04794df94d076be67198f72d2_8.png)

## 数据导入与预处理

![](img/e541b7e04794df94d076be67198f72d2_10.png)

![](img/e541b7e04794df94d076be67198f72d2_12.png)

首先，我们需要导入必要的工具包并加载数据。以下是导入和读取数据的基本步骤。

![](img/e541b7e04794df94d076be67198f72d2_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/e541b7e04794df94d076be67198f72d2_16.png)

![](img/e541b7e04794df94d076be67198f72d2_18.png)

![](img/e541b7e04794df94d076be67198f72d2_20.png)

# 读取数据，并将第一列解析为日期索引
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/e541b7e04794df94d076be67198f72d2_22.png)

![](img/e541b7e04794df94d076be67198f72d2_24.png)

数据加载完成后，我们选择苹果公司的股票数据作为分析对象，并处理数据中可能存在的缺失值，以确保后续计算的准确性。

![](img/e541b7e04794df94d076be67198f72d2_26.png)

```python
# 选择苹果公司的股票数据列
apple_data = data[['AAPL']].copy()

![](img/e541b7e04794df94d076be67198f72d2_28.png)

# 删除缺失值
apple_data.dropna(inplace=True)
```

![](img/e541b7e04794df94d076be67198f72d2_30.png)

![](img/e541b7e04794df94d076be67198f72d2_32.png)

## 计算移动平均线

![](img/e541b7e04794df94d076be67198f72d2_34.png)

上一节我们完成了数据的基础准备，本节中我们来看看如何计算关键的移动平均线指标。移动平均线是技术分析中常用的工具，用于平滑价格数据并识别趋势。

以下是计算短期和长期简单移动平均线的步骤：

![](img/e541b7e04794df94d076be67198f72d2_36.png)

1.  **定义窗口大小**：首先，我们需要设定短期和长期移动平均线的计算周期。考虑到数据量较大，我们使用较大的窗口以便于观察。
    ```python
    short_window = 40
    long_window = 100
    ```
2.  **计算移动平均值**：使用Pandas的`rolling`和`mean`方法，分别计算短期和长期移动平均线，并将结果添加到数据框中。
    ```python
    apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()
    apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()
    ```
3.  **清理计算产生的缺失值**：由于移动平均计算在数据开头会产生NaN值，我们需要将其删除。
    ```python
    apple_data.dropna(inplace=True)
    ```

![](img/e541b7e04794df94d076be67198f72d2_38.png)

![](img/e541b7e04794df94d076be67198f72d2_40.png)

## 识别买卖信号点

![](img/e541b7e04794df94d076be67198f72d2_42.png)

![](img/e541b7e04794df94d076be67198f72d2_44.png)

计算完移动平均线后，我们可以通过它们之间的交叉关系来识别买卖信号。这是双移动平均线策略的核心。

![](img/e541b7e04794df94d076be67198f72d2_46.png)

![](img/e541b7e04794df94d076be67198f72d2_48.png)

以下是识别和标记信号点的步骤：

![](img/e541b7e04794df94d076be67198f72d2_50.png)

![](img/e541b7e04794df94d076be67198f72d2_52.png)

1.  **创建信号标志位**：我们创建一个新的列`Position`，当短期均线高于长期均线时标记为1，否则标记为0。这代表了“持有”或“看涨”的信号。
    ```python
    apple_data['Position'] = np.where(apple_data['SMA_short'] > apple_data['SMA_long'], 1, 0)
    ```
2.  **理解信号含义**：
    *   **黄金交叉**：当短期均线从下方上穿长期均线时（`Position`从0变为1），通常被视为**买入信号**。
    *   **死亡交叉**：当短期均线从上方下穿长期均线时（`Position`从1变为0），通常被视为**卖出信号**。

![](img/e541b7e04794df94d076be67198f72d2_54.png)

![](img/e541b7e04794df94d076be67198f72d2_56.png)

## 结果可视化

![](img/e541b7e04794df94d076be67198f72d2_58.png)

理论需要结合实践来观察。现在，我们将原始股价与两条移动平均线一同绘制在图表上，直观地观察交叉点。

```python
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='Apple Price', alpha=0.5)
plt.plot(apple_data['SMA_short'], label=f'{short_window}-Day SMA', color='orange')
plt.plot(apple_data['SMA_long'], label=f'{long_window}-Day SMA', color='purple')

![](img/e541b7e04794df94d076be67198f72d2_60.png)

![](img/e541b7e04794df94d076be67198f72d2_62.png)

plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/e541b7e04794df94d076be67198f72d2_64.png)

![](img/e541b7e04794df94d076be67198f72d2_66.png)

在生成的图表中，您可以清晰地看到短期均线（橙色）与长期均线（紫色）的交叉点。这些交叉点对应的就是策略提示的潜在买卖时机。

![](img/e541b7e04794df94d076be67198f72d2_68.png)

![](img/e541b7e04794df94d076be67198f72d2_70.png)

为了更清晰地展示持仓信号，我们可以将`Position`标志位以背景色的形式绘制在另一幅图中。

![](img/e541b7e04794df94d076be67198f72d2_72.png)

```python
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

![](img/e541b7e04794df94d076be67198f72d2_74.png)

ax1.plot(apple_data['AAPL'], label='Price')
ax1.plot(apple_data['SMA_short'], label='Short SMA')
ax1.plot(apple_data['SMA_long'], label='Long SMA')
ax1.set_ylabel('Price')
ax1.legend()
ax1.set_title('Price and Moving Averages')

![](img/e541b7e04794df94d076be67198f72d2_76.png)

# 在第二个子图中用背景色显示持仓信号
ax2.fill_between(apple_data.index, 0, apple_data['Position'], color='green', alpha=0.3, label='Hold Signal (Position=1)')
ax2.set_ylabel('Position')
ax2.set_xlabel('Date')
ax2.legend(loc='upper left')
ax2.set_title('Trading Position Signal')
ax2.set_ylim(-0.1, 1.1)

![](img/e541b7e04794df94d076be67198f72d2_78.png)

plt.tight_layout()
plt.show()
```

在这张图中，绿色背景区域表示`Position=1`的时期，即短期均线在长期均线之上，策略建议持有或看涨的时段。

![](img/e541b7e04794df94d076be67198f72d2_80.png)

![](img/e541b7e04794df94d076be67198f72d2_82.png)

## 策略逻辑与收益思考

![](img/e541b7e04794df94d076be67198f72d2_84.png)

通过可视化，我们理解了信号的产生。现在我们来探讨这个策略背后的逻辑及其对收益的潜在影响。

*   **被动持有策略**：如果投资者在最早的时间点买入股票并一直持有到最后，其收益完全取决于股价的最终涨跌。
*   **双均线主动策略**：本课构建的策略试图在“死亡交叉”时卖出（或做空），在“黄金交叉”时买入（或做多）。理想情况下，这能帮助投资者避开部分下跌，抓住部分上涨，从而可能获得比单纯持有更高的**风险调整后收益**。

**核心思想**：策略的目标并非在股价每次下跌时都亏损，而是通过信号系统，在趋势可能反转前调整仓位，力求在长期波动中获取超额收益。例如，在识别到“死亡交叉”后卖出，即使股价继续下跌，也已锁定部分利润或减少亏损；待“黄金交叉”出现后再买入，参与后续的上涨行情。

![](img/e541b7e04794df94d076be67198f72d2_86.png)

---

![](img/e541b7e04794df94d076be67198f72d2_88.png)

本节课中我们一起学习了如何利用Python进行金融数据的可视化分析。我们从数据预处理开始，计算了短期和长期移动平均线，并基于它们的交叉关系定义了买卖信号点。通过绘制图表，我们直观地观察了“黄金交叉”和“死亡交叉”的出现位置。最后，我们探讨了这种双移动平均线策略的基本逻辑，它旨在通过主动的仓位管理来优化投资回报。这是构建自动化量化交易策略非常基础且重要的一步。