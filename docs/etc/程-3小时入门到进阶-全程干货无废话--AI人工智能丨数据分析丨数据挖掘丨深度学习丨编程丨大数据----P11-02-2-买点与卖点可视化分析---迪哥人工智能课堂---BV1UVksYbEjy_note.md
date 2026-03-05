# Python金融分析与量化交易实战教程：P11：02-2-买点与卖点可视化分析 📈

![](img/5ece8a114b1c986bc149d3fa7f733bf6_0.png)

![](img/5ece8a114b1c986bc149d3fa7f733bf6_2.png)

在本节课中，我们将学习如何利用Python进行金融数据分析，具体目标是实现一个基于双移动平均线（MA）策略的买点与卖点可视化分析。我们将从数据预处理开始，计算短期和长期移动平均线，识别交叉点，并最终通过可视化图表清晰地展示交易信号。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_4.png)

---

![](img/5ece8a114b1c986bc149d3fa7f733bf6_6.png)

![](img/5ece8a114b1c986bc149d3fa7f733bf6_8.png)

## 数据准备与预处理

![](img/5ece8a114b1c986bc149d3fa7f733bf6_10.png)

首先，我们需要导入必要的工具包并加载数据。我们将使用`pandas`进行数据处理，`matplotlib`进行可视化。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/5ece8a114b1c986bc149d3fa7f733bf6_14.png)

接下来，我们读取包含股票价格的历史数据文件。数据的第一列是时间，我们将把它设置为索引。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_16.png)

![](img/5ece8a114b1c986bc149d3fa7f733bf6_18.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/5ece8a114b1c986bc149d3fa7f733bf6_20.png)

数据集中包含多只股票的信息。在本教程中，我们选择苹果公司的股票作为分析示例。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_22.png)

![](img/5ece8a114b1c986bc149d3fa7f733bf6_24.png)

```python
# 选择苹果公司的股票数据列
apple_data = data[['AAPL']]
```

![](img/5ece8a114b1c986bc149d3fa7f733bf6_26.png)

数据中可能存在缺失值，这会影响后续移动平均线的计算。因此，预处理的第一步是删除所有缺失值。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_28.png)

```python
# 删除缺失值
apple_data = apple_data.dropna()
```

![](img/5ece8a114b1c986bc149d3fa7f733bf6_30.png)

现在，我们得到了一个干净、只包含苹果公司股价的数据集。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_32.png)

---

![](img/5ece8a114b1c986bc149d3fa7f733bf6_34.png)

## 计算移动平均线

上一节我们准备好了数据，本节中我们来看看如何计算关键的移动平均线指标。

移动平均线是分析股票趋势的重要工具。短期移动平均线对价格变化更敏感，长期移动平均线则反映更稳定的趋势。两者的交叉点常被视作买卖信号。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_36.png)

以下是计算移动平均线的步骤：

![](img/5ece8a114b1c986bc149d3fa7f733bf6_38.png)

1.  **定义窗口期**：我们需要指定短期和长期移动平均线的计算天数。考虑到我们的数据跨度有7-8年，为了在图表中更清晰地观察，我们将短期窗口设为42天，长期窗口设为252天。
    ```python
    short_window = 42
    long_window = 252
    ```
2.  **计算移动平均线**：使用`rolling`方法创建滑动窗口，并计算窗口内收盘价的均值。
    ```python
    # 计算短期移动平均线
    apple_data['SMA_Short'] = apple_data['AAPL'].rolling(window=short_window).mean()
    # 计算长期移动平均线
    apple_data['SMA_Long'] = apple_data['AAPL'].rolling(window=long_window).mean()
    ```

![](img/5ece8a114b1c986bc149d3fa7f733bf6_40.png)

![](img/5ece8a114b1c986bc149d3fa7f733bf6_42.png)

计算完成后，数据的前几行会因为窗口不足而出现缺失值，这是正常现象。我们可以查看数据尾部来确认计算成功。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_44.png)

---

![](img/5ece8a114b1c986bc149d3fa7f733bf6_46.png)

![](img/5ece8a114b1c986bc149d3fa7f733bf6_48.png)

## 识别买卖信号

![](img/5ece8a114b1c986bc149d3fa7f733bf6_50.png)

计算完移动平均线后，下一步是识别它们之间的交叉点，这些交叉点构成了我们的交易信号。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_52.png)

![](img/5ece8a114b1c986bc149d3fa7f733bf6_54.png)

