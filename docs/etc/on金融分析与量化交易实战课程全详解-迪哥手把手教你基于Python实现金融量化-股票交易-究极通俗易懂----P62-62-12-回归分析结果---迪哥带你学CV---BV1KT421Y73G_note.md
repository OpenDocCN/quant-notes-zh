# Python金融分析与量化交易实战课程：P62：回归分析结果 📈

![](img/c7b618077b84744c1edde7d5c7cb5639_1.png)

在本节课中，我们将学习如何对回归分析的结果进行处理、评估和可视化。我们将基于之前构建的回归模型，计算不同交易策略的累计收益，并与原始走势进行对比，从而评估模型的有效性。

![](img/c7b618077b84744c1edde7d5c7cb5639_3.png)

---

上一节我们介绍了如何构建回归模型来预测股票走势。本节中，我们来看看如何评估这些模型的实战效果，并计算不同策略的最终收益。

首先，我们已经有了处理好的数据。之前我们对数据进行了对数化处理，其数学原理是：`log(A) + log(B) = log(A * B)`。这种处理使得数据具有可加性，便于我们进行累计计算。

现在，我们可以对数据执行累加操作。我们的目标是预测并对比三种情况下的最终收益：
1.  原始走势（什么都不做）。
2.  基于第一个回归方程的策略。
3.  基于第二个回归方程的策略。

![](img/c7b618077b84744c1edde7d5c7cb5639_5.png)

以下是具体操作步骤：

首先，从数据中提取我们需要的关键指标：原始收益率（`returns`）、第一个回归模型的预测值（`regression_1`）和第二个回归模型的预测值（`regression_2`）。

```python
# 提取关键指标
returns = data['returns']
regression_1 = data['regression_1']
regression_2 = data['regression_2']
```

接着，对这些指标进行累加。由于我们之前对数据取了对数，累加后需要将其还原为原始数值。

```python
# 对指标进行累加并还原（取指数）
cumulative_returns = np.exp(returns.cumsum())
cumulative_strategy_1 = np.exp(regression_1.cumsum())
cumulative_strategy_2 = np.exp(regression_2.cumsum())
```

执行上述代码后，我们得到了三个最终数值。例如，`cumulative_returns` 为 0.8，意味着初始的1元钱最终变成了0.8元，即亏损了20%。`cumulative_strategy_1` 的结果是0.94，虽然也亏损，但比什么都不做亏得少。而 `cumulative_strategy_2` 的结果是1.3，意味着初始的1元钱变成了1.3元，实现了盈利。

---

为了理解模型预测的准确性，我们可以将模型的预测方向与实际涨跌方向进行比较。

![](img/c7b618077b84744c1edde7d5c7cb5639_7.png)

以下是评估预测准确率的步骤：

![](img/c7b618077b84744c1edde7d5c7cb5639_9.png)

我们有一列名为 `direction` 的数据，它记录了股票实际的涨跌情况。我们可以将其与模型的预测值进行比较。

```python
# 比较第一个回归模型的预测方向与实际方向
predictions_1 = (regression_1 > 0)  # 预测为涨
actual_direction = (data['direction'] == 1)  # 实际为涨
comparison_1 = (predictions_1 == actual_direction)
value_counts_1 = comparison_1.value_counts()
```

![](img/c7b618077b84744c1edde7d5c7cb5639_11.png)

查看 `value_counts_1` 的结果，例如 `False: 1093, True: 1042`，表示第一个模型预测错了1093次，预测对了1042次，准确率略低于50%，这解释了为何该策略最终仍然亏损。

![](img/c7b618077b84744c1edde7d5c7cb5639_13.png)

![](img/c7b618077b84744c1edde7d5c7cb5639_15.png)

对第二个回归模型进行同样的操作：

![](img/c7b618077b84744c1edde7d5c7cb5639_17.png)

```python
# 比较第二个回归模型的预测方向与实际方向
predictions_2 = (regression_2 > 0)
comparison_2 = (predictions_2 == actual_direction)
value_counts_2 = comparison_2.value_counts()
```

`value_counts_2` 的结果可能是 `True: 1096, False: 1039`。第二个模型预测正确的次数稍多于错误的次数，准确率略高于50%，这与其最终实现盈利的结果相符。

---

![](img/c7b618077b84744c1edde7d5c7cb5639_19.png)

![](img/c7b618077b84744c1edde7d5c7cb5639_21.png)

最后，我们来可视化这三种策略在整个时间序列上的走势，而不仅仅是最终结果。

![](img/c7b618077b84744c1edde7d5c7cb5639_23.png)

上一节我们计算了最终收益，但投资者通常关心整个投资期间的收益曲线。本节中，我们使用累积求和函数来绘制走势图。

![](img/c7b618077b84744c1edde7d5c7cb5639_25.png)

`cumsum()` 函数可以实现累加操作：第一天的值是它本身，第二天的值是前两天的和，以此类推。这正好可以描绘出从起始点到任意时间点的累计收益曲线。

以下是绘制走势图的代码：

![](img/c7b618077b84744c1edde7d5c7cb5639_27.png)

```python
import matplotlib.pyplot as plt

# 计算累计收益曲线
cum_curve_returns = np.exp(returns.cumsum())
cum_curve_strategy_1 = np.exp(regression_1.cumsum())
cum_curve_strategy_2 = np.exp(regression_2.cumsum())

![](img/c7b618077b84744c1edde7d5c7cb5639_29.png)

# 绘图
plt.figure(figsize=(16, 8))
plt.plot(cum_curve_returns, label='原始走势', color='blue')
plt.plot(cum_curve_strategy_1, label='策略一', color='green')
plt.plot(cum_curve_strategy_2, label='策略二', color='red')
plt.xlabel('时间')
plt.ylabel('累计收益（起始为1）')
plt.title('不同策略收益走势对比')
plt.legend()
plt.grid(True)
plt.show()
```

在生成的图中，蓝色曲线代表原始走势，显示为下跌趋势，1元钱最终跌至约0.8元。绿色曲线代表第一个策略，虽然最终也下跌，但其路径与蓝色曲线有显著差异。红色曲线代表第二个策略，在大部分时间里都实现了盈利，最终收益高于初始本金。

![](img/c7b618077b84744c1edde7d5c7cb5639_31.png)

![](img/c7b618077b84744c1edde7d5c7cb5639_33.png)

---

![](img/c7b618077b84744c1edde7d5c7cb5639_35.png)

本节课中我们一起学习了如何对回归分析的结果进行实战评估。我们通过计算累计收益，对比了“无为而治”和两种主动交易策略的最终效果。接着，我们通过比较预测方向与实际涨跌，分析了模型盈利或亏损的原因。最后，我们使用 `cumsum()` 函数绘制了完整的收益走势图，直观展示了不同策略在整个投资期内的表现。

![](img/c7b618077b84744c1edde7d5c7cb5639_37.png)

![](img/c7b618077b84744c1edde7d5c7cb5639_39.png)

核心在于，数据和标签（预测目标）的选择都会对量化策略的结果产生重大影响。本节课演示了如何将传统的回归预测问题融入到交易策略的构建与评估中。