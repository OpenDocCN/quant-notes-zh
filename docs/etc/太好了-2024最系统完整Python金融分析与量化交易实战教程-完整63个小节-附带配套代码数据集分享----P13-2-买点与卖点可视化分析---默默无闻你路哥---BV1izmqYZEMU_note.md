# Python金融分析与量化交易实战：P13：2-买点与卖点可视化分析 📈

![](img/933c31a1f2edc93ed1695991563c71e9_0.png)

在本节课中，我们将学习如何利用双移动平均线策略进行买点与卖点的可视化分析。我们将通过处理股票数据、计算移动平均线、识别交叉信号，并最终计算策略的回报率，来直观地理解这一经典量化交易策略。

---

![](img/933c31a1f2edc93ed1695991563c71e9_2.png)

![](img/933c31a1f2edc93ed1695991563c71e9_3.png)

![](img/933c31a1f2edc93ed1695991563c71e9_4.png)

## 第一步：导入工具包与读取数据 📊

首先，我们需要导入必要的Python库并加载数据。我们将使用`pandas`进行数据处理，`numpy`进行数值计算，以及`matplotlib`进行可视化。

![](img/933c31a1f2edc93ed1695991563c71e9_6.png)

![](img/933c31a1f2edc93ed1695991563c71e9_7.png)

以下是导入库和读取数据的代码：

![](img/933c31a1f2edc93ed1695991563c71e9_9.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# 读取数据，指定第一列为时间索引
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/933c31a1f2edc93ed1695991563c71e9_11.png)

数据读取完成后，我们选择苹果公司的股票价格作为分析示例。

![](img/933c31a1f2edc93ed1695991563c71e9_12.png)

---

![](img/933c31a1f2edc93ed1695991563c71e9_14.png)

## 第二步：数据预处理与选择股票 🍎

![](img/933c31a1f2edc93ed1695991563c71e9_15.png)

![](img/933c31a1f2edc93ed1695991563c71e9_16.png)

上一节我们导入了数据，本节中我们来看看如何选择特定股票并进行数据清洗。

![](img/933c31a1f2edc93ed1695991563c71e9_17.png)

首先，从数据集中提取苹果公司的股价数据。原始数据中可能存在缺失值，这会影响后续移动平均线的计算，因此我们需要先将其删除。

![](img/933c31a1f2edc93ed1695991563c71e9_19.png)

以下是具体步骤：

```python
# 选择苹果公司的股价列
apple_data = data[['AAPL']].copy()

# 删除缺失值
apple_data = apple_data.dropna()

# 查看前几行数据
print(apple_data.head())
```

![](img/933c31a1f2edc93ed1695991563c71e9_21.png)

![](img/933c31a1f2edc93ed1695991563c71e9_22.png)

执行上述代码后，我们得到了一个只包含苹果公司股价且无缺失值的`DataFrame`。

![](img/933c31a1f2edc93ed1695991563c71e9_23.png)

---

![](img/933c31a1f2edc93ed1695991563c71e9_25.png)

## 第三步：计算短期与长期移动平均线 📉

数据准备就绪后，接下来我们需要计算移动平均线。移动平均线是分析股价趋势的重要指标。

我们首先定义短期和长期的窗口大小。由于我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察交叉点，我们将短期窗口设为42天，长期窗口设为252天。

![](img/933c31a1f2edc93ed1695991563c71e9_27.png)

以下是计算移动平均线的代码：

```python
# 定义短期和长期窗口大小
short_window = 42
long_window = 252

![](img/933c31a1f2edc93ed1695991563c71e9_29.png)

# 计算短期移动平均线 (SMA)
apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()

![](img/933c31a1f2edc93ed1695991563c71e9_30.png)

![](img/933c31a1f2edc93ed1695991563c71e9_31.png)

# 计算长期移动平均线 (SMA)
apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()

![](img/933c31a1f2edc93ed1695991563c71e9_32.png)

# 查看计算结果
print(apple_data.head())
```

计算完成后，数据集中新增了两列，分别代表短期和长期的移动平均线。初始部分会出现`NaN`值，这是因为移动平均计算需要足够的数据点。

---

## 第四步：可视化移动平均线与交叉点 🔍

![](img/933c31a1f2edc93ed1695991563c71e9_34.png)

![](img/933c31a1f2edc93ed1695991563c71e9_35.png)

现在我们已经有了股价和两条移动平均线，可以通过绘图来直观地观察它们之间的关系，特别是交叉点。

![](img/933c31a1f2edc93ed1695991563c71e9_36.png)

![](img/933c31a1f2edc93ed1695991563c71e9_37.png)

以下是绘制图表的代码：

![](img/933c31a1f2edc93ed1695991563c71e9_38.png)

![](img/933c31a1f2edc93ed1695991563c71e9_39.png)