**黄金交叉**：当短期均线从下方上穿长期均线时，通常被视为**买入信号**。
**死亡交叉**：当短期均线从上方下穿长期均线时，通常被视为**卖出信号**。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_56.png)

为了量化这种关系，我们创建一个标志位（`Position`）。当短期均线高于长期均线时，标志位为1；否则为0。在创建标志位前，需要先剔除移动平均线计算产生的缺失值。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_58.png)

```python
# 剔除因计算移动平均线产生的缺失值
apple_data = apple_data.dropna()

# 创建标志位：短期均线 > 长期均线 则为1，否则为0
apple_data['Position'] = np.where(apple_data['SMA_Short'] > apple_data['SMA_Long'], 1, 0)
```

现在，我们的数据集中包含了原始股价、两条移动平均线以及买卖标志位。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_60.png)

![](img/5ece8a114b1c986bc149d3fa7f733bf6_62.png)

---

![](img/5ece8a114b1c986bc149d3fa7f733bf6_64.png)

## 结果可视化

![](img/5ece8a114b1c986bc149d3fa7f733bf6_66.png)

![](img/5ece8a114b1c986bc149d3fa7f733bf6_68.png)

有了清晰的数据和信号，我们可以通过图表直观地展示分析结果。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_70.png)

以下是绘制股价与移动平均线图的代码：

```python
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='Apple Stock Price', alpha=0.5)
plt.plot(apple_data['SMA_Short'], label=f'{short_window}-Day SMA', color='red')
plt.plot(apple_data['SMA_Long'], label=f'{long_window}-Day SMA', color='green')
plt.title('Apple Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

![](img/5ece8a114b1c986bc149d3fa7f733bf6_72.png)

在生成的图表中，我们可以观察到红色短期均线与绿色长期均线的多个交叉点。上穿点（黄金交叉）提示潜在的买入时机，下穿点（死亡交叉）提示潜在的卖出时机。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_74.png)

为了更清晰地展示买卖信号，我们可以将标志位也绘制在次坐标轴上：

![](img/5ece8a114b1c986bc149d3fa7f733bf6_76.png)

```python
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/5ece8a114b1c986bc149d3fa7f733bf6_78.png)

ax1.plot(apple_data['AAPL'], label='Price', color='blue')
ax1.plot(apple_data['SMA_Short'], label='Short MA', color='red')
ax1.plot(apple_data['SMA_Long'], label='Long MA', color='green')
ax1.set_ylabel('Price')
ax1.legend(loc='upper left')

ax2 = ax1.twinx()
ax2.plot(apple_data['Position'], label='Buy/Sell Signal', color='orange', linestyle='--')
ax2.set_ylabel('Signal (1=Buy, 0=Sell)')
ax2.legend(loc='upper right')

![](img/5ece8a114b1c986bc149d3fa7f733bf6_80.png)

plt.title('Stock Price, Moving Averages, and Trading Signals')
plt.show()
```

![](img/5ece8a114b1c986bc149d3fa7f733bf6_82.png)

---

![](img/5ece8a114b1c986bc149d3fa7f733bf6_84.png)

## 策略逻辑与收益思考

通过可视化，我们清晰地看到了基于双均线的买卖点。现在我们来思考这个策略背后的逻辑。

假设我们有一笔初始资金。在“买入信号”出现时（标志位从0变为1），我们买入股票；在“卖出信号”出现时（标志位从1变为0），我们卖出股票。这样做的目标是**“高抛低吸”**。

*   **被动持有**：如果单纯买入并长期持有，收益完全取决于股价的最终涨跌。
*   **主动交易**：而按照我们的均线策略，我们试图在股价下跌趋势开始前卖出，在上涨趋势开始前买入。理论上，这可以让我们避免部分下跌损失，并抓住上涨机会，从而可能获得比单纯持有更高的收益。

当然，这是一个简化的理论模型。实际交易中还需考虑手续费、滑点、策略过拟合等问题。

![](img/5ece8a114b1c986bc149d3fa7f733bf6_86.png)

---

![](img/5ece8a114b1c986bc149d3fa7f733bf6_88.png)

本节课中我们一起学习了如何利用Python进行金融数据分析和策略可视化。我们从数据预处理开始，计算了短期和长期移动平均线，创建了买卖信号标志位，并最终通过图表直观展示了基于双均线交叉的交易策略。这个过程是构建量化交易策略的基础步骤。在后续课程中，我们可以在此基础上回测策略的历史表现，并优化参数。