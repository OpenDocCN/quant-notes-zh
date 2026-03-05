# Python金融分析与量化交易实战：P10：02-2-买点与卖点可视化分析 📈

![](img/730d03ca62094afafd1c0e73bfa69d96_0.png)

![](img/730d03ca62094afafd1c0e73bfa69d96_2.png)

在本节课中，我们将学习如何利用Python进行金融数据的可视化分析，特别是通过计算短期和长期移动平均线来识别股票的潜在买点与卖点。我们将使用苹果公司的股价数据作为示例，一步步完成数据预处理、指标计算和策略可视化。

![](img/730d03ca62094afafd1c0e73bfa69d96_4.png)

---

![](img/730d03ca62094afafd1c0e73bfa69d96_6.png)

## 第一步：导入工具包与读取数据

![](img/730d03ca62094afafd1c0e73bfa69d96_8.png)

![](img/730d03ca62094afafd1c0e73bfa69d96_10.png)

首先，我们需要导入必要的Python库并加载数据。我们将使用`pandas`进行数据处理，`matplotlib`进行绘图。

![](img/730d03ca62094afafd1c0e73bfa69d96_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

接下来，读取包含股价数据的CSV文件。我们将第一列（时间）设置为索引，并解析日期格式。

![](img/730d03ca62094afafd1c0e73bfa69d96_14.png)

![](img/730d03ca62094afafd1c0e73bfa69d96_16.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/730d03ca62094afafd1c0e73bfa69d96_18.png)

数据读取完成后，我们选择苹果公司（AAPL）的股价列作为分析对象。

![](img/730d03ca62094afafd1c0e73bfa69d96_20.png)

```python
# 选择苹果公司的股价数据
stock_data = data[['AAPL']]
```

![](img/730d03ca62094afafd1c0e73bfa69d96_22.png)

![](img/730d03ca62094afafd1c0e73bfa69d96_24.png)

---

![](img/730d03ca62094afafd1c0e73bfa69d96_26.png)

## 第二步：数据预处理

![](img/730d03ca62094afafd1c0e73bfa69d96_28.png)

在开始分析之前，我们需要处理数据中可能存在的缺失值，以确保后续计算的准确性。

![](img/730d03ca62094afafd1c0e73bfa69d96_30.png)

```python
# 删除缺失值
stock_data = stock_data.dropna()
```

![](img/730d03ca62094afafd1c0e73bfa69d96_32.png)

执行上述操作后，我们得到了一个干净、只包含苹果公司股价的数据集。

---

![](img/730d03ca62094afafd1c0e73bfa69d96_34.png)

## 第三步：计算移动平均线

移动平均线是技术分析中常用的指标。本节中，我们将计算短期和长期移动平均线。

![](img/730d03ca62094afafd1c0e73bfa69d96_36.png)

首先，定义短期和长期的窗口大小。由于我们的数据跨度较长（约7-8年），为了在图表中更清晰地观察趋势，我们将窗口设置得稍大一些。

```python
# 定义短期和长期窗口
short_window = 42
long_window = 252
```

![](img/730d03ca62094afafd1c0e73bfa69d96_38.png)

![](img/730d03ca62094afafd1c0e73bfa69d96_40.png)

接下来，计算短期和长期的简单移动平均（SMA）。

![](img/730d03ca62094afafd1c0e73bfa69d96_42.png)

```python
# 计算短期移动平均线
stock_data['SMA_short'] = stock_data['AAPL'].rolling(window=short_window).mean()

![](img/730d03ca62094afafd1c0e73bfa69d96_44.png)

![](img/730d03ca62094afafd1c0e73bfa69d96_46.png)

# 计算长期移动平均线
stock_data['SMA_long'] = stock_data['AAPL'].rolling(window=long_window).mean()
```

![](img/730d03ca62094afafd1c0e73bfa69d96_48.png)

计算完成后，数据的前几行由于窗口不足会显示为缺失值（NaN），这是正常现象。我们可以查看数据的尾部来确认计算成功。

![](img/730d03ca62094afafd1c0e73bfa69d96_50.png)

---

![](img/730d03ca62094afafd1c0e73bfa69d96_52.png)

![](img/730d03ca62094afafd1c0e73bfa69d96_54.png)

## 第四步：可视化移动平均线

![](img/730d03ca62094afafd1c0e73bfa69d96_56.png)

现在，我们可以将原始股价与两条移动平均线绘制在同一张图上进行观察。

![](img/730d03ca62094afafd1c0e73bfa69d96_58.png)

```python
# 绘制股价与移动平均线
stock_data[['AAPL', 'SMA_short', 'SMA_long']].plot(figsize=(14, 7))
plt.title('Apple Stock Price with Moving Averages')
plt.ylabel('Price')
plt.show()
```

在生成的图表中，我们可以观察到：
*   **原始股价**（通常为蓝色或红色线）。
*   **短期移动平均线**（例如红色线）。
*   **长期移动平均线**（例如绿色线）。

两条移动平均线的交叉点是我们关注的重点：
*   **黄金交叉**：当短期均线从下向上穿越长期均线时，通常被视为**买入信号**。
*   **死亡交叉**：当短期均线从上向下穿越长期均线时，通常被视为**卖出信号**。

![](img/730d03ca62094afafd1c0e73bfa69d96_60.png)

---

![](img/730d03ca62094afafd1c0e73bfa69d96_62.png)

![](img/730d03ca62094afafd1c0e73bfa69d96_64.png)

## 第五步：标记买卖信号

![](img/730d03ca62094afafd1c0e73bfa69d96_66.png)

为了量化这些信号，我们创建一个新的列来标记短期均线是否高于长期均线。

![](img/730d03ca62094afafd1c0e73bfa69d96_68.png)

![](img/730d03ca62094afafd1c0e73bfa69d96_70.png)

首先，确保数据中已无缺失值。

```python
# 再次删除因计算移动平均线产生的缺失值
stock_data = stock_data.dropna()
```

![](img/730d03ca62094afafd1c0e73bfa69d96_72.png)

然后，使用`numpy`的`where`函数创建标志位。

```python
import numpy as np

![](img/730d03ca62094afafd1c0e73bfa69d96_74.png)

# 创建标志位：1表示短期均线高于长期均线（潜在持有/买入期），0表示相反（潜在卖出期）
stock_data['Position'] = np.where(stock_data['SMA_short'] > stock_data['SMA_long'], 1, 0)
```

![](img/730d03ca62094afafd1c0e73bfa69d96_76.png)

现在，数据集中多了一个“Position”列，其值为1或0，清晰地标明了均线的相对位置关系。

![](img/730d03ca62094afafd1c0e73bfa69d96_78.png)

我们也可以将这个标志位绘制在股价图下方作为参考。

```python
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

# 上方子图：绘制股价和移动平均线
ax1.plot(stock_data['AAPL'], label='AAPL Price', alpha=0.5)
ax1.plot(stock_data['SMA_short'], label=f'SMA {short_window}')
ax1.plot(stock_data['SMA_long'], label=f'SMA {long_window}')
ax1.set_ylabel('Price')
ax1.legend()
ax1.set_title('Apple Stock Price and Moving Averages')

![](img/730d03ca62094afafd1c0e73bfa69d96_80.png)

![](img/730d03ca62094afafd1c0e73bfa69d96_82.png)

# 下方子图：绘制买卖信号标志位
ax2.fill_between(stock_data.index, 0, stock_data['Position'], color='grey', alpha=0.3, label='Position (1=Hold/Buy)')
ax2.set_ylabel('Position')
ax2.set_xlabel('Date')
ax2.legend()

![](img/730d03ca62094afafd1c0e73bfa69d96_84.png)

plt.show()
```

---

## 第六步：理解策略逻辑

最后，我们来理解基于双均线交叉策略的核心逻辑。

假设我们初始有100元本金：
*   **无策略情况（买入并持有）**：在股价波动中，100元可能变为80元，意味着亏损20元。
*   **双均线策略情况**：
    1.  当出现“死亡交叉”（卖出信号）时，我们卖出股票，持有现金。
    2.  当出现“黄金交叉”（买入信号）时，我们用现金买入股票。
    3.  通过这种“高卖低买”的规则，目标是在长期波动中获取比简单持有更高的收益。例如，在股价高点卖出获得100元现金，在随后的低点用80元买回等量股票，相当于赚取了20元的价差。

**请注意**：这是一个简化的理论模型。在实际交易中，需要综合考虑交易成本、滑点、策略滞后性以及市场突发事件等因素。

![](img/730d03ca62094afafd1c0e73bfa69d96_86.png)

---

![](img/730d03ca62094afafd1c0e73bfa69d96_88.png)

本节课中我们一起学习了如何使用Python进行金融数据的可视化分析。我们从数据导入和预处理开始，接着计算了短期与长期移动平均线，并通过图表直观地展示了“黄金交叉”和“死亡交叉”这两个关键的技术信号。最后，我们创建了买卖信号标志位，并探讨了基于此信号的简单交易策略逻辑。这为后续构建和回测量化交易策略打下了坚实的基础。