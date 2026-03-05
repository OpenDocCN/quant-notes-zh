# Python金融量化与股票交易实战：P10：02-2-买点与卖点可视化分析 📈

![](img/04f194819b1ca088cd0727e50998488e_0.png)

![](img/04f194819b1ca088cd0727e50998488e_2.png)

在本节课中，我们将学习如何通过移动平均线（MA）策略来识别股票的潜在买点和卖点，并进行可视化分析。我们将使用Python的Pandas和Matplotlib库，从数据处理、指标计算到策略信号标记，一步步构建一个简单的双均线交易模型。

![](img/04f194819b1ca088cd0727e50998488e_4.png)

---

![](img/04f194819b1ca088cd0727e50998488e_6.png)

![](img/04f194819b1ca088cd0727e50998488e_8.png)

## 数据准备与预处理

![](img/04f194819b1ca088cd0727e50998488e_10.png)

首先，我们需要导入必要的工具包并加载数据。

![](img/04f194819b1ca088cd0727e50998488e_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/04f194819b1ca088cd0727e50998488e_14.png)

接下来，我们读取包含多只股票历史价格的数据文件。这里假设第一列是日期，我们将其设置为索引。

![](img/04f194819b1ca088cd0727e50998488e_16.png)

![](img/04f194819b1ca088cd0727e50998488e_18.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/04f194819b1ca088cd0727e50998488e_20.png)

数据加载后，我们选择苹果公司（AAPL）的股价数据作为本次分析的示例。

![](img/04f194819b1ca088cd0727e50998488e_22.png)

![](img/04f194819b1ca088cd0727e50998488e_24.png)

```python
# 选择苹果公司的股价列，这里假设列名为‘AAPL’
stock_data = data[['AAPL']].copy()
```

![](img/04f194819b1ca088cd0727e50998488e_26.png)

在开始分析前，一个重要的预处理步骤是处理缺失值。缺失值会影响移动平均线的计算。

```python
# 删除含有缺失值的行
stock_data.dropna(inplace=True)
print(stock_data.head())
```

![](img/04f194819b1ca088cd0727e50998488e_28.png)

![](img/04f194819b1ca088cd0727e50998488e_30.png)

---

![](img/04f194819b1ca088cd0727e50998488e_32.png)

## 计算短期与长期移动平均线

上一节我们完成了数据准备，本节中我们来看看如何计算关键的技术指标——移动平均线。

![](img/04f194819b1ca088cd0727e50998488e_34.png)

移动平均线能平滑价格波动，帮助我们识别趋势。我们通常使用两条不同周期的均线：短期均线（如30日）对价格变化更敏感；长期均线（如100日）则反映更长期的趋势。

以下是计算移动平均线的步骤：

![](img/04f194819b1ca088cd0727e50998488e_36.png)

1.  **定义周期**：首先，设定短期和长期移动平均线的计算窗口大小。
2.  **进行计算**：使用Pandas的`.rolling()`和`.mean()`方法计算指定窗口内的价格平均值。

```python
# 定义短期和长期均线的窗口大小
short_window = 30
long_window = 100

![](img/04f194819b1ca088cd0727e50998488e_38.png)

![](img/04f194819b1ca088cd0727e50998488e_40.png)

# 计算短期移动平均线 (SMA)
stock_data['SMA_short'] = stock_data['AAPL'].rolling(window=short_window).mean()

![](img/04f194819b1ca088cd0727e50998488e_42.png)

# 计算长期移动平均线 (SMA)
stock_data['SMA_long'] = stock_data['AAPL'].rolling(window=long_window).mean()

![](img/04f194819b1ca088cd0727e50998488e_44.png)

![](img/04f194819b1ca088cd0727e50998488e_46.png)

# 查看计算结果
print(stock_data.tail())
```

![](img/04f194819b1ca088cd0727e50998488e_48.png)

![](img/04f194819b1ca088cd0727e50998488e_50.png)

请注意，计算移动平均线后，数据的前 `long_window-1` 行会出现缺失值（NaN），因为最初的几天不足以构成一个完整的计算窗口。

![](img/04f194819b1ca088cd0727e50998488e_52.png)

---

![](img/04f194819b1ca088cd0727e50998488e_54.png)

## 可视化均线与交叉点

![](img/04f194819b1ca088cd0727e50998488e_56.png)

计算完指标后，我们可以通过图表直观地观察股价与两条均线的关系。

![](img/04f194819b1ca088cd0727e50998488e_58.png)

