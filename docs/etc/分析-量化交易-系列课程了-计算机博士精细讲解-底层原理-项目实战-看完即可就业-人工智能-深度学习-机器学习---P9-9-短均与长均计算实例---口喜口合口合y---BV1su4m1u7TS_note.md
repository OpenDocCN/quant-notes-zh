# Python金融分析+量化交易：P9：短均与长均计算实例 📈

![](img/6204d1db44f04b99ce1113a91aa65a78_1.png)

在本节课中，我们将学习如何计算股票的短期移动平均线和长期移动平均线，并通过分析它们的交叉点（黄金交叉与死亡交叉）来构建一个简单的交易策略。我们将使用Python的Pandas库进行计算，并用Matplotlib进行可视化。

![](img/6204d1db44f04b99ce1113a91aa65a78_3.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_5.png)

## 概述

![](img/6204d1db44f04b99ce1113a91aa65a78_7.png)

我们将分析苹果公司的股票数据。核心任务是计算两个关键指标：短期移动平均线（例如10日）和长期移动平均线（例如30日）。通过观察这两条线的交叉情况，我们可以判断市场的潜在买入或卖出信号。

![](img/6204d1db44f04b99ce1113a91aa65a78_9.png)

## 计算短期与长期移动平均线

上一节我们介绍了移动平均线的概念，本节中我们来看看如何具体计算。

首先，我们需要加载股票数据。这里我们使用苹果公司（AAPL）的历史股价数据。

![](img/6204d1db44f04b99ce1113a91aa65a78_11.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_13.png)

```python
# 假设df是包含苹果股价的DataFrame，其中‘Close’列为收盘价
stock_price = df[‘Close’]
```

![](img/6204d1db44f04b99ce1113a91aa65a78_15.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_17.png)

接下来，我们分别计算短期和长期的简单移动平均线。简单移动平均线的计算公式为：
**SMA = (P1 + P2 + … + Pn) / n**
其中，P代表股价，n是计算周期。

![](img/6204d1db44f04b99ce1113a91aa65a78_19.png)

以下是计算步骤：

![](img/6204d1db44f04b99ce1113a91aa65a78_21.png)

1.  **计算短期移动平均线（M1）**：我们选择一个较小的窗口，例如10天。
    ```python
    short_window = 10
    df[‘M1’] = stock_price.rolling(window=short_window).mean()
    ```

![](img/6204d1db44f04b99ce1113a91aa65a78_23.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_25.png)

2.  **计算长期移动平均线（M2）**：我们选择一个较大的窗口，例如30天。
    ```python
    long_window = 30
    df[‘M2’] = stock_price.rolling(window=long_window).mean()
    ```

![](img/6204d1db44f04b99ce1113a91aa65a78_27.png)

计算完成后，我们的DataFrame中会新增两列：‘M1’和‘M2’，分别代表短期和长期移动平均线。

## 可视化移动平均线与股价

![](img/6204d1db44f04b99ce1113a91aa65a78_29.png)

计算出指标后，最直观的方式是将它们与原始股价一起绘制在图表上。

![](img/6204d1db44f04b99ce1113a91aa65a78_31.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_33.png)

以下是绘图代码：

![](img/6204d1db44f04b99ce1113a91aa65a78_35.png)

```python
import matplotlib.pyplot as plt

![](img/6204d1db44f04b99ce1113a91aa65a78_37.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_39.png)

plt.figure(figsize=(12, 6))
plt.plot(df[‘Close’], label=‘Apple Stock Price’, alpha=0.5)
plt.plot(df[‘M1’], label=‘Short-term MA (10 days)’, color=‘green’)
plt.plot(df[‘M2’], label=‘Long-term MA (30 days)’, color=‘red’)
plt.legend()
plt.show()
```

为了更清晰地观察交叉点，我们可以截取一段时间的数据进行绘图，例如最近250个交易日（约一年）。

![](img/6204d1db44f04b99ce1113a91aa65a78_41.png)

## 识别黄金交叉与死亡交叉

![](img/6204d1db44f04b99ce1113a91aa65a78_43.png)

图表绘制完成后，我们可以开始寻找关键的交叉信号。

![](img/6204d1db44f04b99ce1113a91aa65a78_45.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_47.png)

以下是两种交叉信号的定义：

![](img/6204d1db44f04b99ce1113a91aa65a78_49.png)

*   **黄金交叉**：短期移动平均线从下方上穿长期移动平均线。这通常被视为**买入信号**，预示着上涨趋势可能开始。
*   **死亡交叉**：短期移动平均线从上方下穿长期移动平均线。这通常被视为**卖出信号**，预示着下跌趋势可能开始。

![](img/6204d1db44f04b99ce1113a91aa65a78_51.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_53.png)

在图表中，当绿色的短期均线（M1）向上穿过红色的长期均线（M2）时，即出现黄金交叉。反之，当绿色线向下穿过红色线时，即出现死亡交叉。

![](img/6204d1db44f04b99ce1113a91aa65a78_55.png)

## 使用数值信号增强分析

为了更精确地定位交叉点，我们可以创建一个信号列，用数值明确标识短期均线相对于长期均线的位置。

![](img/6204d1db44f04b99ce1113a91aa65a78_57.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_59.png)

以下是创建信号列的步骤：

![](img/6204d1db44f04b99ce1113a91aa65a78_61.png)

1.  **使用`np.where`函数进行比较**：当短期均线大于长期均线时，标记为1；小于时，标记为-1。
    ```python
    import numpy as np
    df[‘position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
    ```

2.  **在次坐标轴上绘制信号**：由于信号值（1和-1）与股价尺度差异巨大，我们将其绘制在右侧的次坐标轴上以便观察。
    ```python
    fig, ax1 = plt.subplots(figsize=(12, 6))

    ax1.plot(df[‘Close’], label=‘Price’, color=‘blue’, alpha=0.3)
    ax1.plot(df[‘M1’], label=‘M1 (10d)’, color=‘green’)
    ax1.plot(df[‘M2’], label=‘M2 (30d)’, color=‘red’)
    ax1.set_ylabel(‘Price’)
    ax1.legend(loc=‘upper left’)

    ax2 = ax1.twinx()
    ax2.plot(df[‘position’], label=‘Signal’, color=‘purple’, linestyle=‘--’)
    ax2.set_ylabel(‘Signal (1/-1)’)
    ax2.legend(loc=‘upper right’)

    plt.show()
    ```

![](img/6204d1db44f04b99ce1113a91aa65a78_63.png)

在最终的图表中，紫色虚线（信号线）在+1和-1之间切换。**信号线从-1变为+1的点对应黄金交叉（买入点），从+1变为-1的点对应死亡交叉（卖出点）**。这样，交易信号就变得一目了然。

![](img/6204d1db44f04b99ce1113a91aa65a78_65.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_67.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_69.png)

## 总结

![](img/6204d1db44f04b99ce1113a91aa65a78_71.png)

![](img/6204d1db44f04b99ce1113a91aa65a78_73.png)

本节课中我们一起学习了移动平均线交叉策略的核心实践。我们首先计算了股票的短期和长期简单移动平均线，然后通过可视化图表观察了“黄金交叉”和“死亡交叉”这两种重要的技术信号。最后，我们通过创建数值信号列，并利用双坐标轴绘图，更清晰、更量化地标识出了具体的交叉时点。这个方法为后续构建自动化交易策略打下了基础。在实际应用中，可以根据需要调整均线的周期，并结合其他指标进行综合判断。