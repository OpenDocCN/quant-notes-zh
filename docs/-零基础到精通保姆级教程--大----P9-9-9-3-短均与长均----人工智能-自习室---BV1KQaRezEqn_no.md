# Python金融量化交易：9.9.3：短均与长均计算实例 📈

![](img/d2febf09308fc03658e573ee4e6f2c21_1.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_3.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的分析策略。我们将识别“黄金交叉”和“死亡交叉”这两种关键信号，并学习如何用代码实现和可视化它们。

![](img/d2febf09308fc03658e573ee4e6f2c21_5.png)

## 概述

![](img/d2febf09308fc03658e573ee4e6f2c21_7.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_9.png)

我们将分析苹果公司的股票数据。核心策略是计算两个移动平均线：一个短期（例如10天）和一个长期（例如30天）。通过观察这两条线的交叉点，我们可以判断市场的潜在买入或卖出信号。

## 计算短期与长期移动平均线

![](img/d2febf09308fc03658e573ee4e6f2c21_11.png)

首先，我们需要从数据中计算出短期和长期的移动平均值。移动平均线能平滑价格波动，帮助我们看清趋势。

![](img/d2febf09308fc03658e573ee4e6f2c21_13.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_15.png)

以下是计算步骤的代码实现：

![](img/d2febf09308fc03658e573ee4e6f2c21_17.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_19.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_21.png)

```python
# 假设 df 是包含苹果股票价格（例如‘AAPL’列）的DataFrame
# 计算短期移动平均线（例如10天窗口）
short_window = 10
df[‘M1’] = df[‘AAPL’].rolling(window=short_window).mean()

![](img/d2febf09308fc03658e573ee4e6f2c21_23.png)

# 计算长期移动平均线（例如30天窗口）
long_window = 30
df[‘M2’] = df[‘AAPL’].rolling(window=long_window).mean()
```

![](img/d2febf09308fc03658e573ee4e6f2c21_25.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_27.png)

代码解释：
*   `df[‘AAPL’]` 选择了我们要分析的苹果股价列。
*   `.rolling(window=10)` 创建了一个大小为10的滚动窗口。
*   `.mean()` 计算了这个窗口内数据的平均值，从而得到移动平均线。

![](img/d2febf09308fc03658e573ee4e6f2c21_29.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_31.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_33.png)

## 可视化股价与移动平均线

计算出指标后，最直观的方式就是将它们绘制在图表上。这样我们可以清晰地看到股价走势以及两条平均线的相互位置。

以下是绘制股价和两条移动平均线的代码：

![](img/d2febf09308fc03658e573ee4e6f2c21_35.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_37.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_39.png)

```python
import matplotlib.pyplot as plt

![](img/d2febf09308fc03658e573ee4e6f2c21_41.png)

# 绘制图表
plt.figure(figsize=(12, 6))
plt.plot(df[‘AAPL’], label=‘Apple Stock Price’, alpha=0.5)
plt.plot(df[‘M1’], label=‘Short-term MA (10-day)’, color=‘green’)
plt.plot(df[‘M2’], label=‘Long-term MA (30-day)’, color=‘red’)
plt.legend()
plt.show()
```

![](img/d2febf09308fc03658e573ee4e6f2c21_43.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_45.png)

在生成的图表中，我们可以观察：
*   **蓝色线（或原色线）**：代表苹果股票的实际价格。
*   **绿色线**：代表10天短期移动平均线。
*   **红色线**：代表30天长期移动平均线。

## 识别黄金交叉与死亡交叉

![](img/d2febf09308fc03658e573ee4e6f2c21_47.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_49.png)

上一节我们绘制了图表，本节中我们来看看如何解读图表中的关键信号——“黄金交叉”和“死亡交叉”。

![](img/d2febf09308fc03658e573ee4e6f2c21_51.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_53.png)

这两个交叉点是移动平均线策略的核心：
*   **黄金交叉**：当短期移动平均线从下方**上穿**长期移动平均线时，通常被视为**买入信号**，预示着上涨趋势可能开始。
*   **死亡交叉**：当短期移动平均线从上方**下穿**长期移动平均线时，通常被视为**卖出信号**，预示着下跌趋势可能开始。

![](img/d2febf09308fc03658e573ee4e6f2c21_55.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_57.png)

在图表上，你可以寻找绿色线（短期）与红色线（长期）的交叉点，并根据交叉方向判断信号类型。

![](img/d2febf09308fc03658e573ee4e6f2c21_59.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_61.png)

## 使用信号指标进行增强分析

![](img/d2febf09308fc03658e573ee4e6f2c21_63.png)

单纯看图寻找交叉点可能不够精确。我们可以创建一个明确的信号指标，让程序自动标记出交叉发生的区域。

以下是创建和可视化信号指标的步骤：

![](img/d2febf09308fc03658e573ee4e6f2c21_65.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_67.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_69.png)

1.  **生成信号列**：我们使用 `np.where` 函数来比较短期均线（M1）和长期均线（M2）。当 M1 > M2 时，标记为 1（看多信号）；当 M1 < M2 时，标记为 -1（看空信号）。
    ```python
    import numpy as np
    df[‘position’] = np.where(df[‘M1’] > df[‘M2’], 1, -1)
    ```

2.  **双坐标轴可视化**：由于股价和信号值（1或-1）的量纲差异巨大，我们使用双Y轴来绘制，使信号清晰可见。
    ```python
    fig, ax1 = plt.subplots(figsize=(12, 6))

    # 第一个Y轴：绘制股价和均线
    ax1.plot(df[‘AAPL’], label=‘Price’, color=‘blue’, alpha=0.3)
    ax1.plot(df[‘M1’], label=‘10-day MA’, color=‘green’)
    ax1.plot(df[‘M2’], label=‘30-day MA’, color=‘red’)
    ax1.set_ylabel(‘Price’)
    ax1.legend(loc=‘upper left’)

    # 第二个Y轴：绘制信号线
    ax2 = ax1.twinx()
    ax2.plot(df[‘position’], label=‘Signal’, color=‘purple’, linestyle=‘--’)
    ax2.set_ylabel(‘Signal (1/-1)’)
    ax2.legend(loc=‘upper right’)

    plt.show()
    ```

![](img/d2febf09308fc03658e573ee4e6f2c21_71.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_73.png)

在最终的图表中，紫色的虚线（信号线）在 **1** 和 **-1** 之间切换。**从 -1 变为 1 的转折点对应黄金交叉（买入点）**，而 **从 1 变为 -1 的转折点对应死亡交叉（卖出点）**。这使得交易信号的识别变得一目了然。

![](img/d2febf09308fc03658e573ee4e6f2c21_75.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_77.png)

## 总结

![](img/d2febf09308fc03658e573ee4e6f2c21_79.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_81.png)

![](img/d2febf09308fc03658e573ee4e6f2c21_83.png)

本节课中我们一起学习了如何利用移动平均线进行简单的量化分析。我们首先计算了股票的短期和长期移动平均线，然后通过可视化图表观察了“黄金交叉”和“死亡交叉”这两种重要信号。最后，我们通过创建信号指标并利用双坐标轴绘图，将抽象的交叉概念转化为了清晰、可程序化识别的图表信号。这是构建自动化交易策略非常基础且关键的一步。