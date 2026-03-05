# Python金融量化分析：13：买点与卖点可视化分析 📈

![](img/27dcefcdd4da2afeb803bf225280f396_0.png)

![](img/27dcefcdd4da2afeb803bf225280f396_2.png)

在本节课中，我们将学习如何利用双移动平均线策略，对股票数据进行可视化分析，并识别潜在的买入和卖出信号。我们将使用苹果公司的股价数据作为示例，通过计算短期和长期移动平均线，并标记它们的交叉点，来直观地展示交易策略的核心逻辑。

![](img/27dcefcdd4da2afeb803bf225280f396_4.png)

![](img/27dcefcdd4da2afeb803bf225280f396_6.png)

---

![](img/27dcefcdd4da2afeb803bf225280f396_8.png)

## 第一步：导入工具包与读取数据

![](img/27dcefcdd4da2afeb803bf225280f396_10.png)

首先，我们需要导入必要的Python库并加载数据。我们将使用`pandas`进行数据处理，`matplotlib`进行可视化。

![](img/27dcefcdd4da2afeb803bf225280f396_12.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/27dcefcdd4da2afeb803bf225280f396_14.png)

接下来，读取包含多只股票历史价格的数据文件。我们将第一列（日期）设置为索引，并解析日期格式。

![](img/27dcefcdd4da2afeb803bf225280f396_16.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/27dcefcdd4da2afeb803bf225280f396_18.png)

![](img/27dcefcdd4da2afeb803bf225280f396_20.png)

数据加载完成后，我们选择苹果公司（AAPL）的股价数据作为本次分析的示例。

![](img/27dcefcdd4da2afeb803bf225280f396_22.png)

```python
# 选择苹果公司的股价列
apple_data = data[['AAPL']].copy()
```

![](img/27dcefcdd4da2afeb803bf225280f396_24.png)

---

![](img/27dcefcdd4da2afeb803bf225280f396_26.png)

## 第二步：数据预处理

![](img/27dcefcdd4da2afeb803bf225280f396_28.png)

在进行分析之前，必须处理数据中的缺失值，以确保后续计算的准确性。

![](img/27dcefcdd4da2afeb803bf225280f396_30.png)

![](img/27dcefcdd4da2afeb803bf225280f396_32.png)

以下是处理缺失值的步骤：

1.  使用`dropna()`方法移除包含缺失值的行。
2.  查看处理后的数据前几行，确认操作成功。

![](img/27dcefcdd4da2afeb803bf225280f396_34.png)

```python
# 移除缺失值
apple_data = apple_data.dropna()
# 查看数据
print(apple_data.head())
```

---

## 第三步：计算移动平均线

![](img/27dcefcdd4da2afeb803bf225280f396_36.png)

上一节我们完成了数据清洗，本节中我们来看看如何计算关键的指标——移动平均线。

![](img/27dcefcdd4da2afeb803bf225280f396_38.png)

移动平均线是技术分析中常用的工具，用于平滑价格数据并识别趋势。我们将计算两个不同周期的移动平均线：短期和长期。

![](img/27dcefcdd4da2afeb803bf225280f396_40.png)

首先，定义短期和长期的窗口大小。由于我们的数据集时间跨度较长（约7-8年），为了在图表中更清晰地观察趋势，我们将窗口设置得比通常的5日和20日更大一些。

![](img/27dcefcdd4da2afeb803bf225280f396_42.png)

![](img/27dcefcdd4da2afeb803bf225280f396_44.png)

```python
# 定义短期和长期窗口大小
short_window = 40
long_window = 100
```

![](img/27dcefcdd4da2afeb803bf225280f396_46.png)

接下来，分别计算短期简单移动平均（SMA）和长期简单移动平均。

![](img/27dcefcdd4da2afeb803bf225280f396_48.png)

![](img/27dcefcdd4da2afeb803bf225280f396_50.png)

以下是计算移动平均线的步骤：

![](img/27dcefcdd4da2afeb803bf225280f396_52.png)

1.  对苹果股价列使用`rolling()`方法创建滑动窗口。
2.  对每个窗口计算均值（`mean()`）。
3.  将计算结果作为新列添加到数据框中。

![](img/27dcefcdd4da2afeb803bf225280f396_54.png)

```python
# 计算短期移动平均线
apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()
# 计算长期移动平均线
apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()
```

![](img/27dcefcdd4da2afeb803bf225280f396_56.png)

