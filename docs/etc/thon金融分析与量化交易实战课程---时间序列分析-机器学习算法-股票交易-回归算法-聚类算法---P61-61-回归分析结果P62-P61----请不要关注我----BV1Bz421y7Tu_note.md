# 量化交易实战：P61：回归分析结果评估与可视化

![](img/7b7bd14eb8d2de21f75cd8372a403c00_0.png)

![](img/7b7bd14eb8d2de21f75cd8372a403c00_2.png)

在本节课中，我们将学习如何评估回归分析在量化交易策略中的实际效果。我们将通过计算累计收益、对比策略表现以及可视化走势图，来直观地理解不同预测模型对投资结果的影响。

上一节我们介绍了如何构建回归模型来预测股票涨跌。本节中，我们来看看如何评估这些模型的实战表现，并对比不同策略的最终收益。

![](img/7b7bd14eb8d2de21f75cd8372a403c00_4.png)

## 累计收益计算

![](img/7b7bd14eb8d2de21f75cd8372a403c00_6.png)

首先，我们需要计算不同策略的累计收益。我们已经有了每日的收益率数据（`returns`）以及两个回归模型的预测结果（`regression_1` 和 `regression_2`）。由于之前对收益率进行了对数处理，现在需要先进行累加，再通过指数函数还原为实际收益。

以下是计算累计收益的步骤：
1.  对收益率序列和两个预测结果序列分别进行累加操作。
2.  使用指数函数 `np.exp()` 将累加后的对数收益还原为实际收益倍数。

```python
# 假设 returns, regression_1, regression_2 是已有的数据序列
import numpy as np

# 计算累计收益（还原后）
cumulative_returns = np.exp(np.cumsum(returns))
cumulative_strategy_1 = np.exp(np.cumsum(regression_1))
cumulative_strategy_2 = np.exp(np.cumsum(regression_2))

# 查看最终收益倍数
print(f"原始策略最终收益倍数: {cumulative_returns.iloc[-1]:.2f}")
print(f"策略一最终收益倍数: {cumulative_strategy_1.iloc[-1]:.2f}")
print(f"策略二最终收益倍数: {cumulative_strategy_2.iloc[-1]:.2f}")
```
执行代码后，我们可以分析结果。例如，如果原始策略最终值为0.8，意味着初始的1元钱最终变为0.8元，亏损了20%。策略一的结果为0.94，意味着亏损减少至6%。而策略二的结果为1.3，则意味着实现了30%的盈利。这直观地展示了不同预测模型对投资结果的影响。

![](img/7b7bd14eb8d2de21f75cd8372a403c00_8.png)

## 预测准确率分析

![](img/7b7bd14eb8d2de21f75cd8372a403c00_10.png)

为了更深入地理解模型表现，我们可以分析预测方向的准确率。数据中通常有一列 `direction` 表示实际的涨跌方向（上涨为1，下跌为0）。我们可以将模型的预测方向与实际方向进行比较。

![](img/7b7bd14eb8d2de21f75cd8372a403c00_12.png)

以下是计算预测准确率的步骤：
1.  将回归模型的连续预测值转换为涨跌方向（例如，预测值大于0视为看涨，否则看跌）。
2.  将此预测方向与实际 `direction` 列进行比较。
3.  统计预测正确和错误的次数。

![](img/7b7bd14eb8d2de21f75cd8372a403c00_14.png)

