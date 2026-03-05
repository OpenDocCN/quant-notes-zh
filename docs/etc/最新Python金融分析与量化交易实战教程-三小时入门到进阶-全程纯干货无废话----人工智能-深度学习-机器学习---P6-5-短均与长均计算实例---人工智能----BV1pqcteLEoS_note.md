# Python金融分析与量化交易实战：P6：5-短均与长均计算实例 📈

![](img/d77b272c9f04d6f34b0b67736916183e_1.png)

在本节课中，我们将学习一个经典的股票分析策略：通过计算短期和长期移动平均线，来识别“黄金交叉”与“死亡交叉”信号，从而辅助判断买入和卖出的时机。

![](img/d77b272c9f04d6f34b0b67736916183e_3.png)

![](img/d77b272c9f04d6f34b0b67736916183e_5.png)

上一节我们介绍了数据处理的基础，本节中我们来看看如何应用这些知识进行技术指标的计算与分析。

![](img/d77b272c9f04d6f34b0b67736916183e_7.png)

## 策略核心：移动平均线交叉

![](img/d77b272c9f04d6f34b0b67736916183e_9.png)

该策略的核心在于计算两个指标：短期移动平均线（Short-term Moving Average）和长期移动平均线（Long-term Moving Average）。通过观察这两条线的交叉点，我们可以得到交易信号：
*   **黄金交叉**：短期均线从下向上穿越长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿越长期均线，通常被视为**卖出信号**。

## 第一步：计算短期与长期移动平均线

首先，我们需要为选定的股票数据计算移动平均线。我们将以苹果公司（AAPL）的股价数据为例。

![](img/d77b272c9f04d6f34b0b67736916183e_11.png)

![](img/d77b272c9f04d6f34b0b67736916183e_13.png)

以下是计算步骤：

![](img/d77b272c9f04d6f34b0b67736916183e_15.png)

![](img/d77b272c9f04d6f34b0b67736916183e_17.png)

1.  **选择股票数据**：我们使用之前获取的苹果公司股价数据。
2.  **定义窗口大小**：通常，短期窗口可设为5天（一周交易日），长期窗口可设为20或30天（约一个月交易日）。由于我们的数据时间跨度较长，为了在图表中更清晰地展示交叉现象，我们将短期窗口设为10天，长期窗口设为30天。
3.  **执行计算**：使用Pandas的`.rolling()`和`.mean()`方法计算移动平均值。

![](img/d77b272c9f04d6f34b0b67736916183e_19.png)

```python
# 假设 df 是包含苹果股价的DataFrame，其中‘AAPL’是股价列
# 计算短期移动平均线（10天窗口）
short_window = 10
df['M1_Short_MA'] = df['AAPL'].rolling(window=short_window).mean()

![](img/d77b272c9f04d6f34b0b67736916183e_21.png)

# 计算长期移动平均线（30天窗口）
long_window = 30
df['M2_Long_MA'] = df['AAPL'].rolling(window=long_window).mean()
```

![](img/d77b272c9f04d6f34b0b67736916183e_23.png)

![](img/d77b272c9f04d6f34b0b67736916183e_25.png)

![](img/d77b272c9f04d6f34b0b67736916183e_27.png)

## 第二步：可视化股价与移动平均线

计算完成后，我们可以将原始股价、短期均线和长期均线绘制在同一张图上进行观察。

```python
# 选择要绘制的列
plot_columns = ['AAPL', 'M1_Short_MA', 'M2_Long_MA']
df[plot_columns].plot(figsize=(12, 6))
```

![](img/d77b272c9f04d6f34b0b67736916183e_29.png)

![](img/d77b272c9f04d6f34b0b67736916183e_31.png)

为了更清晰地观察交叉细节，我们可以截取一段时间的数据（例如一年）进行绘图。在生成的图表中：
*   **蓝色线**代表苹果公司的实际股价。
*   **绿色线**代表10日短期移动平均线（M1）。
*   **红色线**代表30日长期移动平均线（M2）。

![](img/d77b272c9f04d6f34b0b67736916183e_33.png)

观察图表，我们可以手动识别交叉点：
*   当**绿色线（短期）向下穿越红色线（长期）**时，形成**死亡交叉**，暗示下跌趋势，应考虑卖出或观望。
*   当**绿色线（短期）向上穿越红色线（长期）**时，形成**黄金交叉**，暗示上涨趋势，可考虑买入。

