# 量化交易完全可自学教程：P70：8.7.9.2-买点与卖点可视化分析P9 📈

![](img/81f5a332fed114ad6bd055f0516ab1c6_0.png)

![](img/81f5a332fed114ad6bd055f0516ab1c6_2.png)

在本节课中，我们将学习如何使用Python进行双均线策略的买点与卖点可视化分析。我们将通过处理苹果公司的股价数据，计算短期和长期移动平均线，识别交叉点，并最终通过图表直观展示交易信号。

![](img/81f5a332fed114ad6bd055f0516ab1c6_4.png)

---

![](img/81f5a332fed114ad6bd055f0516ab1c6_6.png)

![](img/81f5a332fed114ad6bd055f0516ab1c6_8.png)

## 第一步：导入工具包与读取数据 📂

![](img/81f5a332fed114ad6bd055f0516ab1c6_10.png)

首先，我们需要导入分析所需的Python库，并加载我们的股票数据。

![](img/81f5a332fed114ad6bd055f0516ab1c6_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

接下来，我们读取包含股价数据的CSV文件。我们将第一列指定为索引，并将其解析为日期时间格式。

![](img/81f5a332fed114ad6bd055f0516ab1c6_14.png)

![](img/81f5a332fed114ad6bd055f0516ab1c6_16.png)

```python
# 读取数据，指定第一列为索引（日期），并解析日期
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/81f5a332fed114ad6bd055f0516ab1c6_18.png)

![](img/81f5a332fed114ad6bd055f0516ab1c6_20.png)

数据读取完成后，我们选择苹果公司（AAPL）的股价作为本次分析的示例。

![](img/81f5a332fed114ad6bd055f0516ab1c6_22.png)

```python
# 选择苹果公司的股价数据
stock_data = data[['AAPL']]
```

![](img/81f5a332fed114ad6bd055f0516ab1c6_24.png)

---

![](img/81f5a332fed114ad6bd055f0516ab1c6_26.png)

## 第二步：数据预处理与缺失值处理 🧹

![](img/81f5a332fed114ad6bd055f0516ab1c6_28.png)

在开始计算之前，我们需要对数据进行预处理。一个常见的问题是数据中可能存在缺失值，这会影响后续移动平均线的计算。因此，我们的第一步是移除这些缺失值。

![](img/81f5a332fed114ad6bd055f0516ab1c6_30.png)

```python
# 移除数据中的缺失值
stock_data = stock_data.dropna()
```

![](img/81f5a332fed114ad6bd055f0516ab1c6_32.png)

处理完成后，我们可以查看一下数据的前几行，确认数据已准备就绪。

```python
print(stock_data.head())
```

![](img/81f5a332fed114ad6bd055f0516ab1c6_34.png)

---

## 第三步：计算短期与长期移动平均线 📊

![](img/81f5a332fed114ad6bd055f0516ab1c6_36.png)

上一节我们完成了数据清洗，本节中我们来看看如何计算关键的技术指标——移动平均线。

移动平均线是分析股价趋势的重要工具。我们需要定义两个参数：短期窗口和长期窗口。通常，短期常用5天，长期常用20天。但由于我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察交叉点，我们将适当调大这两个参数。

![](img/81f5a332fed114ad6bd055f0516ab1c6_38.png)

![](img/81f5a332fed114ad6bd055f0516ab1c6_40.png)

以下是计算移动平均线的步骤：

![](img/81f5a332fed114ad6bd055f0516ab1c6_42.png)

1.  定义短期和长期的时间窗口。
2.  使用Pandas的`rolling`方法计算指定窗口内的股价均值。

![](img/81f5a332fed114ad6bd055f0516ab1c6_44.png)

![](img/81f5a332fed114ad6bd055f0516ab1c6_46.png)

```python
# 定义短期和长期窗口大小
short_window = 40
long_window = 100

![](img/81f5a332fed114ad6bd055f0516ab1c6_48.png)

# 计算短期移动平均线 (SMA)
stock_data['SMA_short'] = stock_data['AAPL'].rolling(window=short_window).mean()

![](img/81f5a332fed114ad6bd055f0516ab1c6_50.png)

# 计算长期移动平均线 (SMA)
stock_data['SMA_long'] = stock_data['AAPL'].rolling(window=long_window).mean()
```

![](img/81f5a332fed114ad6bd055f0516ab1c6_52.png)

![](img/81f5a332fed114ad6bd055f0516ab1c6_54.png)

计算完成后，我们可以查看数据的尾部，确认移动平均线已成功计算。前`long_window-1`个数据点由于窗口不足，其移动平均值会显示为`NaN`，这是正常现象。

![](img/81f5a332fed114ad6bd055f0516ab1c6_56.png)

```python
print(stock_data.tail())
```

![](img/81f5a332fed114ad6bd055f0516ab1c6_58.png)

---

![](img/81f5a332fed114ad6bd055f0516ab1c6_60.png)

## 第四步：可视化股价与移动平均线 📉

计算完指标后，我们可以通过绘图来直观地观察股价走势以及双均线的交叉情况。

```python
# 绘制股价与移动平均线
plt.figure(figsize=(14, 7))
plt.plot(stock_data['AAPL'], label='AAPL Price', alpha=0.5)
plt.plot(stock_data['SMA_short'], label=f'SMA {short_window}', color='red')
plt.plot(stock_data['SMA_long'], label=f'SMA {long_window}', color='green')
plt.title('AAPL Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/81f5a332fed114ad6bd055f0516ab1c6_62.png)

![](img/81f5a332fed114ad6bd055f0516ab1c6_64.png)

在生成的图表中：
*   **原始股价**（浅色线）代表实际价格波动。
*   **短期均线**（红线）对价格变化更敏感。
*   **长期均线**（绿线）趋势更平缓。

![](img/81f5a332fed114ad6bd055f0516ab1c6_66.png)

**关键观察点**：
*   当短期均线**从下向上**穿越长期均线时，形成“黄金交叉”，通常被视为**买入信号**。
*   当短期均线**从上向下**穿越长期均线时，形成“死亡交叉”，通常被视为**卖出信号**。
图表中的交叉点就是我们需要关注的潜在交易点。

![](img/81f5a332fed114ad6bd055f0516ab1c6_68.png)

![](img/81f5a332fed114ad6bd055f0516ab1c6_70.png)

---

![](img/81f5a332fed114ad6bd055f0516ab1c6_72.png)

## 第五步：标记均线位置关系 🎯

为了量化交易信号，我们需要创建一个标志位来明确记录短期均线与长期均线的位置关系。

![](img/81f5a332fed114ad6bd055f0516ab1c6_74.png)

在此之前，我们需要再次清理数据，移除计算移动平均线时产生的新缺失值。

![](img/81f5a332fed114ad6bd055f0516ab1c6_76.png)

```python
# 移除因计算移动平均线而产生的缺失值
stock_data = stock_data.dropna()
```

![](img/81f5a332fed114ad6bd055f0516ab1c6_78.png)

接下来，我们创建`position`列。当短期均线大于长期均线时，标记为1（代表持有或看涨）；否则标记为0（代表不持有或看跌）。

![](img/81f5a332fed114ad6bd055f0516ab1c6_80.png)

```python
# 创建标志位：短期均线 > 长期均线 时为1，否则为0
stock_data['position'] = np.where(stock_data['SMA_short'] > stock_data['SMA_long'], 1, 0)
```

我们可以查看添加了新列的数据：

```python
print(stock_data[['AAPL', 'SMA_short', 'SMA_long', 'position']].head())
```

![](img/81f5a332fed114ad6bd055f0516ab1c6_82.png)

![](img/81f5a332fed114ad6bd055f0516ab1c6_84.png)

`position`列从0变为1或从1变为0的时刻，就对应了图表上的均线交叉点，即理论上的买卖点。

![](img/81f5a332fed114ad6bd055f0516ab1c6_86.png)

---

## 第六步：理解策略逻辑与收益 💡

最后，我们来探讨一下这个双均线策略背后的逻辑。

假设我们最初有100元本金。
*   **无策略情况**：如果在股价高点买入并一直持有，到股价低点时，账户价值可能缩水至80元，亏损20元。
*   **双均线策略**：策略旨在通过“信号”指导操作。
    *   当出现“死亡交叉”（卖出信号）时，我们卖出股票，持有现金。
    *   当出现“黄金交叉”（买入信号）时，我们用现金买入股票。
    *   理想情况下，我们在价格相对高位卖出，在价格相对低位买入。

因此，即使股价从100元跌至80元，遵循策略的投资者可能在90元时卖出，然后在75元时重新买入。这样，他不仅避免了下跌损失，反而通过这次波段操作获得了额外的股份（同样的现金在更低价格能买更多股），为后续上涨积累了优势。

**核心思想**：双均线策略的目标不是预测最高最低点，而是跟随趋势，在上涨趋势开始时介入，在下跌趋势开始时退出，从而在长期波动中获取超额收益。

![](img/81f5a332fed114ad6bd055f0516ab1c6_88.png)

---

![](img/81f5a332fed114ad6bd055f0516ab1c6_90.png)

本节课中我们一起学习了双均线策略从数据准备、指标计算到可视化分析的全过程。我们掌握了如何用Python计算移动平均线，如何在图表上识别“黄金交叉”和“死亡交叉”，并理解了如何利用这些交叉点作为量化交易的买卖信号。这为后续构建完整的回测和交易系统打下了坚实的基础。