```python
# 绘制股价与移动平均线
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='AAPL Price', alpha=0.5)
plt.plot(apple_data['SMA_short'], label=f'SMA {short_window}', color='red')
plt.plot(apple_data['SMA_long'], label=f'SMA {long_window}', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/933c31a1f2edc93ed1695991563c71e9_40.png)

![](img/933c31a1f2edc93ed1695991563c71e9_41.png)

在生成的图表中，我们可以寻找短期均线（红色）与长期均线（绿色）的交叉点：
*   **黄金交叉**：短期均线从下向上穿过长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿过长期均线，通常被视为**卖出信号**。

---

## 第五步：标记买卖信号位置 🎯

仅仅通过看图识别交叉点效率较低，我们可以通过计算创建一个明确的买卖信号标志位。

![](img/933c31a1f2edc93ed1695991563c71e9_43.png)

首先，需要删除移动平均线计算产生的`NaN`值，以确保数据连贯。

![](img/933c31a1f2edc93ed1695991563c71e9_44.png)

![](img/933c31a1f2edc93ed1695991563c71e9_45.png)

```python
# 删除因计算移动平均线产生的缺失值
apple_data = apple_data.dropna()
```

![](img/933c31a1f2edc93ed1695991563c71e9_46.png)

![](img/933c31a1f2edc93ed1695991563c71e9_47.png)

接着，我们创建一个名为`Position`的列。当短期均线大于长期均线时，标记为1（持有或买入信号）；否则标记为0（不持有或卖出信号）。

```python
# 创建买卖信号标志位
apple_data['Position'] = np.where(apple_data['SMA_short'] > apple_data['SMA_long'], 1, 0)

# 查看标记结果
print(apple_data[['AAPL', 'SMA_short', 'SMA_long', 'Position']].head())
```

![](img/933c31a1f2edc93ed1695991563c71e9_49.png)

![](img/933c31a1f2edc93ed1695991563c71e9_50.png)

我们也可以将这个标志位添加到之前的图表中进行双重验证。

```python
# 在股价图上叠加买卖信号标志位
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

![](img/933c31a1f2edc93ed1695991563c71e9_52.png)

# 第一个子图：股价与均线
ax1.plot(apple_data['AAPL'], label='AAPL Price', alpha=0.5)
ax1.plot(apple_data['SMA_short'], label=f'SMA {short_window}', color='red')
ax1.plot(apple_data['SMA_long'], label=f'SMA {long_window}', color='green')
ax1.set_ylabel('Price')
ax1.legend()
ax1.set_title('Apple Stock Price with Moving Averages and Buy/Sell Signals')

# 第二个子图：买卖信号
ax2.fill_between(apple_data.index, 0, apple_data['Position'], color='grey', alpha=0.3, label='Position (1=Buy/Hold)')
ax2.set_ylabel('Position')
ax2.set_xlabel('Date')
ax2.legend()

![](img/933c31a1f2edc93ed1695991563c71e9_54.png)

![](img/933c31a1f2edc93ed1695991563c71e9_55.png)

plt.show()
```

---

![](img/933c31a1f2edc93ed1695991563c71e9_57.png)

## 第六步：计算并比较策略回报率 💹

最后，我们来量化这个双均线策略的效果。我们将比较两种情况的回报率：
1.  **买入并持有策略**：在期初买入股票并一直持有到期末。
2.  **双均线策略**：根据我们生成的买卖信号（`Position`）进行交易。

以下是计算回报率的代码：

```python
# 计算每日股价的百分比变化（收益率）
apple_data['Returns'] = apple_data['AAPL'].pct_change()

![](img/933c31a1f2edc93ed1695991563c71e9_59.png)

![](img/933c31a1f2edc93ed1695991563c71e9_60.png)

# 计算买入并持有策略的累计回报
apple_data['Buy_Hold_Cumulative'] = (1 + apple_data['Returns']).cumprod()

![](img/933c31a1f2edc93ed1695991563c71e9_61.png)

# 计算双均线策略的每日收益：当Position为1时（持有），获得市场收益；为0时（空仓），收益为0。
apple_data['Strategy_Returns'] = apple_data['Returns'] * apple_data['Position'].shift(1)  # 使用前一天的信号

# 计算双均线策略的累计回报
apple_data['Strategy_Cumulative'] = (1 + apple_data['Strategy_Returns']).cumprod()

# 绘制两种策略的累计回报对比图
plt.figure(figsize=(14, 7))
plt.plot(apple_data['Buy_Hold_Cumulative'], label='Buy and Hold Strategy', alpha=0.7)
plt.plot(apple_data['Strategy_Cumulative'], label='Dual Moving Average Strategy', alpha=0.7)
plt.title('Cumulative Returns: Buy & Hold vs. Dual MA Strategy')
plt.xlabel('Date')
plt.ylabel('Cumulative Returns (Growth of $1)')
plt.legend()
plt.grid(True)
plt.show()

# 打印最终回报
initial_investment = 1
final_buy_hold = apple_data['Buy_Hold_Cumulative'].iloc[-1]
final_strategy = apple_data['Strategy_Cumulative'].iloc[-1()

print(f"买入并持有策略：1美元最终变为 ${final_buy_hold:.2f}")
print(f"双均线交易策略：1美元最终变为 ${final_strategy:.2f}")
```

通过对比图表和最终数字，我们可以清晰地评估双均线策略相对于简单持有策略的表现优劣。理想情况下，该策略能在市场下跌时通过卖出信号规避部分损失，从而可能获得更高的风险调整后收益。

![](img/933c31a1f2edc93ed1695991563c71e9_63.png)

---

![](img/933c31a1f2edc93ed1695991563c71e9_65.png)

本节课中我们一起学习了双移动平均线策略的完整实现流程。我们从数据预处理开始，计算了短期和长期均线，并通过可视化识别了买卖点。接着，我们创建了明确的交易信号，并最终计算和比较了策略的回报率。这个过程展示了如何将一个经典的交易想法转化为可执行、可验证的量化策略代码。