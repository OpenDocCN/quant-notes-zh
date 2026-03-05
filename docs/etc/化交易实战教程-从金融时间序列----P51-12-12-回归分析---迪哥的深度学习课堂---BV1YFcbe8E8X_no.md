# Python金融分析与量化交易实战：P51：12-12-回归分析结果 📈

![](img/15509ea17db06befc86e73542e1b425b_0.png)

在本节课中，我们将学习如何评估回归模型在量化交易策略中的实际表现。我们将通过计算累计收益、对比不同策略的最终结果，并可视化其走势，来理解模型预测如何影响交易决策。

![](img/15509ea17db06befc86e73542e1b425b_2.png)

---

上一节我们介绍了如何构建回归模型来预测股票价格。本节中，我们来看看如何评估这些模型的实战效果。

首先，我们需要对预测结果进行累加操作，以模拟投资策略的累计收益。由于我们之前对收益率数据进行了对数处理，因此累加后需要进行指数还原。

![](img/15509ea17db06befc86e73542e1b425b_4.png)

以下是核心操作步骤：

1.  **提取指标**：从数据中提取原始收益率（`returns`）以及两个回归模型的预测结果。
2.  **计算累计收益**：对收益率序列进行累加（`cumsum`），以模拟资金随时间的变化。
3.  **还原数据**：由于之前使用了对数收益率，累加后需使用指数函数（`np.exp`）还原为实际收益率倍数。

```python
# 提取指标：原始收益率和两个模型的预测收益率
returns = data['returns']
pred_returns_1 = data['pred_1']
pred_returns_2 = data['pred_2']

# 计算累计收益并还原
cumulative_returns = np.exp(returns.cumsum())
cumulative_pred_1 = np.exp(pred_returns_1.cumsum())
cumulative_pred_2 = np.exp(pred_returns_2.cumsum())
```

执行上述代码后，我们得到了三个最终值。这些值代表：如果初始投入1元钱，在“什么都不做”、“采用策略一”、“采用策略二”三种情况下，最终的资金总额。数值大于1表示盈利，小于1表示亏损。

结果显示，原始走势（`returns`）导致资金缩水至约0.8元。策略一（第一个回归方程）的结果约为0.94元，虽然也亏损，但比什么都不做亏损得少。策略二（第二个回归方程）的结果约为1.3元，实现了盈利。

---

![](img/15509ea17db06befc86e73542e1b425b_6.png)

接下来，我们分析模型预测的准确性。我们可以将模型预测的涨跌方向与实际涨跌方向进行比较。

![](img/15509ea17db06befc86e73542e1b425b_8.png)

以下是准确率分析步骤：

![](img/15509ea17db06befc86e73542e1b425b_10.png)

1.  **获取实际方向**：数据中`direction`列记录了实际的涨跌情况。
2.  **比较预测方向**：将模型预测的收益率（`pred_1`, `pred_2`）转换为涨跌方向（大于0为涨，小于0为跌）。
3.  **计算正确率**：统计预测方向与实际方向一致的次数。

![](img/15509ea17db06befc86e73542e1b425b_12.png)

```python
# 将预测收益率转换为涨跌方向 (1为涨，0为跌)
pred_direction_1 = (data['pred_1'] > 0).astype(int)
pred_direction_2 = (data['pred_2'] > 0).astype(int)
actual_direction = data['direction']

![](img/15509ea17db06befc86e73542e1b425b_14.png)

![](img/15509ea17db06befc86e73542e1b425b_16.png)

# 计算预测正确的次数
correct_1 = (pred_direction_1 == actual_direction).value_counts()
correct_2 = (pred_direction_2 == actual_direction).value_counts()
```

![](img/15509ea17db06befc86e73542e1b425b_18.png)

分析结果显示，第一个模型预测正确1042次，错误1093次。第二个模型预测正确1096次，错误1039次。第二个模型的正确率略高，这与其最终实现盈利的结果相符。

---

最后，为了更直观地展示策略在整个时间周期内的表现，我们将绘制三种策略的资金曲线走势图。

![](img/15509ea17db06befc86e73542e1b425b_20.png)

![](img/15509ea17db06befc86e73542e1b425b_22.png)

绘制走势图的关键在于使用`cumsum`函数，它能生成每个时间点的累计收益，而不仅仅是最终结果。

![](img/15509ea17db06befc86e73542e1b425b_24.png)

以下是可视化步骤：

1.  **计算累计序列**：对三个收益率序列分别进行`cumsum`操作。
2.  **还原数据**：同样，对累计序列进行指数还原。
3.  **绘制图表**：使用`matplotlib`将三条资金曲线绘制在同一张图中进行对比。

![](img/15509ea17db06befc86e73542e1b425b_26.png)

```python
import matplotlib.pyplot as plt

# 计算并还原累计收益序列
cum_returns = np.exp(returns.cumsum())
cum_pred_1 = np.exp(pred_returns_1.cumsum())
cum_pred_2 = np.exp(pred_returns_2.cumsum())

![](img/15509ea17db06befc86e73542e1b425b_28.png)

# 绘制走势图
plt.figure(figsize=(16, 8))
plt.plot(cum_returns, label='Actual Returns (Blue)', color='blue')
plt.plot(cum_pred_1, label='Strategy 1 (Green)', color='green')
plt.plot(cum_pred_2, label='Strategy 2 (Red)', color='red')
plt.title('Cumulative Returns of Different Strategies')
plt.xlabel('Time')
plt.ylabel('Cumulative Return (Multiple)')
plt.legend()
plt.grid(True)
plt.show()
```

![](img/15509ea17db06befc86e73542e1b425b_30.png)

从生成的图中可以清晰看到：蓝色曲线（原始走势）持续下跌；绿色曲线（策略一）虽然也下跌，但走势与蓝色曲线差异明显；红色曲线（策略二）在大部分时间里实现了增长，最终获得盈利。

![](img/15509ea17db06befc86e73542e1b425b_32.png)

![](img/15509ea17db06befc86e73542e1b425b_34.png)

---

![](img/15509ea17db06befc86e73542e1b425b_36.png)

![](img/15509ea17db06befc86e73542e1b425b_38.png)

本节课中我们一起学习了如何评估回归模型在量化交易中的应用效果。我们通过计算累计收益对比了不同策略的最终盈亏，通过方向预测分析了模型的准确性，并通过可视化直观展示了策略在整个时间序列上的表现。核心在于理解，回归预测本质上是一个预测问题，不同的数据标签（如连续收益率或涨跌方向）和模型策略会对最终的交易结果产生关键影响。本例展示了如何将最简单的回归方程引入交易策略构建的基本流程。