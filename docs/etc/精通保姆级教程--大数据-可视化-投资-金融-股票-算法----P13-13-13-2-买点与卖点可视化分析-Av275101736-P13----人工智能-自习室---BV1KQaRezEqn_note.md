# Python金融量化交易：13：买点与卖点可视化分析 📈

![](img/9ef34d7ca499c44a7ae590e4640d8471_0.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_2.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，特别是通过计算短期和长期移动平均线来识别股票的潜在买点与卖点。我们将从数据预处理开始，逐步计算技术指标，并最终通过图表直观地展示交易信号。

![](img/9ef34d7ca499c44a7ae590e4640d8471_4.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_6.png)

## 概述

![](img/9ef34d7ca499c44a7ae590e4640d8471_8.png)

我们将使用苹果公司的股票数据作为示例。整个过程分为几个步骤：导入必要的库、加载并预处理数据、计算短期和长期移动平均线、识别均线交叉点（即交易信号），并进行可视化分析。通过本教程，你将掌握量化分析中一个基础但强大的策略工具。

![](img/9ef34d7ca499c44a7ae590e4640d8471_10.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_12.png)

## 数据准备与预处理

首先，我们需要导入分析所需的Python库，并加载股票数据。

![](img/9ef34d7ca499c44a7ae590e4640d8471_14.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_16.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/9ef34d7ca499c44a7ae590e4640d8471_18.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_20.png)

我们复制之前课程中使用的代码来读取数据文件。这里指定第一列（日期）作为数据的索引。

![](img/9ef34d7ca499c44a7ae590e4640d8471_22.png)

```python
data = pd.read_csv(‘data.csv‘, index_col=0)
```

![](img/9ef34d7ca499c44a7ae590e4640d8471_24.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_26.png)

读取数据后，需要解析时间索引，以确保日期被正确识别。我们选择苹果公司（AAPL）的股票数据列进行后续分析。

![](img/9ef34d7ca499c44a7ae590e4640d8471_28.png)

```python
# 选择苹果公司的股票价格列，这里假设列名为‘AAPL‘
stock_data = data[[‘AAPL‘]]
```

![](img/9ef34d7ca499c44a7ae590e4640d8471_30.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_32.png)

数据中可能存在缺失值，这会影响移动平均线的计算。因此，预处理的第一步是删除所有缺失值。

![](img/9ef34d7ca499c44a7ae590e4640d8471_34.png)

```python
stock_data = stock_data.dropna()
print(stock_data.head())
```

完成上述步骤后，我们得到了一份干净、只包含苹果公司股价的`DataFrame`。

![](img/9ef34d7ca499c44a7ae590e4640d8471_36.png)

## 计算移动平均线

上一节我们准备好了数据，本节中我们来看看如何计算技术指标。我们将计算短期和长期简单移动平均线（SMA）。

![](img/9ef34d7ca499c44a7ae590e4640d8471_38.png)

首先，定义短期和长期的时间窗口。由于我们的数据集跨度较大（约7-8年），为了在图表中更清晰地观察趋势，我们将窗口值设置得比通常的5日和20日更大一些。

![](img/9ef34d7ca499c44a7ae590e4640d8471_40.png)

```python
short_window = 40
long_window = 100
```

![](img/9ef34d7ca499c44a7ae590e4640d8471_42.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_44.png)

接下来，我们计算移动平均线。这可以通过Pandas的`rolling`和`mean`方法实现。

![](img/9ef34d7ca499c44a7ae590e4640d8471_46.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_48.png)

```python
# 计算短期移动平均线
stock_data[‘SMA_short‘] = stock_data[‘AAPL‘].rolling(window=short_window).mean()
# 计算长期移动平均线
stock_data[‘SMA_long‘] = stock_data[‘AAPL‘].rolling(window=long_window).mean()
```

![](img/9ef34d7ca499c44a7ae590e4640d8471_50.png)

计算完成后，查看数据的尾部以确认结果。前`long_window-1`个数据点由于窗口不足，其移动平均值为`NaN`。

![](img/9ef34d7ca499c44a7ae590e4640d8471_52.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_54.png)

```python
print(stock_data.tail())
```

![](img/9ef34d7ca499c44a7ae590e4640d8471_56.png)

## 可视化均线交叉

![](img/9ef34d7ca499c44a7ae590e4640d8471_58.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_60.png)

计算完移动平均线后，我们可以通过绘图来直观地观察它们的走势关系。

![](img/9ef34d7ca499c44a7ae590e4640d8471_62.png)

```python
plt.figure(figsize=(14, 7))
plt.plot(stock_data[‘AAPL‘], label=‘Apple Stock Price‘, alpha=0.5)
plt.plot(stock_data[‘SMA_short‘], label=f‘{short_window}-Day SMA‘)
plt.plot(stock_data[‘SMA_long‘], label=f‘{long_window}-Day SMA‘)
plt.legend(loc=‘best‘)
plt.title(‘Apple Stock Price with Moving Averages‘)
plt.show()
```

在生成的图表中，短期均线（SMA_short）波动更频繁，长期均线（SMA_long）则更加平滑。两者的交叉点具有重要的交易信号意义：
*   **黄金交叉**：短期均线从下向上穿越长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿越长期均线，通常被视为**卖出信号**。

![](img/9ef34d7ca499c44a7ae590e4640d8471_64.png)

## 标记交易信号

![](img/9ef34d7ca499c44a7ae590e4640d8471_66.png)

为了量化这些交叉点，我们需要创建一个标志位来记录短期均线是否高于长期均线。

![](img/9ef34d7ca499c44a7ae590e4640d8471_68.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_70.png)

首先，需要删除在计算移动平均线时产生的缺失值。

![](img/9ef34d7ca499c44a7ae590e4640d8471_72.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_74.png)

```python
stock_data.dropna(inplace=True)
```

然后，使用`np.where`函数创建标志列。当短期均线大于长期均线时，标记为1，否则标记为0。

![](img/9ef34d7ca499c44a7ae590e4640d8471_76.png)

```python
import numpy as np
stock_data[‘Position‘] = np.where(stock_data[‘SMA_short‘] > stock_data[‘SMA_long‘], 1, 0)
print(stock_data.head())
```

![](img/9ef34d7ca499c44a7ae590e4640d8471_78.png)

`Position`列为1代表短期趋势强于长期趋势，为0则相反。这个标志位的变化点（从0变1或从1变0）就对应着均线的交叉点。

![](img/9ef34d7ca499c44a7ae590e4640d8471_80.png)

我们也可以将股价、均线和交易信号标志位画在一张图上。

![](img/9ef34d7ca499c44a7ae590e4640d8471_82.png)

```python
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

![](img/9ef34d7ca499c44a7ae590e4640d8471_84.png)

ax1.plot(stock_data[‘AAPL‘], label=‘Price‘)
ax1.plot(stock_data[‘SMA_short‘], label=‘Short SMA‘)
ax1.plot(stock_data[‘SMA_long‘], label=‘Long SMA‘)
ax1.legend(loc=‘best‘)
ax1.set_title(‘Apple Stock Price and Moving Averages‘)

ax2.plot(stock_data[‘Position‘], label=‘Trading Signal‘, color=‘red‘, drawstyle=‘steps-post‘)
ax2.legend(loc=‘best‘)
ax2.set_title(‘Buy/Sell Signal (1=Buy, 0=Sell)‘)
ax2.set_ylim([-0.1, 1.1])

![](img/9ef34d7ca499c44a7ae590e4640d8471_86.png)

![](img/9ef34d7ca499c44a7ae590e4640d8471_88.png)

plt.show()
```

![](img/9ef34d7ca499c44a7ae590e4640d8471_90.png)

## 理解策略逻辑

![](img/9ef34d7ca499c44a7ae590e4640d8471_92.png)

最后，我们来探讨一下这个策略背后的逻辑。如果不采取任何策略，只是简单地买入并持有，投资者的收益完全取决于股价的最终涨跌。

然而，双均线策略提供了一种系统性的方法：
1.  当出现**黄金交叉**（`Position`从0变为1）时，策略发出买入信号。
2.  当出现**死亡交叉**（`Position`从1变为0）时，策略发出卖出信号。

理论上，通过在这种信号下进行交易，投资者可以在市场上涨时持有头寸，在市场下跌时卖出以规避风险或持有现金，从而可能获得比简单持有更好的风险调整后收益。这就像拥有了一个基于历史数据的“导航系统”，虽然不能预测未来，但能根据既定规则做出反应。

## 总结

![](img/9ef34d7ca499c44a7ae590e4640d8471_94.png)

本节课中我们一起学习了量化交易中的一个基础分析流程：
1.  **数据预处理**：导入数据、处理缺失值。
2.  **指标计算**：定义了短期和长期窗口，并计算了对应的简单移动平均线（SMA）。
3.  **信号生成**：通过比较两条均线，创建了标志位来量化**黄金交叉**（买点）和**死亡交叉**（卖点）。
4.  **可视化分析**：使用Matplotlib绘制了股价、移动平均线以及交易信号图，使分析结果一目了然。

![](img/9ef34d7ca499c44a7ae590e4640d8471_96.png)

通过本教程，你掌握了使用双移动平均线交叉策略进行初步量化分析的方法，这是构建更复杂交易系统的基石。