```python
# 绘制股价与移动平均线
plt.figure(figsize=(12, 6))
plt.plot(stock_data['AAPL'], label='AAPL Price', color='red', alpha=0.5)
plt.plot(stock_data['SMA_short'], label=f'{short_window}-Day SMA', color='blue')
plt.plot(stock_data['SMA_long'], label=f'{long_window}-Day SMA', color='green')
plt.title('AAPL Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

在生成的图表中，我们重点关注短期均线（蓝线）与长期均线（绿线）的交叉点：
*   **黄金交叉**：当短期均线从下方上穿长期均线时，通常被视为**买入信号**。
*   **死亡交叉**：当短期均线从上方下穿长期均线时，通常被视为**卖出信号**。

![](img/04f194819b1ca088cd0727e50998488e_60.png)

---

![](img/04f194819b1ca088cd0727e50998488e_62.png)

## 标记交易信号

![](img/04f194819b1ca088cd0727e50998488e_64.png)

![](img/04f194819b1ca088cd0727e50998488e_66.png)

为了量化这些交叉点，我们需要在数据中创建一个标志位来记录均线的位置关系。

![](img/04f194819b1ca088cd0727e50998488e_68.png)

![](img/04f194819b1ca088cd0727e50998488e_70.png)

首先，确保我们已经去除了因计算移动平均线而产生的缺失值。

```python
# 删除因计算移动平均线而产生的缺失值
stock_data.dropna(inplace=True)
```

![](img/04f194819b1ca088cd0727e50998488e_72.png)

接着，我们使用 `np.where` 函数创建一个新的列 `Position`。当短期均线高于长期均线时，标记为1；否则标记为0。这个标志位清晰地标出了理论上的持仓区间。

```python
import numpy as np

![](img/04f194819b1ca088cd0727e50998488e_74.png)

# 创建标志位：短期均线 > 长期均线时为1，否则为0
stock_data['Position'] = np.where(stock_data['SMA_short'] > stock_data['SMA_long'], 1, 0)
print(stock_data[['AAPL', 'SMA_short', 'SMA_long', 'Position']].head())
```

![](img/04f194819b1ca088cd0727e50998488e_76.png)

![](img/04f194819b1ca088cd0727e50998488e_78.png)

我们也可以将这个标志位添加到图表中，以便更直观地理解策略信号。

```python
# 创建双Y轴图表，分别显示价格和交易信号
fig, ax1 = plt.subplots(figsize=(12, 6))

ax1.plot(stock_data['AAPL'], label='Price', color='lightgray')
ax1.plot(stock_data['SMA_short'], label='Short MA', color='blue')
ax1.plot(stock_data['SMA_long'], label='Long MA', color='green')
ax1.set_ylabel('Price', color='black')
ax1.legend(loc='upper left')

![](img/04f194819b1ca088cd0727e50998488e_80.png)

# 创建第二个Y轴来显示交易信号
ax2 = ax1.twinx()
ax2.plot(stock_data['Position'], label='Trading Signal (1=Hold, 0=Sell)', color='red', alpha=0.3)
ax2.set_ylabel('Signal', color='red')
ax2.legend(loc='upper right')

![](img/04f194819b1ca088cd0727e50998488e_82.png)

plt.title('Stock Price, Moving Averages and Trading Signals')
plt.show()
```

![](img/04f194819b1ca088cd0727e50998488e_84.png)

---

## 策略逻辑与初步思考

在图表中，标志位为1的区域（红色背景）代表根据双均线策略应该**持有或买入**股票的时期；标志位为0的区域则代表应该**卖出或空仓**的时期。

这个策略的核心思想在于利用趋势的持续性。与“买入并持有”的被动策略不同，双均线策略试图在上升趋势开始时介入，在下降趋势开始时退出，从而规避部分下跌风险，并可能提高资金利用效率。

例如，假设初始资金为100元。在被动策略下，如果股价从100元跌至80元，则亏损20元。但在双均线策略中，我们会在死亡交叉信号出现时（假设股价为95元）卖出，然后在后续的黄金交叉信号出现时（假设股价为75元）再次买入。虽然股价总体下跌，但通过这次“高卖低买”的操作，我们反而实现了盈利（95-75=20元）。当然，实际收益取决于信号的质量和交易成本。

![](img/04f194819b1ca088cd0727e50998488e_86.png)

---

![](img/04f194819b1ca088cd0727e50998488e_88.png)

本节课中我们一起学习了双均线交易策略从数据预处理、指标计算到信号生成与可视化的完整流程。我们使用Pandas处理数据并计算移动平均线，利用Matplotlib将股价、均线及交易信号可视化，并初步探讨了该策略试图通过捕捉趋势变化来优化投资回报的逻辑。在后续课程中，我们将基于这些信号进行回测，量化评估该策略的历史表现。