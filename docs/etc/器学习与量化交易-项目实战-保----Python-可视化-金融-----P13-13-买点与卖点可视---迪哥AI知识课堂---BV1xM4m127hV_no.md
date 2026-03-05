# 机器学习与量化交易：13：买点与卖点可视化分析 📈

![](img/ddb16544b0ef6b94a5839e5abf0396ea_0.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_2.png)

在本节课中，我们将学习如何利用双均线策略（短期均线与长期均线）来识别股票交易中的潜在买点和卖点，并通过Python进行数据可视化分析。我们将以苹果公司的股票数据为例，从数据预处理、指标计算到策略信号生成，一步步构建一个简单的交易信号分析系统。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_4.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_6.png)

---

![](img/ddb16544b0ef6b94a5839e5abf0396ea_8.png)

## 数据准备与预处理

![](img/ddb16544b0ef6b94a5839e5abf0396ea_10.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_12.png)

首先，我们需要导入必要的Python工具包并加载数据。

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/ddb16544b0ef6b94a5839e5abf0396ea_14.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_16.png)

接下来，我们读取包含股票价格的历史数据文件。数据的第一列是时间索引，我们需要将其正确解析。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_18.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_20.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/ddb16544b0ef6b94a5839e5abf0396ea_22.png)

数据集中包含多只股票的信息。在本教程中，我们选择苹果公司的股票进行分析。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_24.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_26.png)

```python
apple_data = data[['AAPL']].copy()
```

数据中可能存在缺失值，这会影响后续移动平均线的计算。因此，第一步是去除所有缺失值。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_28.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_30.png)

```python
apple_data.dropna(inplace=True)
print(apple_data.head())
```

![](img/ddb16544b0ef6b94a5839e5abf0396ea_32.png)

---

![](img/ddb16544b0ef6b94a5839e5abf0396ea_34.png)

## 计算短期与长期移动平均线

上一节我们完成了数据清洗。本节中，我们来计算关键的策略指标：短期和长期移动平均线。

移动平均线能平滑价格波动，反映趋势。我们首先定义短期和长期的窗口大小。由于我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察交叉点，我们将窗口设置得比通常的5日和20日更大一些。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_36.png)

```python
short_window = 42
long_window = 252
```

![](img/ddb16544b0ef6b94a5839e5abf0396ea_38.png)

以下是计算移动平均线的步骤。我们使用`rolling`方法创建滑动窗口，并计算每个窗口内收盘价的均值。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_40.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_42.png)

```python
# 计算短期移动平均线
apple_data['SMA_Short'] = apple_data['AAPL'].rolling(window=short_window).mean()
# 计算长期移动平均线
apple_data['SMA_Long'] = apple_data['AAPL'].rolling(window=long_window).mean()
```

![](img/ddb16544b0ef6b94a5839e5abf0396ea_44.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_46.png)

计算完成后，数据尾部会显示有效的移动平均值，而头部因窗口不足会显示为缺失值。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_48.png)

```python
print(apple_data.tail())
```

![](img/ddb16544b0ef6b94a5839e5abf0396ea_50.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_52.png)

---

![](img/ddb16544b0ef6b94a5839e5abf0396ea_54.png)

## 可视化移动平均线与交叉点

![](img/ddb16544b0ef6b94a5839e5abf0396ea_56.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_58.png)

计算完指标后，我们可以通过图表直观地观察价格与两条均线的关系。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_60.png)

```python
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='Apple Stock Price', alpha=0.5)
plt.plot(apple_data['SMA_Short'], label=f'Short MA ({short_window} days)', color='red')
plt.plot(apple_data['SMA_Long'], label=f'Long MA ({long_window} days)', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.legend()
plt.show()
```

在生成的图表中，关注短期均线（红色）与长期均线（绿色）的交叉点：
*   **黄金交叉**：短期均线从下向上穿越长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿越长期均线，通常被视为**卖出信号**。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_62.png)

---

![](img/ddb16544b0ef6b94a5839e5abf0396ea_64.png)

## 生成交易信号

![](img/ddb16544b0ef6b94a5839e5abf0396ea_66.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_68.png)

从图表中我们可以手动识别交叉点。本节中，我们来看看如何通过编程自动标记这些信号。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_70.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_72.png)

首先，需要去除计算移动平均线时产生的缺失值，以确保数据连贯。

```python
apple_data.dropna(inplace=True)
```

![](img/ddb16544b0ef6b94a5839e5abf0396ea_74.png)

接下来，我们创建一个标志位`Position`。该标志位表示短期均线是否高于长期均线，这可以帮助我们系统化地识别趋势。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_76.png)

```python
apple_data['Position'] = np.where(apple_data['SMA_Short'] > apple_data['SMA_Long'], 1, 0)
print(apple_data.head())
```
*   `Position = 1`：表示短期均线高于长期均线，处于潜在上涨或持有阶段。
*   `Position = 0`：表示短期均线低于长期均线，处于潜在下跌或空仓阶段。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_78.png)

我们也可以将股价、均线和交易信号位置绘制在同一张图上进行对比。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_80.png)

```python
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_82.png)

ax1.plot(apple_data['AAPL'], label='Price')
ax1.plot(apple_data['SMA_Short'], label='Short MA')
ax1.plot(apple_data['SMA_Long'], label='Long MA')
ax1.set_ylabel('Price')
ax1.legend()
ax1.set_title('Price and Moving Averages')

ax2.plot(apple_data['Position'], label='Trading Signal (1=Hold/Buy, 0=Sell)', color='orange', drawstyle='steps-post')
ax2.set_ylabel('Signal')
ax2.set_xlabel('Date')
ax2.legend()

![](img/ddb16544b0ef6b94a5839e5abf0396ea_84.png)

![](img/ddb16544b0ef6b94a5839e5abf0396ea_86.png)

plt.show()
```

![](img/ddb16544b0ef6b94a5839e5abf0396ea_88.png)

---

## 策略逻辑与收益思考

通过以上步骤，我们已经可以识别出理论上的买卖点。现在我们来探讨这个策略背后的逻辑。

假设我们最初有100元本金：
*   **无策略情况**：如果在价格高点买入并一直持有，在价格跌至80元时，我们亏损20元。
*   **双均线策略情况**：我们在“死亡交叉”（卖出信号）出现时，以接近100元的价格卖出。随后在“黄金交叉”（买入信号）出现时，以80元的价格重新买入。在这个过程中，我们通过“高卖低买”实现了盈利，本金实际上增加了。

这种策略的核心在于利用均线交叉的信号，试图在下跌趋势开始前卖出，在上涨趋势启动前买入，从而规避部分下跌风险并捕捉上涨机会。它假设趋势会持续一段时间。

![](img/ddb16544b0ef6b94a5839e5abf0396ea_90.png)

---

![](img/ddb16544b0ef6b94a5839e5abf0396ea_92.png)

本节课中我们一起学习了量化交易中的一个基础策略——双均线交叉策略。我们从处理苹果公司股价数据开始，计算了短期和长期移动平均线，并通过可视化清晰地识别了“黄金交叉”和“死亡交叉”这两个关键买卖信号。最后，我们通过编程生成了交易信号，并讨论了该策略如何试图通过系统化的买卖决策来提升投资回报。这为后续构建更复杂的回测和交易系统奠定了基础。