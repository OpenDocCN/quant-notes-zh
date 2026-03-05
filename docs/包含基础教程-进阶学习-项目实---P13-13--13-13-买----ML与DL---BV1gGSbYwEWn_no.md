# 量化交易教程：13：买点与卖点可视化分析 📈

![](img/3e9450172a1ee3d8592edbae1159716d_0.png)

![](img/3e9450172a1ee3d8592edbae1159716d_2.png)

在本节课中，我们将学习如何通过计算短期和长期移动平均线，来识别股票交易中的潜在买点与卖点，并进行可视化分析。我们将使用苹果公司的股票数据作为示例，通过Python代码实现数据处理、指标计算和结果绘图。

![](img/3e9450172a1ee3d8592edbae1159716d_4.png)

![](img/3e9450172a1ee3d8592edbae1159716d_6.png)

---

![](img/3e9450172a1ee3d8592edbae1159716d_8.png)

## 数据准备与预处理

![](img/3e9450172a1ee3d8592edbae1159716d_10.png)

![](img/3e9450172a1ee3d8592edbae1159716d_12.png)

首先，我们需要导入必要的工具包并加载数据。

![](img/3e9450172a1ee3d8592edbae1159716d_14.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/3e9450172a1ee3d8592edbae1159716d_16.png)

以下是数据加载与预处理的步骤：

![](img/3e9450172a1ee3d8592edbae1159716d_18.png)

![](img/3e9450172a1ee3d8592edbae1159716d_20.png)

1.  读取包含股票数据的CSV文件。
2.  将第一列（时间）设置为索引，并解析为日期时间格式。
3.  选择苹果公司（AAPL）的股票价格数据列。
4.  删除数据中可能存在的缺失值，以确保后续计算的准确性。

![](img/3e9450172a1ee3d8592edbae1159716d_22.png)

![](img/3e9450172a1ee3d8592edbae1159716d_24.png)

```python
# 读取数据，指定第一列为时间索引
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)

![](img/3e9450172a1ee3d8592edbae1159716d_26.png)

# 选择苹果公司的股票价格列，并删除缺失值
apple_data = data[['AAPL']].dropna()
print(apple_data.head())
```

![](img/3e9450172a1ee3d8592edbae1159716d_28.png)

![](img/3e9450172a1ee3d8592edbae1159716d_30.png)

---

![](img/3e9450172a1ee3d8592edbae1159716d_32.png)

## 计算移动平均线

![](img/3e9450172a1ee3d8592edbae1159716d_34.png)

![](img/3e9450172a1ee3d8592edbae1159716d_36.png)

上一节我们完成了数据准备，本节中我们来看看如何计算关键的交易指标——移动平均线。

![](img/3e9450172a1ee3d8592edbae1159716d_38.png)

移动平均线是分析股票趋势的常用工具。短期均线对价格变化更敏感，长期均线则反映更稳定的趋势。当短期均线上穿长期均线时，可能形成“黄金交叉”，是潜在的买入信号；反之，下穿时则可能形成“死亡交叉”，是潜在的卖出信号。

![](img/3e9450172a1ee3d8592edbae1159716d_40.png)

以下是计算短期和长期简单移动平均线的步骤：

1.  定义短期和长期的窗口大小。根据数据量，我们分别设置为42天和252天。
2.  使用 `rolling` 和 `mean` 方法计算移动平均值。
3.  将计算结果作为新列添加到数据框中。

```python
# 定义短期和长期窗口
short_window = 42
long_window = 252

![](img/3e9450172a1ee3d8592edbae1159716d_42.png)

![](img/3e9450172a1ee3d8592edbae1159716d_44.png)

# 计算短期移动平均线
apple_data['SMA_Short'] = apple_data['AAPL'].rolling(window=short_window).mean()

![](img/3e9450172a1ee3d8592edbae1159716d_46.png)

# 计算长期移动平均线
apple_data['SMA_Long'] = apple_data['AAPL'].rolling(window=long_window).mean()

![](img/3e9450172a1ee3d8592edbae1159716d_48.png)

# 查看计算结果
print(apple_data.tail())
```

![](img/3e9450172a1ee3d8592edbae1159716d_50.png)

![](img/3e9450172a1ee3d8592edbae1159716d_52.png)

由于移动平均计算需要足够的窗口数据，因此结果的前几行会出现缺失值（NaN），这是正常现象。

![](img/3e9450172a1ee3d8592edbae1159716d_54.png)

![](img/3e9450172a1ee3d8592edbae1159716d_56.png)

---

![](img/3e9450172a1ee3d8592edbae1159716d_58.png)

## 可视化均线与交叉点

![](img/3e9450172a1ee3d8592edbae1159716d_60.png)

![](img/3e9450172a1ee3d8592edbae1159716d_62.png)

计算完移动平均线后，我们可以通过绘图直观地观察股价走势以及均线的交叉情况。

