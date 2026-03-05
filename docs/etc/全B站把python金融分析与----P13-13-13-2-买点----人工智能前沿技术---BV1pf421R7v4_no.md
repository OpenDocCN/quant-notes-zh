# Python金融量化与股票交易：13.2：买点与卖点可视化分析 📈

![](img/53cc833c01bda35b61b978f3b5bbc573_0.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_2.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_4.png)

在本节课中，我们将学习如何利用Python进行股票数据的可视化分析，特别是通过计算短期和长期移动平均线来识别潜在的买入和卖出信号。我们将从数据预处理开始，逐步计算均线，并最终通过图表直观地展示这些交易信号。

![](img/53cc833c01bda35b61b978f3b5bbc573_6.png)

---

![](img/53cc833c01bda35b61b978f3b5bbc573_8.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_10.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_12.png)

## 数据准备与预处理

首先，我们需要导入必要的工具包并加载数据。以下是实现这一步骤的代码。

![](img/53cc833c01bda35b61b978f3b5bbc573_14.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_16.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/53cc833c01bda35b61b978f3b5bbc573_18.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_20.png)

# 读取数据，指定第一列为时间索引
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/53cc833c01bda35b61b978f3b5bbc573_22.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_24.png)

数据加载后，我们选择苹果公司的股票数据作为分析示例，并处理数据中可能存在的缺失值。

![](img/53cc833c01bda35b61b978f3b5bbc573_26.png)

```python
# 选择苹果公司的股票数据列
apple_data = data[['AAPL']].copy()

![](img/53cc833c01bda35b61b978f3b5bbc573_28.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_30.png)

# 删除缺失值
apple_data.dropna(inplace=True)

![](img/53cc833c01bda35b61b978f3b5bbc573_32.png)

# 查看前几行数据
print(apple_data.head())
```

![](img/53cc833c01bda35b61b978f3b5bbc573_34.png)

---

## 计算移动平均线

![](img/53cc833c01bda35b61b978f3b5bbc573_36.png)

上一节我们完成了数据的基础准备，本节中我们来看看如何计算关键的短期和长期移动平均线。移动平均线是技术分析中常用的指标，用于平滑价格数据并识别趋势。

![](img/53cc833c01bda35b61b978f3b5bbc573_38.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_40.png)

以下是计算移动平均线的步骤：

![](img/53cc833c01bda35b61b978f3b5bbc573_42.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_44.png)

1.  **定义窗口期**：首先，我们需要设定短期和长期移动平均线的计算窗口大小。考虑到数据量较大，我们将短期窗口设为42天，长期窗口设为252天，以便在图表中更清晰地观察趋势。
    ```python
    short_window = 42
    long_window = 252
    ```

![](img/53cc833c01bda35b61b978f3b5bbc573_46.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_48.png)

2.  **计算移动平均值**：接着，我们使用`rolling`方法结合`mean`函数来计算指定窗口期的移动平均值。
    ```python
    # 计算短期移动平均线 (SMA)
    apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()

    # 计算长期移动平均线 (SMA)
    apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()
    ```

![](img/53cc833c01bda35b61b978f3b5bbc573_50.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_52.png)

3.  **查看结果**：计算完成后，我们可以查看数据的尾部，以确认均线值已成功添加。
    ```python
    print(apple_data.tail())
    ```

![](img/53cc833c01bda35b61b978f3b5bbc573_54.png)

---

![](img/53cc833c01bda35b61b978f3b5bbc573_56.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_58.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_60.png)

## 可视化均线与交叉点

计算完移动平均线后，我们可以通过图表来直观地观察股价走势以及短期和长期均线的交叉情况。

以下是生成图表的代码：

![](img/53cc833c01bda35b61b978f3b5bbc573_62.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_64.png)

```python
# 绘制股价与移动平均线
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='Apple Stock Price', alpha=0.5)
plt.plot(apple_data['SMA_short'], label=f'{short_window}-Day SMA', color='red')
plt.plot(apple_data['SMA_long'], label=f'{long_window}-Day SMA', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/53cc833c01bda35b61b978f3b5bbc573_66.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_68.png)

在生成的图表中，我们可以观察到：
*   **死亡交叉**：当短期均线从上向下穿过长期均线时，通常被视为卖出信号。
*   **黄金交叉**：当短期均线从下向上穿过长期均线时，通常被视为买入信号。

![](img/53cc833c01bda35b61b978f3b5bbc573_70.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_72.png)

---

![](img/53cc833c01bda35b61b978f3b5bbc573_74.png)

## 标记买卖信号

![](img/53cc833c01bda35b61b978f3b5bbc573_76.png)

为了更系统地进行策略分析，我们需要在数据中明确标记出这些交叉点所代表的买卖信号。

![](img/53cc833c01bda35b61b978f3b5bbc573_78.png)

以下是标记信号的具体步骤：

![](img/53cc833c01bda35b61b978f3b5bbc573_80.png)

1.  **清理数据**：首先，由于计算移动平均线时产生了前期的`NaN`值，我们需要将其删除。
    ```python
    apple_data.dropna(inplace=True)
    ```

![](img/53cc833c01bda35b61b978f3b5bbc573_82.png)

2.  **创建信号列**：接着，我们创建一个新的列（例如`Position`）来标记信号。当短期均线大于长期均线时，标记为`1`（代表持有或买入信号）；否则标记为`0`（代表卖出或空仓信号）。
    ```python
    apple_data['Position'] = np.where(apple_data['SMA_short'] > apple_data['SMA_long'], 1, 0)
    ```

![](img/53cc833c01bda35b61b978f3b5bbc573_84.png)

3.  **可视化信号**：我们可以将买卖信号与股价绘制在同一张图上进行观察。
    ```python
    fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

    # 第一张图：股价与均线
    ax1.plot(apple_data['AAPL'], label='Price', alpha=0.5)
    ax1.plot(apple_data['SMA_short'], label='Short SMA')
    ax1.plot(apple_data['SMA_long'], label='Long SMA')
    ax1.set_ylabel('Price')
    ax1.legend()
    ax1.set_title('Price and Moving Averages')

    # 第二张图：买卖信号
    ax2.plot(apple_data['Position'], label='Trading Signal', color='red', drawstyle='steps-post')
    ax2.set_ylabel('Signal (1=Buy, 0=Sell)')
    ax2.set_xlabel('Date')
    ax2.legend()

    plt.show()
    ```

![](img/53cc833c01bda35b61b978f3b5bbc573_86.png)

![](img/53cc833c01bda35b61b978f3b5bbc573_88.png)

---

## 策略思路探讨

通过上述分析，我们能够识别出基于双均线交叉的买卖点。这引出了一个核心的交易策略思路：**在黄金交叉点买入，在死亡交叉点卖出**。

理论上，遵循这一策略可以避免在长期下跌趋势中持有股票，并在上升趋势开始时进场，从而可能获得比简单“买入并持有”策略更好的风险调整后收益。下一节课中，我们将基于这些信号，具体计算和回测这种策略的模拟收益。

![](img/53cc833c01bda35b61b978f3b5bbc573_90.png)

---

![](img/53cc833c01bda35b61b978f3b5bbc573_92.png)

本节课中我们一起学习了如何利用Python进行股票数据的移动平均线计算与可视化，并初步探讨了如何利用均线交叉来识别买卖信号，为构建量化交易策略奠定了基础。