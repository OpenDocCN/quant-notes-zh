# 人工智能数据挖掘实战：P9：短均与长均计算实例 📈

在本节课中，我们将学习一个基础的股票分析策略。我们将通过计算短期和长期移动平均线，并识别它们之间的“黄金交叉”与“死亡交叉”，来辅助判断股票的买入和卖出时机。

![](img/3501565464dda7d370fec67780ee99b6_1.png)

![](img/3501565464dda7d370fec67780ee99b6_2.png)

![](img/3501565464dda7d370fec67780ee99b6_3.png)

上一节我们介绍了数据处理的基础，本节中我们来看看如何应用这些数据计算技术指标。

## 策略核心概念

![](img/3501565464dda7d370fec67780ee99b6_5.png)

![](img/3501565464dda7d370fec67780ee99b6_6.png)

该策略的核心在于比较两条移动平均线：
*   **短期移动平均线 (M1)**：通常代表近期价格趋势。
*   **长期移动平均线 (M2)**：通常代表长期价格趋势。

当短期均线从下方上穿长期均线时，形成 **`黄金交叉`**，通常被视为买入信号。
当短期均线从上方下穿长期均线时，形成 **`死亡交叉`**，通常被视为卖出信号。

## 计算移动平均线

![](img/3501565464dda7d370fec67780ee99b6_8.png)

![](img/3501565464dda7d370fec67780ee99b6_9.png)

首先，我们需要从股票数据中计算出短期和长期的移动平均值。我们以苹果公司（AAPL）的股价数据为例。

![](img/3501565464dda7d370fec67780ee99b6_11.png)

![](img/3501565464dda7d370fec67780ee99b6_13.png)

![](img/3501565464dda7d370fec67780ee99b6_14.png)

以下是计算步骤：

1.  **选择数据与窗口**：我们选取苹果股票的收盘价数据。对于短期均线，我们使用一个较小的窗口（例如10天）；对于长期均线，我们使用一个较大的窗口（例如30天）。由于示例数据时间跨度较长（2010-2018年），我们适当增大了窗口以便观察。
2.  **执行计算**：使用Pandas的`.rolling()`方法结合`.mean()`函数来计算指定窗口内的平均值。

![](img/3501565464dda7d370fec67780ee99b6_16.png)

相关代码如下：

```python
# 计算短期移动平均线 (M1)，窗口为10天
data[‘M1’] = data[‘AAPL’].rolling(window=10).mean()

![](img/3501565464dda7d370fec67780ee99b6_18.png)

![](img/3501565464dda7d370fec67780ee99b6_19.png)

![](img/3501565464dda7d370fec67780ee99b6_20.png)

![](img/3501565464dda7d370fec67780ee99b6_21.png)

# 计算长期移动平均线 (M2)，窗口为30天
data[‘M2’] = data[‘AAPL’].rolling(window=30).mean()
```

![](img/3501565464dda7d370fec67780ee99b6_22.png)

## 可视化均线与交叉点

计算完成后，我们可以将股价、短期均线和长期均线绘制在同一张图上进行观察。

以下是绘图代码：

![](img/3501565464dda7d370fec67780ee99b6_24.png)

```python
# 绘制股价与移动平均线
data[[‘AAPL’, ‘M1’, ‘M2’]].plot(figsize=(12, 6))
```

![](img/3501565464dda7d370fec67780ee99b6_25.png)

![](img/3501565464dda7d370fec67780ee99b6_26.png)

在生成的图表中，我们可以手动寻找短期均线（M1）与长期均线（M2）交叉的区域。交叉点附近可能就是潜在的交易信号点。

![](img/3501565464dda7d370fec67780ee99b6_28.png)

![](img/3501565464dda7d370fec67780ee99b6_29.png)

## 量化交叉信号

![](img/3501565464dda7d370fec67780ee99b6_30.png)

为了更精确、自动化地识别交叉信号，我们可以创建一个新的指标来量化短期均线与长期均线的关系。

我们使用`np.where`函数来创建这个信号指标：
*   当 `M1 > M2` 时，标记为 `1`（代表短期趋势强于长期趋势）。
*   当 `M1 <= M2` 时，标记为 `-1`（代表短期趋势弱于长期趋势）。

![](img/3501565464dda7d370fec67780ee99b6_32.png)

以下是实现代码：

![](img/3501565464dda7d370fec67780ee99b6_33.png)

![](img/3501565464dda7d370fec67780ee99b6_34.png)

```python
import numpy as np

![](img/3501565464dda7d370fec67780ee99b6_36.png)

![](img/3501565464dda7d370fec67780ee99b6_37.png)

# 创建信号列 ‘position’
data[‘position’] = np.where(data[‘M1’] > data[‘M2’], 1, -1)
```

![](img/3501565464dda7d370fec67780ee99b6_39.png)

![](img/3501565464dda7d370fec67780ee99b6_41.png)

![](img/3501565464dda7d370fec67780ee99b6_42.png)

## 综合可视化分析

![](img/3501565464dda7d370fec67780ee99b6_44.png)

最后，我们将所有信息整合在一张图中进行展示。由于股价和信号值（1或-1）的量纲差异巨大，我们使用双Y轴来清晰呈现。

以下是最终的绘图代码：

![](img/3501565464dda7d370fec67780ee99b6_46.png)

```python
import matplotlib.pyplot as plt

![](img/3501565464dda7d370fec67780ee99b6_48.png)

![](img/3501565464dda7d370fec67780ee99b6_49.png)

fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/3501565464dda7d370fec67780ee99b6_51.png)

# 在主Y轴（左侧）绘制股价和均线
ax1.plot(data[‘AAPL’], label=‘AAPL Price’, alpha=0.5)
ax1.plot(data[‘M1’], label=‘M1 (Short-term MA)’, linestyle=‘--’)
ax1.plot(data[‘M2’], label=‘M2 (Long-term MA)’, linestyle=‘--’)
ax1.set_ylabel(‘Price’)
ax1.legend(loc=‘upper left’)

# 在次Y轴（右侧）绘制信号线
ax2 = ax1.twinx()
ax2.plot(data[‘position’], label=‘Signal (1/-1)’, color=‘red’, linewidth=2)
ax2.set_ylabel(‘Signal’)
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc=‘upper right’)

plt.title(‘AAPL Stock Price with Moving Averages and Trading Signals’)
plt.show()
```

![](img/3501565464dda7d370fec67780ee99b6_53.png)

![](img/3501565464dda7d370fec67780ee99b6_54.png)

![](img/3501565464dda7d370fec67780ee99b6_55.png)

在这张综合图表中：
*   **左侧Y轴**：显示股价和两条移动平均线的实际数值。
*   **右侧Y轴**：显示信号线，其值在1和-1之间切换。
*   **信号线从-1变为1**：对应图表中短期均线上穿长期均线的点，即 **`黄金交叉`**（潜在买入点）。
*   **信号线从1变为-1**：对应图表中短期均线下穿长期均线的点，即 **`死亡交叉`**（潜在卖出点）。

![](img/3501565464dda7d370fec67780ee99b6_56.png)

![](img/3501565464dda7d370fec67780ee99b6_57.png)

![](img/3501565464dda7d370fec67780ee99b6_58.png)

本节课中我们一起学习了如何计算股票的短期和长期移动平均线，并通过可视化及创建信号指标的方法，清晰地识别出“黄金交叉”与“死亡交叉”。这是一个基础但非常重要的技术分析工具，可以帮助初学者理解趋势的潜在变化点。