![](img/27dcefcdd4da2afeb803bf225280f396_58.png)

计算完成后，再次移除因窗口计算而产生的前期缺失值，并查看数据尾部以确认计算正确。

![](img/27dcefcdd4da2afeb803bf225280f396_60.png)

```python
apple_data = apple_data.dropna()
print(apple_data.tail())
```

---

## 第四步：可视化移动平均线与交叉点

![](img/27dcefcdd4da2afeb803bf225280f396_62.png)

现在我们已经有了股价和两条移动平均线，可以通过绘图来直观地观察它们的关系。

![](img/27dcefcdd4da2afeb803bf225280f396_64.png)

![](img/27dcefcdd4da2afeb803bf225280f396_66.png)

使用`matplotlib`绘制股价和两条移动平均线的走势图。

![](img/27dcefcdd4da2afeb803bf225280f396_68.png)

```python
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

![](img/27dcefcdd4da2afeb803bf225280f396_70.png)

![](img/27dcefcdd4da2afeb803bf225280f396_72.png)

在生成的图表中，重点关注短期均线（红色）与长期均线（绿色）的交叉点：
*   **黄金交叉**：短期均线从下向上穿越长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿越长期均线，通常被视为**卖出信号**。

---

![](img/27dcefcdd4da2afeb803bf225280f396_74.png)

## 第五步：标记买卖信号位置

上一节我们在图上观察了交叉点，本节我们将通过编程方式精确标记这些信号的位置。

![](img/27dcefcdd4da2afeb803bf225280f396_76.png)

首先，创建一个标志列（`position`），用于记录短期均线是否高于长期均线。我们使用`numpy`的`where`函数来实现条件赋值。

![](img/27dcefcdd4da2afeb803bf225280f396_78.png)

```python
import numpy as np
# 标记短期均线是否高于长期均线：1为是，0为否
apple_data['position'] = np.where(apple_data['SMA_short'] > apple_data['SMA_long'], 1, 0)
```

![](img/27dcefcdd4da2afeb803bf225280f396_80.png)

这个`position`列的变化点（从0变1或从1变0）就对应了移动平均线的交叉点，即潜在的买卖信号。

为了更直观，我们可以在同一张图上绘制股价、移动平均线和买卖信号标志。

```python
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(14, 10), sharex=True)

![](img/27dcefcdd4da2afeb803bf225280f396_82.png)

![](img/27dcefcdd4da2afeb803bf225280f396_84.png)

# 第一个子图：价格与均线
ax1.plot(apple_data['AAPL'], label='Price', alpha=0.7)
ax1.plot(apple_data['SMA_short'], label=f'SMA {short_window}', linewidth=1.5)
ax1.plot(apple_data['SMA_long'], label=f'SMA {long_window}', linewidth=1.5)
ax1.set_ylabel('Price')
ax1.legend()
ax1.set_title('Price and Moving Averages')

![](img/27dcefcdd4da2afeb803bf225280f396_86.png)

# 第二个子图：买卖信号位置
ax2.plot(apple_data['position'], label='Trading Signal (1=Buy, 0=Sell)', color='purple', drawstyle='steps-post', linewidth=2)
ax2.set_ylabel('Signal')
ax2.set_xlabel('Date')
ax2.legend(loc='upper left')
ax2.set_ylim(-0.1, 1.1)

plt.tight_layout()
plt.show()
```

---

## 第六步：理解策略逻辑

通过上述分析，我们构建了一个基于双移动平均线的简单交易策略逻辑：
1.  当`position`从0变为1（黄金交叉）时，视为**买入信号**。
2.  当`position`从1变为0（死亡交叉）时，视为**卖出信号**。

这个策略的核心思想是“趋势跟踪”。它假设一旦短期趋势（短期均线）强于长期趋势（长期均线），上涨趋势可能持续，反之亦然。虽然在实际应用中需要考虑交易成本、滞后性等因素，但该策略清晰地展示了如何将数学计算转化为具体的交易指令。

![](img/27dcefcdd4da2afeb803bf225280f396_88.png)

---

![](img/27dcefcdd4da2afeb803bf225280f396_90.png)

本节课中我们一起学习了金融量化分析中的一个基础而重要的环节：利用双移动平均线进行买卖点的可视化与标记。我们完成了从数据加载、预处理、指标计算到结果可视化的完整流程，并理解了交叉点所代表的交易信号含义。这为后续进行更复杂的策略回测和绩效分析奠定了坚实的基础。