# Python金融时间序列分析与量化交易实战教程：P63：62.回归分析结果 📈

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_0.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_2.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_4.png)

在本节课中，我们将学习如何评估和可视化基于线性回归模型构建的两种交易策略的最终表现。我们将通过计算累计收益、对比预测准确率以及绘制策略走势图，来直观地理解模型的实际效果。

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_6.png)

上一节我们介绍了如何构建回归模型并进行预测，本节中我们来看看如何评估这些预测结果的实际价值。

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_8.png)

## 累计收益计算

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_10.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_12.png)

首先，我们需要计算三种不同情况下的最终累计收益。这三种情况分别是：原始收益率（什么都不做）、基于第一种回归方程的策略收益、以及基于第二种回归方程的策略收益。

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_14.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_16.png)

由于我们在数据预处理阶段对收益率数据进行了对数变换，因此计算累计收益需要先进行累加，再进行指数还原。

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_18.png)

以下是计算累计收益的步骤：

1.  从数据中提取原始收益率列 `returns`，以及两个回归模型的预测结果列 `regression_1` 和 `regression_2`。
2.  对这三列数据分别执行累加操作 `cumsum()`，将每日的对数收益率累加起来。
3.  使用指数函数 `np.exp()` 将累加后的对数收益还原为实际的累计收益倍数。

```python
# 计算累计收益
cumulative_returns = data[['returns', 'regression_1', 'regression_2']].cumsum().apply(np.exp)
print(cumulative_returns.iloc[-1]) # 查看最终一天的累计收益
```

执行上述代码后，我们可以得到三个数值。例如，`returns` 的最终值可能为 0.8，这表示初始的 1 元钱最终变成了 0.8 元，即亏损了 20%。`regression_1` 的值可能为 0.94，表示亏损减少至 6%。而 `regression_2` 的值可能为 1.3，表示实现了 30% 的盈利。这表明第二种策略通过预测买卖点，取得了正向的收益。

## 预测准确率分析

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_20.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_22.png)

接下来，我们分析模型的预测方向（涨或跌）与实际市场方向的一致性，以评估模型的预测准确率。

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_24.png)

我们的数据中有一列 `direction`，记录了每日实际的涨跌情况（例如，1 代表上涨，0 代表下跌）。我们可以将模型的预测值（`regression_1` 和 `regression_2`）转换为涨跌方向，然后与实际方向进行比较。

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_26.png)

以下是计算预测准确率的步骤：

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_28.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_30.png)

1.  将回归模型的连续预测值转换为二元的涨跌方向标签。例如，预测值大于 0 则视为预测上涨，否则视为预测下跌。
2.  将预测方向与实际方向 `data[‘direction’]` 进行比较，得到一个布尔序列（True 表示预测正确，False 表示预测错误）。
3.  使用 `value_counts()` 函数统计正确和错误的次数。

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_32.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_34.png)

```python
# 计算第一个回归模型的预测准确率
pred_direction_1 = (data[‘regression_1’] > 0)
accuracy_1 = (pred_direction_1 == data[‘direction’]).value_counts()
print(“模型1预测准确率统计：”)
print(accuracy_1)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_36.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_38.png)

# 计算第二个回归模型的预测准确率
pred_direction_2 = (data[‘regression_2’] > 0)
accuracy_2 = (pred_direction_2 == data[‘direction’]).value_counts()
print(“\n模型2预测准确率统计：”)
print(accuracy_2)
```

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_40.png)

结果显示，模型1可能预测正确 1042 次，错误 1093 次；模型2可能预测正确 1096 次，错误 1039 次。虽然两个模型的准确率都略高于 50%，但模型2的正确次数更多，这与其最终获得正收益的结果是相符的。这说明了在量化交易中，即使预测准确率优势不大，长期累积也可能带来可观的收益差异。

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_42.png)

## 策略收益走势可视化

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_44.png)

最后，为了更直观地对比三种策略在整个时间序列上的表现，我们将绘制它们的累计收益走势图。

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_46.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_48.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_50.png)

我们使用 `cumsum()` 函数计算每个时间点的累计对数收益，然后通过 `apply(np.exp)` 进行还原，最后用折线图绘制出来。

以下是绘制走势图的代码：

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_52.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_54.png)

```python
import matplotlib.pyplot as plt

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_56.png)

# 计算每日的累计收益序列
cumulative_series = data[['returns', 'regression_1', 'regression_2']].cumsum().apply(np.exp)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_58.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_60.png)

# 绘制走势图
plt.figure(figsize=(16, 8))
plt.plot(cumulative_series[‘returns’], label=‘实际收益’, color=‘blue’)
plt.plot(cumulative_series[‘regression_1’], label=‘策略1收益’, color=‘green’)
plt.plot(cumulative_series[‘regression_2’], label=‘策略2收益’, color=‘red’)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_62.png)

plt.title(‘不同策略累计收益走势对比’)
plt.xlabel(‘时间’)
plt.ylabel(‘累计收益倍数’)
plt.legend()
plt.grid(True)
plt.show()
```

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_64.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_66.png)

从生成的图中可以看到：
*   **蓝色曲线（实际收益）**：呈现下跌趋势，印证了累计收益小于1的结论。
*   **绿色曲线（策略1收益）**：虽然同样下跌，但走势与实际收益有较大差异，且最终亏损更少。
*   **红色曲线（策略2收益）**：在大部分时间里位于1之上，显示出了盈利的能力，尽管过程中有波动。

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_68.png)

![](img/cbfcbd0bf16bb5b02f6c72738157bebf_70.png)

本节课中我们一起学习了如何评估回归模型在量化交易策略中的应用效果。我们通过计算**累计收益**、分析**预测准确率**以及绘制**收益走势图**，完整地展示了从模型预测到策略评估的全过程。核心在于，我们利用前两天的数据预测下一天的涨跌，并据此构建买卖策略。实验表明，不同的数据标签（如连续收益率 vs. 涨跌方向）和回归模型会对最终策略收益产生显著影响。这为我们理解如何将基础的回归分析应用于金融时间序列预测和策略构建提供了一个清晰的范例。