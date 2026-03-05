# 金融量化分析：P12：2-买点与卖点可视化分析 📈

![](img/7cccf2f879f6a99312c1e2cad0044ebd_0.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_2.png)

在本节课中，我们将学习如何通过计算短期和长期移动平均线，来识别股票交易中的潜在买点和卖点，并进行可视化分析。我们将以苹果公司的股票数据为例，一步步完成数据处理、指标计算和策略模拟。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_3.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_4.png)

## 数据准备与预处理

![](img/7cccf2f879f6a99312c1e2cad0044ebd_6.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_7.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_9.png)

上一节我们介绍了量化分析的基本概念，本节中我们来看看如何具体处理数据。首先，我们需要导入必要的工具包并加载数据。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_11.png)

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/7cccf2f879f6a99312c1e2cad0044ebd_12.png)

读取股票数据文件，并将第一列（时间）设置为索引，同时解析时间格式。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_14.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_15.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_16.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/7cccf2f879f6a99312c1e2cad0044ebd_18.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_19.png)

数据集中包含多只股票，我们选择苹果公司的股票作为分析案例。

```python
stock_data = data[['AAPL']].copy()
```

![](img/7cccf2f879f6a99312c1e2cad0044ebd_21.png)

在预处理阶段，一个关键步骤是处理缺失值，以确保后续计算的准确性。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_22.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_23.png)

```python
stock_data.dropna(inplace=True)
print(stock_data.head())
```

![](img/7cccf2f879f6a99312c1e2cad0044ebd_25.png)

## 计算移动平均线

![](img/7cccf2f879f6a99312c1e2cad0044ebd_27.png)

完成数据清洗后，下一步是计算技术指标。我们将计算短期和长期简单移动平均线（SMA）。

首先，定义短期和长期的窗口大小。由于我们的数据集跨度约7-8年，为了在图表中更清晰地观察趋势，我们将窗口设置得比常规稍大一些。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_29.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_30.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_31.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_32.png)

```python
short_window = 40
long_window = 100
```

以下是计算移动平均线的步骤：

1.  使用 `rolling()` 方法创建指定大小的数据窗口。
2.  在每个窗口内计算收盘价的平均值（`mean()`）。
3.  将计算结果作为新列添加到数据框中。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_34.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_35.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_36.png)

```python
stock_data['SMA_Short'] = stock_data['AAPL'].rolling(window=short_window).mean()
stock_data['SMA_Long'] = stock_data['AAPL'].rolling(window=long_window).mean()
```

![](img/7cccf2f879f6a99312c1e2cad0044ebd_37.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_38.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_39.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_40.png)

计算初期会产生一些缺失值（因为前 `short_window-1` 和 `long_window-1` 个数据点无法形成完整窗口），这是正常现象。我们可以再次删除这些缺失值。

```python
stock_data.dropna(inplace=True)
print(stock_data.head())
```

## 可视化均线与交叉点

![](img/7cccf2f879f6a99312c1e2cad0044ebd_42.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_43.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_44.png)

现在，我们可以将股价和两条移动平均线绘制在同一张图上进行观察。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_45.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_46.png)

```python
stock_data[['AAPL', 'SMA_Short', 'SMA_Long']].plot(figsize=(12, 6))
plt.show()
```

![](img/7cccf2f879f6a99312c1e2cad0044ebd_48.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_49.png)

在生成的图表中，我们可以分析短期均线（SMA_Short）与长期均线（SMA_Long）的交叉点：
*   **黄金交叉**：当短期均线从下向上穿越长期均线时，通常被视为**买入信号**。
*   **死亡交叉**：当短期均线从上向下穿越长期均线时，通常被视为**卖出信号**。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_51.png)

## 标记交易信号

![](img/7cccf2f879f6a99312c1e2cad0044ebd_53.png)

为了量化这些交叉点，我们需要创建一个标志位来记录短期均线与长期均线的位置关系。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_54.png)

我们使用 `np.where()` 函数来创建这个标志列 `Position`：
*   当 `SMA_Short > SMA_Long` 时，`Position` 为 1。
*   否则，`Position` 为 0。

```python
stock_data['Position'] = np.where(stock_data['SMA_Short'] > stock_data['SMA_Long'], 1, 0)
```

我们可以将股价和交易信号位置绘制在双轴图上，以便更直观地观察信号与价格的关系。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_56.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_57.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_58.png)

```python
ax = stock_data[['AAPL', 'SMA_Short', 'SMA_Long']].plot(figsize=(10, 6))
stock_data['Position'].plot(ax=ax, secondary_y=True, style='g')
plt.show()
```

## 策略回测思路

最后，我们来探讨基于此双均线策略的交易逻辑。假设有两种投资方式：
1.  **买入并持有**：在期初买入股票并一直持有到期末，其收益完全取决于股价的最终涨跌。
2.  **双均线策略**：根据我们计算的 `Position` 标志位进行交易。
    *   当 `Position` 从 0 变为 1 时（黄金交叉），执行**买入**。
    *   当 `Position` 从 1 变为 0 时（死亡交叉），执行**卖出**。

该策略的核心思想是试图通过“高抛低吸”来超越简单的买入持有收益。例如，在股价阶段性高点（死亡交叉）卖出，在随后的低点（黄金交叉）再买回，从而在股价波动中获取差价收益。

![](img/7cccf2f879f6a99312c1e2cad0044ebd_60.png)

![](img/7cccf2f879f6a99312c1e2cad0044ebd_62.png)

**本节课中我们一起学习了**如何利用移动平均线识别交易信号。我们完成了从数据加载、预处理、计算技术指标（SMA），到可视化分析及生成交易信号标志位的全过程。这为后续进行策略回测和绩效评估奠定了坚实的基础。