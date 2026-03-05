# Python金融分析与量化交易实战：P10：02-2-买点与卖点可视化分析 📈

![](img/ed15816169ef7845382e25a17e4907e4_0.png)

![](img/ed15816169ef7845382e25a17e4907e4_2.png)

在本节课中，我们将学习如何利用移动平均线（MA）策略进行买点与卖点的可视化分析。我们将以苹果公司（AAPL）的股价数据为例，通过计算短期和长期移动平均线，识别“黄金交叉”和“死亡交叉”，并最终计算基于此策略的模拟收益。

![](img/ed15816169ef7845382e25a17e4907e4_4.png)

## 数据准备与预处理

![](img/ed15816169ef7845382e25a17e4907e4_6.png)

![](img/ed15816169ef7845382e25a17e4907e4_8.png)

首先，我们需要导入必要的工具包并加载数据。

![](img/ed15816169ef7845382e25a17e4907e4_10.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/ed15816169ef7845382e25a17e4907e4_12.png)

以下是数据加载与预处理的步骤：

![](img/ed15816169ef7845382e25a17e4907e4_14.png)

1.  **读取数据**：从CSV文件中读取股票数据，并将第一列（时间）设置为索引。
2.  **解析时间**：确保时间列被正确解析为日期时间格式。
3.  **选择股票**：从数据集中选取苹果公司（AAPL）的股价数据。
4.  **处理缺失值**：删除数据中可能存在的缺失值，以确保后续计算的准确性。

![](img/ed15816169ef7845382e25a17e4907e4_16.png)

![](img/ed15816169ef7845382e25a17e4907e4_18.png)

```python
# 读取数据，指定第一列为索引（时间）
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)

![](img/ed15816169ef7845382e25a17e4907e4_20.png)

# 选择苹果公司（AAPL）的股价列
apple_data = data[['AAPL']]

![](img/ed15816169ef7845382e25a17e4907e4_22.png)

![](img/ed15816169ef7845382e25a17e4907e4_24.png)

# 删除缺失值
apple_data = apple_data.dropna()

![](img/ed15816169ef7845382e25a17e4907e4_26.png)

# 查看数据前几行
print(apple_data.head())
```

![](img/ed15816169ef7845382e25a17e4907e4_28.png)

完成数据预处理后，我们得到了一个干净、只包含苹果公司股价的`DataFrame`。

![](img/ed15816169ef7845382e25a17e4907e4_30.png)

## 计算移动平均线

![](img/ed15816169ef7845382e25a17e4907e4_32.png)

上一节我们准备好了数据，本节中我们来看看如何计算关键的移动平均线指标。

![](img/ed15816169ef7845382e25a17e4907e4_34.png)

移动平均线是技术分析中常用的工具，用于平滑股价波动并识别趋势。我们主要关注两条线：
*   **短期移动平均线（SMA_short）**：反映近期价格趋势。
*   **长期移动平均线（SMA_long）**：反映长期价格趋势。

以下是计算步骤：

1.  **定义窗口期**：设定短期和长期移动平均线的计算天数。由于我们的数据跨度较长（约7-8年），为了在图表中更清晰地观察交叉点，我们将短期窗口设为42天，长期窗口设为252天。
2.  **计算移动平均**：使用Pandas的`.rolling()`和`.mean()`方法分别计算两条移动平均线，并将结果作为新列添加到数据中。

![](img/ed15816169ef7845382e25a17e4907e4_36.png)

```python
# 定义短期和长期窗口大小
short_window = 42
long_window = 252

![](img/ed15816169ef7845382e25a17e4907e4_38.png)

# 计算短期移动平均线
apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()

![](img/ed15816169ef7845382e25a17e4907e4_40.png)

# 计算长期移动平均线
apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()

![](img/ed15816169ef7845382e25a17e4907e4_42.png)

![](img/ed15816169ef7845382e25a17e4907e4_44.png)

# 查看数据尾部，确认计算成功
print(apple_data.tail())
```

![](img/ed15816169ef7845382e25a17e4907e4_46.png)

计算完成后，数据中新增了`SMA_short`和`SMA_long`两列。前`long_window-1`个数据点由于窗口不足，其移动平均值为`NaN`。

![](img/ed15816169ef7845382e25a17e4907e4_48.png)

![](img/ed15816169ef7845382e25a17e4907e4_50.png)

## 可视化移动平均线与交叉点

![](img/ed15816169ef7845382e25a17e4907e4_52.png)

![](img/ed15816169ef7845382e25a17e4907e4_54.png)

现在我们已经计算出了移动平均线，接下来通过可视化来直观地观察它们的走势以及关键的交叉点。

我们使用Matplotlib绘制股价以及两条移动平均线。

![](img/ed15816169ef7845382e25a17e4907e4_56.png)

![](img/ed15816169ef7845382e25a17e4907e4_58.png)

```python
# 绘制股价与移动平均线
apple_data[['AAPL', 'SMA_short', 'SMA_long']].plot(figsize=(15, 8))
plt.title('Apple Stock Price with Moving Averages')
plt.ylabel('Price')
plt.show()
```

在生成的图表中，我们可以观察到：
*   **原始股价（通常为蓝色或红色线）**：波动较大。
*   **短期移动平均线（如红色线）**：更贴近股价，波动相对平缓。
*   **长期移动平均线（如绿色线）**：最为平滑，代表长期趋势。

