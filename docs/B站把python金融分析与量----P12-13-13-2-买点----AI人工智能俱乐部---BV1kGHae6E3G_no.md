# Python金融量化+股票交易：P12：13.13.2-买点与卖点可视化分析 📈

![](img/ae39b333fa402f920d4f8b356c6bb31e_0.png)

在本节课中，我们将学习如何使用Python进行股票数据的可视化分析，特别是通过计算短期和长期移动平均线来识别潜在的买入和卖出信号。我们将以苹果公司的股票数据为例，一步步完成数据预处理、指标计算和结果可视化。

![](img/ae39b333fa402f920d4f8b356c6bb31e_2.png)

## 概述

![](img/ae39b333fa402f920d4f8b356c6bb31e_3.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_4.png)

我们将从导入必要的工具包和读取数据开始，然后对数据进行预处理，包括处理缺失值。接着，我们会计算短期和长期移动平均线，并利用它们之间的交叉关系来标记潜在的买卖点。最后，我们会将分析结果可视化，直观地展示这些信号。

![](img/ae39b333fa402f920d4f8b356c6bb31e_6.png)

---

![](img/ae39b333fa402f920d4f8b356c6bb31e_7.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_9.png)

## 第一步：导入工具包与读取数据

首先，我们需要导入进行数据分析所必需的Python库，并加载我们的股票数据集。

![](img/ae39b333fa402f920d4f8b356c6bb31e_11.png)

以下是所需的代码：

![](img/ae39b333fa402f920d4f8b356c6bb31e_12.png)

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt
```

![](img/ae39b333fa402f920d4f8b356c6bb31e_14.png)

读取数据文件，并指定第一列（日期）作为索引，同时解析日期格式。

![](img/ae39b333fa402f920d4f8b356c6bb31e_15.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_16.png)

```python
data = pd.read_csv('data.csv', index_col=0, parse_dates=True)
```

![](img/ae39b333fa402f920d4f8b356c6bb31e_17.png)

数据加载完成后，我们选择苹果公司（AAPL）的股票价格作为本次分析的对象。

![](img/ae39b333fa402f920d4f8b356c6bb31e_19.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_21.png)

---

## 第二步：数据预处理

在开始分析之前，我们需要对数据进行清洗，以确保分析的准确性。一个常见的步骤是处理数据中的缺失值。

我们提取苹果公司的股价列，并删除其中的缺失值。

![](img/ae39b333fa402f920d4f8b356c6bb31e_23.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_24.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_25.png)

```python
# 选择苹果公司的股价列
apple_data = data[['AAPL']].copy()
# 删除缺失值
apple_data = apple_data.dropna()
# 查看前几行数据
print(apple_data.head())
```

经过这一步，我们得到了一个干净、只包含苹果公司股价的数据集，为后续计算做好了准备。

![](img/ae39b333fa402f920d4f8b356c6bb31e_27.png)

---

## 第三步：计算移动平均线

![](img/ae39b333fa402f920d4f8b356c6bb31e_29.png)

移动平均线是技术分析中常用的指标，用于平滑价格数据并识别趋势。我们将计算短期和长期两条移动平均线。

首先，定义短期和长期的时间窗口。考虑到我们的数据跨度较长（约7-8年），为了在图表中更清晰地观察交叉点，我们将窗口设置得比通常稍大一些。

![](img/ae39b333fa402f920d4f8b356c6bb31e_31.png)

```python
# 定义短期和长期窗口
short_window = 42
long_window = 252
```

![](img/ae39b333fa402f920d4f8b356c6bb31e_32.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_33.png)

接下来，使用`rolling`方法计算移动平均线，并将结果作为新列添加到数据框中。

![](img/ae39b333fa402f920d4f8b356c6bb31e_34.png)

```python
# 计算短期移动平均线
apple_data['SMA_short'] = apple_data['AAPL'].rolling(window=short_window).mean()
# 计算长期移动平均线
apple_data['SMA_long'] = apple_data['AAPL'].rolling(window=long_window).mean()
```

计算完成后，数据的前几行会因窗口计算而包含一些缺失值，这是正常现象。我们可以查看一下数据。

```python
print(apple_data.head())
```

![](img/ae39b333fa402f920d4f8b356c6bb31e_36.png)

---

![](img/ae39b333fa402f920d4f8b356c6bb31e_37.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_38.png)

## 第四步：可视化移动平均线

![](img/ae39b333fa402f920d4f8b356c6bb31e_39.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_40.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_41.png)

为了直观地观察短期和长期均线的关系，我们可以将它们与原始股价一起绘制在图表上。

![](img/ae39b333fa402f920d4f8b356c6bb31e_42.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_43.png)

以下是绘制图表的代码：

```python
# 绘制股价与移动平均线
plt.figure(figsize=(14, 7))
plt.plot(apple_data['AAPL'], label='AAPL Price', alpha=0.5)
plt.plot(apple_data['SMA_short'], label=f'Short SMA ({short_window} days)', color='red')
plt.plot(apple_data['SMA_long'], label=f'Long SMA ({long_window} days)', color='green')
plt.title('AAPL Stock Price with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price')
plt.legend()
plt.show()
```

在生成的图表中，我们可以寻找短期均线（红色）与长期均线（绿色）的交叉点：
*   **黄金交叉**：短期均线从下向上穿过长期均线，通常被视为**买入信号**。
*   **死亡交叉**：短期均线从上向下穿过长期均线，通常被视为**卖出信号**。

---

![](img/ae39b333fa402f920d4f8b356c6bb31e_45.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_46.png)

## 第五步：标记买卖信号

![](img/ae39b333fa402f920d4f8b356c6bb31e_47.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_48.png)

为了量化这些交叉点，我们需要创建一个标志位来记录短期均线是否高于长期均线。

![](img/ae39b333fa402f920d4f8b356c6bb31e_49.png)

首先，删除在计算移动平均线时产生的缺失值，以确保数据连贯。

```python
# 删除因计算移动平均线产生的缺失值
apple_data = apple_data.dropna()
```

![](img/ae39b333fa402f920d4f8b356c6bb31e_51.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_52.png)

然后，使用`np.where`函数创建标志列。当短期均线高于长期均线时，标记为1（可能持有或考虑买入）；否则标记为0（可能卖出或观望）。

```python
# 创建位置标志位
apple_data['Position'] = np.where(apple_data['SMA_short'] > apple_data['SMA_long'], 1, 0)
# 查看结果
print(apple_data[['AAPL', 'SMA_short', 'SMA_long', 'Position']].head())
```

![](img/ae39b333fa402f920d4f8b356c6bb31e_54.png)

我们也可以将这个标志位添加到之前的图表中进行双重展示。

![](img/ae39b333fa402f920d4f8b356c6bb31e_56.png)

```python
# 绘制股价、均线及买卖信号
fig, ax1 = plt.subplots(figsize=(14, 7))