```python
# 将回归预测值转换为方向（假设回归预测的是收益率）
predicted_direction_1 = (regression_1 > 0).astype(int)
predicted_direction_2 = (regression_2 > 0).astype(int)

![](img/7b7bd14eb8d2de21f75cd8372a403c00_16.png)

![](img/7b7bd14eb8d2de21f75cd8372a403c00_18.png)

actual_direction = data['direction'] # 假设实际方向列名为 ‘direction‘

![](img/7b7bd14eb8d2de21f75cd8372a403c00_20.png)

![](img/7b7bd14eb8d2de21f75cd8372a403c00_22.png)

# 计算准确率
correct_1 = (predicted_direction_1 == actual_direction).value_counts()
correct_2 = (predicted_direction_2 == actual_direction).value_counts()

print("策略一预测方向统计:")
print(correct_1)
print("\n策略二预测方向统计:")
print(correct_2)
```
分析结果可能显示，策略一预测正确1042次，错误1093次；策略二预测正确1096次，错误1039次。虽然策略二的正确率仅略高于50%，但结合具体的交易规则（如只在看涨时买入并持有），这微弱的优势在长期累积下转化为了正向收益。这说明了在量化交易中，预测准确率与最终盈利并非简单的线性关系。

![](img/7b7bd14eb8d2de21f75cd8372a403c00_24.png)

![](img/7b7bd14eb8d2de21f75cd8372a403c00_26.png)

## 收益走势可视化

![](img/7b7bd14eb8d2de21f75cd8372a403c00_28.png)

![](img/7b7bd14eb8d2de21f75cd8372a403c00_30.png)

最后，为了更直观地对比三种策略随时间的变化情况，我们可以绘制它们的累计收益走势图。这需要使用累加函数 `cumsum()` 来生成每个时间点的累计收益序列，然后进行绘图。

以下是绘制收益走势图的步骤：
1.  对 `returns`、`regression_1`、`regression_2` 分别应用 `cumsum()` 函数进行累加。
2.  使用 `np.exp()` 函数将累加后的对数收益还原。
3.  使用 Matplotlib 库在同一张图中绘制三条曲线。

![](img/7b7bd14eb8d2de21f75cd8372a403c00_32.png)

```python
import matplotlib.pyplot as plt

![](img/7b7bd14eb8d2de21f75cd8372a403c00_34.png)

# 计算用于绘图的累计序列（与之前计算最终值逻辑一致）
plot_cum_returns = np.exp(np.cumsum(returns))
plot_cum_s1 = np.exp(np.cumsum(regression_1))
plot_cum_s2 = np.exp(np.cumsum(regression_2))

![](img/7b7bd14eb8d2de21f75cd8372a403c00_36.png)

# 绘图
plt.figure(figsize=(16, 8))
plt.plot(plot_cum_returns, label='原始走势', color='blue')
plt.plot(plot_cum_s1, label='回归策略一', color='green')
plt.plot(plot_cum_s2, label='回归策略二', color='red')

![](img/7b7bd14eb8d2de21f75cd8372a403c00_38.png)

![](img/7b7bd14eb8d2de21f75cd8372a403c00_40.png)

plt.title('不同策略累计收益走势对比')
plt.xlabel('时间')
plt.ylabel('累计收益倍数')
plt.legend()
plt.grid(True)
plt.show()
```
通过走势图可以清晰看到：
*   **蓝色曲线（原始走势）**：呈现下跌趋势，最终收益低于初始本金。
*   **绿色曲线（策略一）**：虽然同样下跌，但跌幅小于原始走势，起到了减少亏损的作用。
*   **红色曲线（策略二）**：大部分时间位于1.0上方，最终实现了盈利，其走势与原始走势差异明显。

![](img/7b7bd14eb8d2de21f75cd8372a403c00_42.png)

![](img/7b7bd14eb8d2de21f75cd8372a403c00_44.png)

本节课中我们一起学习了如何评估回归分析在量化策略中的应用效果。我们通过计算累计收益对比了“无为而治”、“回归策略一”和“回归策略二”的最终结果，并通过方向准确率分析了模型的预测能力，最后绘制走势图直观展示了策略的全程表现。这个过程揭示了即使预测准确率优势不大，但通过合理的策略设计（如基于预测信号执行买卖），仍然有可能在市场中获取超额收益。这完整展示了如何将一个简单的回归预测问题，转化为可评估、可执行的量化交易策略雏形。