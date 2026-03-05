# Python金融量化分析：13：买点与卖点可视化分析 📈

![](img/c7f2d0c273919ccf53db55dac38e95a2_0.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_2.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，核心是计算股票的短期与长期移动平均线，并通过它们的交叉点来识别潜在的买入和卖出信号。我们将从数据预处理开始，逐步完成计算、标记和可视化，最终理解如何利用这种策略进行简单的模拟。

![](img/c7f2d0c273919ccf53db55dac38e95a2_4.png)

## 概述

![](img/c7f2d0c273919ccf53db55dac38e95a2_6.png)

我们将使用苹果公司的股票数据作为示例。整个过程分为几个步骤：导入必要的库、加载并预处理数据、计算移动平均线、标记买卖信号，并进行可视化分析。通过本教程，你将掌握使用移动平均线交叉策略进行基础量化分析的方法。

![](img/c7f2d0c273919ccf53db55dac38e95a2_8.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_10.png)

---

![](img/c7f2d0c273919ccf53db55dac38e95a2_12.png)

## 数据准备与预处理

首先，我们需要导入分析所需的工具包，并加载股票数据。

![](img/c7f2d0c273919ccf53db55dac38e95a2_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/c7f2d0c273919ccf53db55dac38e95a2_16.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_18.png)

接下来，我们读取数据文件。这里假设数据文件为`data.csv`，并且第一列是日期时间，我们将把它设置为索引。

![](img/c7f2d0c273919ccf53db55dac38e95a2_20.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/c7f2d0c273919ccf53db55dac38e95a2_22.png)

数据加载后，我们选择苹果公司（假设列名为`AAPL`）的股价数据进行分析。同时，金融数据中常存在缺失值，这会影响移动平均线的计算，因此我们需要先去除缺失值。

![](img/c7f2d0c273919ccf53db55dac38e95a2_24.png)

```python
# 选择苹果公司的股价列
stock_data = data[['AAPL']].copy()
# 去除缺失值
stock_data.dropna(inplace=True)
# 查看前几行数据
print(stock_data.head())
```

![](img/c7f2d0c273919ccf53db55dac38e95a2_26.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_28.png)

至此，我们得到了一个干净、只包含苹果公司股价的`DataFrame`，为后续计算做好了准备。

---

![](img/c7f2d0c273919ccf53db55dac38e95a2_30.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_32.png)

## 计算移动平均线

![](img/c7f2d0c273919ccf53db55dac38e95a2_34.png)

上一节我们准备好了数据，本节中我们来看看如何计算短期和长期移动平均线。移动平均线是技术分析中常用的指标，用于平滑股价波动并识别趋势。

首先，我们需要定义短期和长期的时间窗口。由于我们的数据跨度较长（约7-8年），为了在图表中更清晰地观察交叉点，我们将短期窗口设置为42天，长期窗口设置为252天。在实际应用中，常见的短期为5天，长期为20天。

![](img/c7f2d0c273919ccf53db55dac38e95a2_36.png)

```python
short_window = 42
long_window = 252
```

定义好窗口后，我们使用`rolling`方法结合`mean`函数来计算移动平均线。计算完成后，将结果作为新列添加到我们的数据中。

以下是计算短期和长期移动平均线的步骤：

![](img/c7f2d0c273919ccf53db55dac38e95a2_38.png)

1.  计算短期移动平均线（SMA）。
2.  计算长期移动平均线（LMA）。

![](img/c7f2d0c273919ccf53db55dac38e95a2_40.png)

```python
# 计算短期移动平均线
stock_data[‘SMA_short’] = stock_data[‘AAPL’].rolling(window=short_window).mean()
# 计算长期移动平均线
stock_data[‘SMA_long’] = stock_data[‘AAPL’].rolling(window=long_window).mean()
```

![](img/c7f2d0c273919ccf53db55dac38e95a2_42.png)

计算完成后，我们可以查看数据的尾部，确认移动平均线已成功添加。由于使用了滚动窗口，数据前`long_window-1`行会出现缺失值（NaN），这是正常现象。

![](img/c7f2d0c273919ccf53db55dac38e95a2_44.png)

```python
print(stock_data.tail())
```

![](img/c7f2d0c273919ccf53db55dac38e95a2_46.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_48.png)

---

![](img/c7f2d0c273919ccf53db55dac38e95a2_50.png)

## 可视化移动平均线

![](img/c7f2d0c273919ccf53db55dac38e95a2_52.png)

计算完移动平均线后，我们可以通过图表直观地观察股价与两条均线的关系。图表能帮助我们快速识别均线交叉点，即潜在的买卖信号。

![](img/c7f2d0c273919ccf53db55dac38e95a2_54.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_56.png)

使用`matplotlib`可以轻松绘制股价和移动平均线。

```python
plt.figure(figsize=(14, 7))
plt.plot(stock_data[‘AAPL’], label=‘Apple Stock Price’, alpha=0.5)
plt.plot(stock_data[‘SMA_short’], label=f’{short_window}-Day SMA’, color=‘red’)
plt.plot(stock_data[‘SMA_long’], label=f’{long_window}-Day SMA’, color=‘green’)
plt.title(‘Apple Stock Price with Moving Averages’)
plt.xlabel(‘Date’)
plt.ylabel(‘Price’)
plt.legend()
plt.show()
```

![](img/c7f2d0c273919ccf53db55dac38e95a2_58.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_60.png)

在生成的图表中，红色线代表短期均线，绿色线代表长期均线。当短期均线从下方上穿长期均线时，形成“黄金交叉”，通常被视为买入信号；当短期均线从上方下穿长期均线时，形成“死亡交叉”，通常被视为卖出信号。图表中可以清晰地看到这些交叉点。

![](img/c7f2d0c273919ccf53db55dac38e95a2_62.png)

---

## 标记买卖信号

仅仅观察图表还不够，我们需要在数据中明确标记出这些信号。上一节我们通过可视化看到了交叉点，本节我们将通过编程方式标记短期均线与长期均线的位置关系。

![](img/c7f2d0c273919ccf53db55dac38e95a2_64.png)

首先，由于移动平均线计算产生了缺失值，我们需要先清理这些数据行。

![](img/c7f2d0c273919ccf53db55dac38e95a2_66.png)

```python
stock_data.dropna(inplace=True)
```

![](img/c7f2d0c273919ccf53db55dac38e95a2_68.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_70.png)

清理数据后，我们创建一个新的列`Position`作为标志位。当短期均线大于长期均线时，我们标记为`1`，表示处于“持有”或可考虑买入的状态；反之则标记为`0`。

![](img/c7f2d0c273919ccf53db55dac38e95a2_72.png)

我们可以使用`numpy`的`where`函数来实现这个逻辑判断。

![](img/c7f2d0c273919ccf53db55dac38e95a2_74.png)

```python
import numpy as np
stock_data[‘Position’] = np.where(stock_data[‘SMA_short’] > stock_data[‘SMA_long’], 1, 0)
```

查看数据的前几行，可以看到新增的`Position`列。

![](img/c7f2d0c273919ccf53db55dac38e95a2_76.png)

```python
print(stock_data.head())
```

![](img/c7f2d0c273919ccf53db55dac38e95a2_78.png)

我们也可以将这个买卖信号标志绘制在图表上，以便更直观地理解。由于股价和标志位（0或1）的量纲不同，我们需要使用双Y轴来展示。

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/c7f2d0c273919ccf53db55dac38e95a2_80.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_82.png)