![](img/ae39b333fa402f920d4f8b356c6bb31e_57.png)

ax1.plot(apple_data['AAPL'], label='AAPL Price', color='blue', alpha=0.6)
ax1.plot(apple_data['SMA_short'], label=f'Short SMA', color='red')
ax1.plot(apple_data['SMA_long'], label=f'Long SMA', color='green')
ax1.set_ylabel('Price')
ax1.legend(loc='upper left')

![](img/ae39b333fa402f920d4f8b356c6bb31e_59.png)

# 创建第二个Y轴来显示买卖信号
ax2 = ax1.twinx()
ax2.plot(apple_data['Position'], label='Trading Signal (1=Buy/持有, 0=Sell/观望)', color='orange', alpha=0.3)
ax2.set_ylabel('Signal')
ax2.legend(loc='upper right')

plt.title('AAPL Stock Price with Moving Averages and Trading Signals')
plt.show()
```

---

## 第六步：策略思路探讨

![](img/ae39b333fa402f920d4f8b356c6bb31e_61.png)

![](img/ae39b333fa402f920d4f8b356c6bb31e_62.png)

通过上述分析，我们能够识别出理论上的买卖点。这引出了一个重要的交易策略思路：**双均线交叉策略**。

![](img/ae39b333fa402f920d4f8b356c6bb31e_63.png)

*   **无策略情况**：如果投资者在期初买入并一直持有到期末，其收益完全取决于股价的最终涨跌。
*   **双均线策略**：根据我们标记的信号，理论上可以在“死亡交叉”（卖出信号）时卖出股票，在“黄金交叉”（买入信号）时买入股票。这样做的目标是规避部分下跌风险，并在上涨趋势中保持持仓，从而**提升潜在收益**。

例如，假设股价从100元跌至80元：
*   **无策略**：亏损20元。
*   **有策略**：如果在下跌开始时的“死亡交叉”点（例如95元）卖出，然后在低点“黄金交叉”点（例如85元）买回，那么反而实现了低买高卖，获得了盈利。

本节课我们完成了可视化分析的基础工作，为后续回测和评估该策略的实战效果打下了基础。

---

## 总结

![](img/ae39b333fa402f920d4f8b356c6bb31e_65.png)

本节课中我们一起学习了股票数据可视化分析的关键步骤：
1.  **数据准备**：导入库、读取数据并进行预处理（处理缺失值）。
2.  **指标计算**：计算了短期和长期移动平均线（`SMA`）。
3.  **可视化**：绘制了股价与双均线图，直观展示趋势。
4.  **信号标记**：通过比较双均线，创建了代表潜在买卖信号的标志位。
5.  **策略启发**：探讨了如何利用双均线交叉点来构建一个简单的趋势跟踪交易策略。

![](img/ae39b333fa402f920d4f8b356c6bb31e_67.png)

通过本课的学习，你已经掌握了使用Python进行基础技术分析和可视化的重要方法，这是迈向量化交易策略开发的第一步。