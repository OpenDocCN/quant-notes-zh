# Python金融分析与量化交易实战：P6：05-5-短均与长均计算实例 📈

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_1.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_3.png)

在本节课中，我们将学习如何通过计算股票的短期和长期移动平均线，来构建一个简单的分析策略。我们将识别“黄金交叉”和“死亡交叉”这两个关键信号，并通过代码和图表直观地展示它们。

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_5.png)

上一节我们介绍了如何获取和处理金融数据，本节中我们来看看如何利用这些数据计算技术指标并进行可视化分析。

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_7.png)

## 计算短期与长期移动平均线

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_9.png)

要分析黄金交叉和死亡交叉，我们需要两个核心指标：短期移动平均线和长期移动平均线。交叉点就是这两个均线在图表上相交的位置。

以下是计算步骤：

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_11.png)

1.  **选择股票数据**：我们将使用苹果公司的股票数据作为分析对象。
2.  **定义计算窗口**：
    *   短期移动平均线通常使用较短的时间窗口，例如5天（代表一周的交易日）。
    *   长期移动平均线则使用较长的时间窗口，例如20天或30天。
    *   由于我们的数据集时间跨度较长（2010年至2018年），为了在图表上更清晰地观察交叉点，我们将适当放大窗口。这里我们使用10天作为短期窗口，30天作为长期窗口。
3.  **执行计算**：使用Pandas的滚动窗口函数 `.rolling()` 配合 `.mean()` 方法来计算指定窗口内的平均值。

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_13.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_15.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_17.png)

以下是计算移动平均线的核心代码：

```python
# 假设 df 是包含苹果股价的DataFrame，列名为 ‘AAPL‘
# 计算短期移动平均线（10天窗口）
short_window = 10
df[‘M1‘] = df[‘AAPL‘].rolling(window=short_window).mean()

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_19.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_21.png)

# 计算长期移动平均线（30天窗口）
long_window = 30
df[‘M2‘] = df[‘AAPL‘].rolling(window=long_window).mean()
```

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_23.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_25.png)

## 可视化移动平均线与股价

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_27.png)

计算完成后，我们将股价与两条移动平均线绘制在同一张图上进行观察。

```python
# 绘制股价、短期均线和长期均线
df[[‘AAPL‘, ‘M1‘, ‘M2‘]].plot(figsize=(12,6))
```

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_29.png)

在生成的图表中：
*   **蓝色线** 代表苹果公司的实际股价。
*   **绿色线** 代表10日短期移动平均线。
*   **红色线** 代表30日长期移动平均线。

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_31.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_33.png)

通过观察图表，我们可以寻找交叉点：
*   **死亡交叉**：当短期均线从上方向下穿越长期均线时，通常被视为下跌信号，提示可能不适合买入或应考虑卖出。
*   **黄金交叉**：当短期均线从下方向上穿越长期均线时，通常被视为上涨信号，提示可能是一个买入机会。

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_35.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_37.png)

## 量化交叉信号

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_39.png)

为了让分析更精确，我们可以创建一个新的指标来量化“短期均线是否高于长期均线”这一状态。

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_41.png)

以下是创建信号指标的方法：

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_43.png)

```python
import numpy as np

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_45.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_47.png)

# 创建一个名为‘position‘的列
# 当短期均线M1大于长期均线M2时，值为1；否则为-1
df[‘position‘] = np.where(df[‘M1‘] > df[‘M2‘], 1, -1)
```

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_49.png)

这个 `position` 列清晰地标明了每个时间点上，短期趋势相对于长期趋势是看涨（1）还是看跌（-1）。数值从1变为-1的点对应**死亡交叉**，从-1变为1的点对应**黄金交叉**。

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_51.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_53.png)

为了更清晰地展示这个信号，我们可以将其绘制在次坐标轴上：

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_55.png)

```python
import matplotlib.pyplot as plt

fig, ax1 = plt.subplots(figsize=(12,6))

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_57.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_59.png)

# 主坐标轴（ax1）绘制股价和均线
ax1.plot(df[‘AAPL‘], label=‘AAPL Price‘, alpha=0.7)
ax1.plot(df[‘M1‘], label=‘10-day MA‘, alpha=0.7)
ax1.plot(df[‘M2‘], label=‘30-day MA‘, alpha=0.7)
ax1.set_ylabel(‘Price‘)
ax1.legend(loc=‘upper left‘)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_61.png)

# 次坐标轴（ax2）绘制信号线
ax2 = ax1.twinx()
ax2.plot(df[‘position‘], label=‘Signal (1/-1)‘, color=‘purple‘, linestyle=‘--‘)
ax2.set_ylabel(‘Signal‘)
ax2.set_ylim([-1.5, 1.5])
ax2.legend(loc=‘upper right‘)

plt.title(‘AAPL Stock Price with Moving Averages and Crossover Signals‘)
plt.show()
```

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_63.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_65.png)

在这张双坐标轴图表中，紫色虚线在+1和-1之间切换，其转折点明确指出了黄金交叉和死亡交叉发生的位置，使得交易信号一目了然。

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_67.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_69.png)

![](img/7ccd3f7b81228c1f58ce3f6788cb350e_71.png)

本节课中我们一起学习了如何计算股票的短期和长期移动平均线，并通过可视化方法识别“黄金交叉”和“死亡交叉”。我们还通过创建信号指标，将抽象的交叉概念转化为具体的、可量化的数据，为后续构建更复杂的量化交易策略打下了基础。在Pandas中，利用 `.rolling().mean()` 等工具可以轻松实现各种窗口计算，并结合Matplotlib进行灵活的可视化展示。