ax1.plot(stock_data[‘AAPL’], label=‘Price’, color=‘blue’, alpha=0.6)
ax1.plot(stock_data[‘SMA_short’], label=f’{short_window}-Day SMA’, color=‘red’, linewidth=1)
ax1.plot(stock_data[‘SMA_long’], label=f’{long_window}-Day SMA’, color=‘green’, linewidth=1)
ax1.set_xlabel(‘Date’)
ax1.set_ylabel(‘Price’, color=‘black’)
ax1.tick_params(axis=‘y’, labelcolor=‘black’)
ax1.legend(loc=‘upper left’)

ax2 = ax1.twinx()
ax2.plot(stock_data[‘Position’], label=‘Buy/Sell Signal’, color=‘gray’, linestyle=‘—’, alpha=0.7)
ax2.set_ylabel(‘Signal’, color=‘gray’)
ax2.tick_params(axis=‘y’, labelcolor=‘gray’)
ax2.legend(loc=‘upper right’)

![](img/c7f2d0c273919ccf53db55dac38e95a2_84.png)

plt.title(‘Stock Price, Moving Averages, and Trading Signals’)
plt.show()
```

在这张图中，灰色的虚线代表买卖信号（1或0），它与均线交叉点对应，清晰地标出了策略建议的持仓时段。

![](img/c7f2d0c273919ccf53db55dac38e95a2_86.png)

---

![](img/c7f2d0c273919ccf53db55dac38e95a2_88.png)

![](img/c7f2d0c273919ccf53db55dac38e95a2_90.png)

## 策略思路简述

通过以上步骤，我们已经可以识别出基于双移动平均线交叉的买卖点。这引出了一个简单的交易策略思路：在“黄金交叉”（信号从0变为1）时买入，在“死亡交叉”（信号从1变为0）时卖出。

与单纯买入并持有的策略相比，这种趋势跟踪策略旨在抓住主要的股价上升波段，并在下跌趋势开始时退出，从而可能提高收益或降低风险。例如，假设初始资金为100元。在股价下跌时，如果策略发出卖出信号，我们以100元卖出。当股价跌至80元并出现买入信号时，我们再用80元买入相同的资产。在这个过程中，我们通过“高卖低买”实现了20元的盈利，而不是承受20元的账面亏损。

当然，这是一个简化的理论模型。实际交易中需要考虑手续费、滑点、信号滞后性等因素。本节课的重点是掌握识别和可视化这些核心信号的方法。

---

## 总结

![](img/c7f2d0c273919ccf53db55dac38e95a2_92.png)

本节课中我们一起学习了金融量化分析中的一个基础而重要的部分——利用双移动平均线进行买点与卖点的可视化分析。

我们首先完成了数据导入和预处理，选择了目标股票并清除了缺失值。接着，我们定义了短期和长期窗口，并计算了相应的移动平均线。然后，我们通过`matplotlib`将股价与均线绘制在图表上，直观地观察了“黄金交叉”和“死亡交叉”。为了量化这些信号，我们使用`np.where`函数创建了买卖标志位，并再次通过双Y轴图表进行了可视化展示。最后，我们探讨了基于此信号进行简单交易的策略思路。

![](img/c7f2d0c273919ccf53db55dac38e95a2_94.png)

通过本课实践，你应当能够使用Python计算移动平均线、识别交叉信号，并绘制专业的金融分析图表，为后续构建更复杂的量化交易模型打下坚实基础。