![](img/3e9450172a1ee3d8592edbae1159716d_64.png)

```python
# 绘制股价与移动平均线
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='AAPL Price', alpha=0.5)
plt.plot(apple_data['SMA_Short'], label=f'{short_window}-Day SMA', color='red')
plt.plot(apple_data['SMA_Long'], label=f'{long_window}-Day SMA', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/3e9450172a1ee3d8592edbae1159716d_66.png)

![](img/3e9450172a1ee3d8592edbae1159716d_68.png)

在生成的图表中，红色线代表短期均线，绿色线代表长期均线。两者的交叉点是需要关注的关键位置：
*   **黄金交叉**：短期均线从下方上穿长期均线（如图中绿色箭头所指），通常被视为买入信号。
*   **死亡交叉**：短期均线从上方下穿长期均线（如图中红色箭头所指），通常被视为卖出信号。

---

## 标记趋势与生成交易信号

![](img/3e9450172a1ee3d8592edbae1159716d_70.png)

![](img/3e9450172a1ee3d8592edbae1159716d_72.png)

为了量化这些交叉点，我们需要创建一个标志位来明确记录短期均线与长期均线的位置关系。

![](img/3e9450172a1ee3d8592edbae1159716d_74.png)

![](img/3e9450172a1ee3d8592edbae1159716d_76.png)

首先，我们需要删除在计算移动平均线时产生的缺失值，以确保数据连贯。

![](img/3e9450172a1ee3d8592edbae1159716d_78.png)

```python
# 删除因计算移动平均而产生的缺失值
apple_data.dropna(inplace=True)
```

![](img/3e9450172a1ee3d8592edbae1159716d_80.png)

接下来，我们创建一个新的列 `Position`。当短期均线高于长期均线时，标记为1，代表“持有”或“看涨”信号；反之则标记为0。

![](img/3e9450172a1ee3d8592edbae1159716d_82.png)

```python
# 生成交易信号标志位
apple_data['Position'] = np.where(apple_data['SMA_Short'] > apple_data['SMA_Long'], 1, 0)
print(apple_data.head())
```

![](img/3e9450172a1ee3d8592edbae1159716d_84.png)

我们也可以将这个“持仓”信号可视化，更清晰地看到策略在何时建议买入（持仓为1）或卖出（持仓为0）。

![](img/3e9450172a1ee3d8592edbae1159716d_86.png)

```python
# 绘制股价与持仓信号
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

![](img/3e9450172a1ee3d8592edbae1159716d_88.png)

# 第一个子图：股价与均线
ax1.plot(apple_data['AAPL'], label='Price', alpha=0.7)
ax1.plot(apple_data['SMA_Short'], label='Short SMA', color='red', linewidth=1)
ax1.plot(apple_data['SMA_Long'], label='Long SMA', color='green', linewidth=1)
ax1.set_ylabel('Price')
ax1.legend()
ax1.set_title('Price and Moving Averages')

# 第二个子图：持仓信号
ax2.fill_between(apple_data.index, 0, apple_data['Position'], color='grey', alpha=0.3, step='pre')
ax2.set_ylabel('Position (0=Sell, 1=Buy)')
ax2.set_xlabel('Date')
ax2.set_title('Trading Signal')
ax2.set_yticks([0, 1])

plt.tight_layout()
plt.show()
```

![](img/3e9450172a1ee3d8592edbae1159716d_90.png)

![](img/3e9450172a1ee3d8592edbae1159716d_92.png)

---

![](img/3e9450172a1ee3d8592edbae1159716d_94.png)

## 策略逻辑与收益思考

![](img/3e9450172a1ee3d8592edbae1159716d_96.png)

通过以上步骤，我们已经能够系统地识别出基于双均线的买卖点。这引出了一个核心的交易策略思想：**通过择时交易来提升收益**。

假设我们有两种投资方式：
1.  **买入并持有**：在期初买入股票并一直持有到期末，收益完全取决于股价的最终涨跌。
2.  **双均线策略**：根据我们生成的 `Position` 信号进行操作。当信号为1时买入（或持有），当信号变为0时卖出（或空仓）。

双均线策略的目标是在股价下跌趋势开始前（死亡交叉）卖出以规避损失，在上涨趋势开始时（黄金交叉）买入以获取收益。理想情况下，这种“高抛低吸”的操作能够超越简单的买入持有策略，获得更高的累计回报。

在接下来的课程中，我们将基于这个 `Position` 信号，具体计算并比较这两种策略的实际收益表现。

![](img/3e9450172a1ee3d8592edbae1159716d_98.png)

---

![](img/3e9450172a1ee3d8592edbae1159716d_100.png)

本节课中我们一起学习了如何利用移动平均线进行交易信号分析。我们完成了从数据预处理、计算双均线、可视化交叉点到生成量化交易信号的完整流程。理解并识别这些基本的图表形态和指标信号，是构建自动化量化交易策略的重要基础。