**关键交叉点分析**：
*   **黄金交叉（Golden Cross）**：当短期均线从下向上穿越长期均线时，通常被视为**买入信号**，预示着上涨趋势可能开始。
*   **死亡交叉（Death Cross）**：当短期均线从上向下穿越长期均线时，通常被视为**卖出信号**，预示着下跌趋势可能开始。

![](img/ed15816169ef7845382e25a17e4907e4_60.png)

在图表中，我们可以清晰地找到这些交叉点，它们构成了我们交易策略的基础。

![](img/ed15816169ef7845382e25a17e4907e4_62.png)

![](img/ed15816169ef7845382e25a17e4907e4_64.png)

## 标记趋势与生成交易信号

![](img/ed15816169ef7845382e25a17e4907e4_66.png)

为了量化交叉点并自动生成交易信号，我们需要创建一个标志位来记录短期均线与长期均线的位置关系。

![](img/ed15816169ef7845382e25a17e4907e4_68.png)

![](img/ed15816169ef7845382e25a17e4907e4_70.png)

以下是具体操作：

1.  **清理数据**：首先移除计算移动平均线时产生的`NaN`值。
2.  **创建标志位**：使用`np.where()`函数生成一个新列`Position`。当短期均线大于长期均线时，标记为`1`，否则标记为`0`。这个标志位直观地显示了当前市场处于“看涨”（短期强势）还是“看跌”（短期弱势）状态。

```python
# 移除NaN值（即前251行）
apple_data_clean = apple_data.dropna()

![](img/ed15816169ef7845382e25a17e4907e4_72.png)

# 创建标志位：短期均线 > 长期均线 则为1，否则为0
import numpy as np
apple_data_clean['Position'] = np.where(apple_data_clean['SMA_short'] > apple_data_clean['SMA_long'], 1, 0)

![](img/ed15816169ef7845382e25a17e4907e4_74.png)

# 查看结果
print(apple_data_clean[['AAPL', 'SMA_short', 'SMA_long', 'Position']].head())
```

![](img/ed15816169ef7845382e25a17e4907e4_76.png)

我们也可以将这个标志位绘制在次坐标轴上，以便与股价对比观察。

```python
# 绘制股价与趋势标志位
fig, ax1 = plt.subplots(figsize=(15, 8))

![](img/ed15816169ef7845382e25a17e4907e4_78.png)

ax1.plot(apple_data_clean.index, apple_data_clean['AAPL'], label='Price', alpha=0.5)
ax1.set_ylabel('Price', color='black')
ax1.tick_params(axis='y', labelcolor='black')

![](img/ed15816169ef7845382e25a17e4907e4_80.png)

# 创建次坐标轴用于显示标志位
ax2 = ax1.twinx()
ax2.plot(apple_data_clean.index, apple_data_clean['Position'], label='Position (1=Buy, 0=Sell)', color='gray', linestyle='--')
ax2.set_ylabel('Position Signal', color='gray')
ax2.tick_params(axis='y', labelcolor='gray')

![](img/ed15816169ef7845382e25a17e4907e4_82.png)

plt.title('Apple Stock Price with Trading Position Signal')
fig.legend(loc='upper left')
plt.show()
```

## 策略逻辑与收益模拟

最后，我们来探讨基于双均线交叉策略的核心逻辑，并理解其如何理论上实现持续盈利。

**策略核心思想**：
*   当`Position`标志位从`0`变为`1`时（即出现**黄金交叉**），执行**买入**操作。
*   当`Position`标志位从`1`变为`0`时（即出现**死亡交叉**），执行**卖出**操作。

**与传统持有的对比**：
*   **“买入并持有”（Buy & Hold）策略**：在期初投入一笔资金，期间不做任何操作，直到期末卖出。其收益完全取决于股价的最终涨跌。
*   **双均线交叉策略**：通过在高位（死亡交叉）卖出、在低位（黄金交叉）买入，旨在**捕获波段收益，规避大幅下跌**。即使股价长期横盘或下跌，该策略也试图通过多次小规模的高卖低买来累积利润。

![](img/ed15816169ef7845382e25a17e4907e4_84.png)

> **举例说明**：假设初始资金为100元。
> *   在死亡交叉点A，股价为100元，我们卖出全部股票，持有100元现金。
> *   随后股价下跌，在黄金交叉点B，股价为80元，我们用100元现金买入股票，获得1.25股。
> *   此时，我们的资产价值是 `1.25股 * 80元/股 = 100元`，看似持平。但**如果股价从80元涨回100元**，我们的资产将变为 `1.25股 * 100元/股 = 125元`，实现了25%的收益。而单纯的“买入持有”者，资产则从100元变为100元，没有收益。

![](img/ed15816169ef7845382e25a17e4907e4_86.png)

本节课中我们一起学习了如何利用Python进行金融数据的移动平均线分析。我们从数据预处理开始，逐步计算了短期和长期移动平均线，并通过可视化清晰地识别了“黄金交叉”和“死亡交叉”这两个关键的交易信号点。接着，我们创建了趋势标志位来量化这些信号，并阐述了基于此的双均线交易策略如何通过“高卖低买”的模式，在理论上实现优于单纯“买入并持有”策略的收益潜力。这为后续构建完整的量化交易回测系统奠定了坚实的基础。