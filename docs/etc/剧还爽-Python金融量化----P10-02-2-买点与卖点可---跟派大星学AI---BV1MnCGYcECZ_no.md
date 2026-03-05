# 量化交易全教程：P10：02-2-买点与卖点可视化分析 📈

![](img/a2270b722d1fb1f0b8a3283e99832a5e_0.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_2.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_4.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_6.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，特别是通过计算短期和长期移动平均线来识别股票的潜在买点和卖点。我们将从数据预处理开始，逐步计算技术指标，并最终通过图表直观地展示交易信号。

![](img/a2270b722d1fb1f0b8a3283e99832a5e_8.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_10.png)

## 数据准备与预处理

![](img/a2270b722d1fb1f0b8a3283e99832a5e_12.png)

首先，我们需要导入必要的工具包并加载数据。以下是实现这一步骤的代码：

![](img/a2270b722d1fb1f0b8a3283e99832a5e_14.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_16.png)

```python
import pandas as pd
import matplotlib.pyplot as plt

![](img/a2270b722d1fb1f0b8a3283e99832a5e_18.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_20.png)

# 读取数据，并将第一列（时间）设置为索引
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/a2270b722d1fb1f0b8a3283e99832a5e_22.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_24.png)

我们选择苹果公司的股票数据作为分析示例。为了确保后续计算的准确性，第一步是处理数据中的缺失值。

![](img/a2270b722d1fb1f0b8a3283e99832a5e_26.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_28.png)

```python
# 选择苹果公司的股票数据列
apple_data = data[['AAPL']].copy()
# 删除缺失值
apple_data.dropna(inplace=True)
print(apple_data.head())
```

![](img/a2270b722d1fb1f0b8a3283e99832a5e_30.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_32.png)

## 计算移动平均线

![](img/a2270b722d1fb1f0b8a3283e99832a5e_34.png)

上一节我们完成了数据清洗。本节中，我们来看看如何计算技术指标——移动平均线。移动平均线是分析股票趋势的常用工具，通过计算特定时间窗口内价格的平均值来平滑价格波动。

以下是计算短期和长期移动平均线的步骤：

![](img/a2270b722d1fb1f0b8a3283e99832a5e_36.png)

1.  **定义时间窗口**：首先，我们需要设定短期和长期移动平均线的计算周期。由于我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察交叉点，我们将短期窗口设为42天，长期窗口设为252天。
    ```python
    short_window = 42
    long_window = 252
    ```
2.  **计算移动平均值**：使用Pandas的`rolling`和`mean`方法，分别计算指定窗口内的收盘价平均值。
    ```python
    # 计算短期移动平均线 (SMA)
    apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()
    # 计算长期移动平均线 (SMA)
    apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()
    print(apple_data.tail())
    ```

![](img/a2270b722d1fb1f0b8a3283e99832a5e_38.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_40.png)

计算完成后，数据的前几行由于窗口不足会显示为缺失值（NaN），这是正常现象。

![](img/a2270b722d1fb1f0b8a3283e99832a5e_42.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_44.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_46.png)

## 可视化分析交叉点

![](img/a2270b722d1fb1f0b8a3283e99832a5e_48.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_50.png)

现在我们已经有了原始股价和两条移动平均线数据。接下来，我们将它们绘制在图表上，以直观地观察“黄金交叉”和“死亡交叉”。

![](img/a2270b722d1fb1f0b8a3283e99832a5e_52.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_54.png)

```python
# 绘制股价与移动平均线
apple_data.plot(figsize=(10, 6))
plt.title('Apple Stock Price with Moving Averages')
plt.ylabel('Price')
plt.show()
```

![](img/a2270b722d1fb1f0b8a3283e99832a5e_56.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_58.png)

在生成的图表中，我们可以观察到：
*   **短期均线（SMA_short）** 与 **长期均线（SMA_long）** 会发生交叉。
*   **黄金交叉**：当短期均线从下向上穿越长期均线时，通常被视为**买入信号**。
*   **死亡交叉**：当短期均线从上向下穿越长期均线时，通常被视为**卖出信号**。

通过图表，我们可以清晰地定位这些潜在的交易时机。

![](img/a2270b722d1fb1f0b8a3283e99832a5e_60.png)

## 标记均线位置关系

![](img/a2270b722d1fb1f0b8a3283e99832a5e_62.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_64.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_66.png)

为了量化这种位置关系，我们可以添加一个标志位来明确指示短期均线是否高于长期均线。在添加标志位之前，需要先剔除计算移动平均线时产生的缺失值。

![](img/a2270b722d1fb1f0b8a3283e99832a5e_68.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_70.png)

以下是具体操作：

![](img/a2270b722d1fb1f0b8a3283e99832a5e_72.png)

1.  **删除缺失值**：确保数据集中所有行都具有有效的移动平均值。
    ```python
    apple_data.dropna(inplace=True)
    ```
2.  **创建标志位**：使用`np.where`函数生成一个新列。当短期均线大于长期均线时，标记为1（代表持有或买入信号）；否则标记为0（代表卖出或观望信号）。
    ```python
    import numpy as np
    apple_data['Position'] = np.where(apple_data['SMA_short'] > apple_data['SMA_long'], 1, 0)
    print(apple_data.head())
    ```

![](img/a2270b722d1fb1f0b8a3283e99832a5e_74.png)

我们也可以将这个标志位绘制在图表上作为参考（使用右侧次坐标轴）。

![](img/a2270b722d1fb1f0b8a3283e99832a5e_76.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_78.png)

```python
# 绘制股价、均线及交易信号位置
fig, ax1 = plt.subplots(figsize=(10,6))
ax1.plot(apple_data['AAPL'], label='Price', color='blue', alpha=0.5)
ax1.plot(apple_data['SMA_short'], label=f'SMA {short_window}', color='red')
ax1.plot(apple_data['SMA_long'], label=f'SMA {long_window}', color='green')
ax1.set_ylabel('Price')

# 在次坐标轴上绘制买卖信号标志位
ax2 = ax1.twinx()
ax2.plot(apple_data['Position'], label='Position (Buy/Sell Signal)', color='orange', linestyle='--', alpha=0.7)
ax2.set_ylabel('Position Signal (0 or 1)')
ax2.set_ylim(-0.5, 1.5)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_80.png)

fig.legend(loc='upper left')
plt.title('Stock Price, Moving Averages and Trading Signals')
plt.show()
```

![](img/a2270b722d1fb1f0b8a3283e99832a5e_82.png)

![](img/a2270b722d1fb1f0b8a3283e99832a5e_84.png)

## 策略思路简述

最后，我们来探讨一下基于此分析的基本策略思路。如果不采取任何策略，单纯地买入并持有，最终的收益完全取决于股价的净变动。

然而，如果我们利用移动平均线交叉策略：
*   在出现 **“黄金交叉”** （标志位由0变为1）时买入或持有。
*   在出现 **“死亡交叉”** （标志位由1变为0）时卖出或空仓。

![](img/a2270b722d1fb1f0b8a3283e99832a5e_86.png)

理论上，这种策略可以帮助我们避开部分下跌行情，并在上涨趋势中保持持仓，从而有可能获得超越简单买入持有策略的回报。这体现了技术分析在量化交易中用于生成系统性交易信号的核心价值。

![](img/a2270b722d1fb1f0b8a3283e99832a5e_88.png)

本节课中我们一起学习了如何获取并预处理股票数据、计算短期与长期移动平均线、通过可视化图表识别买卖点，以及如何创建交易信号标志位。这些是构建简单趋势跟踪策略的基础步骤。