![](img/d77b272c9f04d6f34b0b67736916183e_35.png)

![](img/d77b272c9f04d6f34b0b67736916183e_37.png)

## 第三步：量化交叉信号

![](img/d77b272c9f04d6f34b0b67736916183e_39.png)

手动识别交叉点不够精确。我们可以使用NumPy来量化这一信号，生成一个明确的交易位置指标。

以下是具体方法：
我们创建一个新列`Position`，当短期均线大于长期均线时，标记为`1`；当短期均线小于长期均线时，标记为`-1`。这个`1`和`-1`的切换点，就是交叉发生的位置。

![](img/d77b272c9f04d6f34b0b67736916183e_41.png)

![](img/d77b272c9f04d6f34b0b67736916183e_43.png)

```python
import numpy as np

![](img/d77b272c9f04d6f34b0b67736916183e_45.png)

![](img/d77b272c9f04d6f34b0b67736916183e_47.png)

# 生成交易信号位置列
# 当短期均线 > 长期均线时，为1；否则为-1
df['Position'] = np.where(df['M1_Short_MA'] > df['M2_Long_MA'], 1, -1)
```

![](img/d77b272c9f04d6f34b0b67736916183e_49.png)

为了在图表中同时展示股价走势和清晰的信号变化，我们使用双Y轴进行绘图。

![](img/d77b272c9f04d6f34b0b67736916183e_51.png)

![](img/d77b272c9f04d6f34b0b67736916183e_53.png)

```python
import matplotlib.pyplot as plt

![](img/d77b272c9f04d6f34b0b67736916183e_55.png)

fig, ax1 = plt.subplots(figsize=(14, 7))

# 第一个Y轴：绘制股价和移动平均线
color = ‘tab:blue’
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’, color=color)
ax1.plot(df.index, df[‘AAPL’], color=color, alpha=0.3, label=‘AAPL Price’)
ax1.plot(df.index, df[‘M1_Short_MA’], color=‘tab:green’, label=‘10-Day MA’)
ax1.plot(df.index, df[‘M2_Long_MA’], color=‘tab:red’, label=‘30-Day MA’)
ax1.tick_params(axis=‘y’, labelcolor=color)
ax1.legend(loc=‘upper left’)

![](img/d77b272c9f04d6f34b0b67736916183e_57.png)

# 第二个Y轴：绘制交易位置信号
ax2 = ax1.twinx()
color = ‘tab:purple’
ax2.set_ylabel(‘Position Signal’, color=color)
ax2.plot(df.index, df[‘Position’], color=color, linestyle=‘—’, label=‘Position (1/-1)’)
ax2.tick_params(axis=‘y’, labelcolor=color)
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc=‘upper right’)

![](img/d77b272c9f04d6f34b0b67736916183e_59.png)

![](img/d77b272c9f04d6f34b0b67736916183e_61.png)

fig.tight_layout()
plt.title(‘AAPL Price with Moving Averages and Crossover Signals’)
plt.show()
```

在这张图中：
*   左侧Y轴对应股价和均线。
*   右侧Y轴对应位置信号（`1` 或 `-1`）。
*   **信号线从 -1 变为 1 的点**，对应**黄金交叉**（买入信号）。
*   **信号线从 1 变为 -1 的点**，对应**死亡交叉**（卖出信号）。

![](img/d77b272c9f04d6f34b0b67736916183e_63.png)

## 总结

![](img/d77b272c9f04d6f34b0b67736916183e_65.png)

![](img/d77b272c9f04d6f34b0b67736916183e_67.png)

本节课中我们一起学习了如何实现一个基础的移动平均线交叉策略：
1.  **计算指标**：使用`.rolling().mean()`方法计算短期和长期移动平均线。
2.  **可视化分析**：通过绘图直观观察股价、短期均线与长期均线的走势关系，识别黄金交叉与死亡交叉。
3.  **信号量化**：利用`np.where`函数将交叉逻辑转化为明确的数值信号（1和-1），并通过双Y轴图表进行清晰展示。

![](img/d77b272c9f04d6f34b0b67736916183e_69.png)

![](img/d77b272c9f04d6f34b0b67736916183e_71.png)

这个实例展示了如何将Pandas的数据处理能力与简单的金融逻辑相结合，为量化交易分析打下基础。在实际应用中，你可以调整窗口参数、结合其他指标，并在此基础上进行回测，以构建更稳健的交易策略。