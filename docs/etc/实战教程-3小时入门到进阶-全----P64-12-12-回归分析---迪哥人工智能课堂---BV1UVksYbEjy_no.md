# Python金融分析与量化交易实战教程：P64：12-12-回归分析结果 📈

![](img/4332035e3f3ceabafa538e8ed167eaa2_1.png)

![](img/4332035e3f3ceabafa538e8ed167eaa2_3.png)

## 概述
在本节课中，我们将学习如何评估和可视化基于回归分析的量化交易策略结果。我们将通过计算累计收益、对比策略表现以及绘制走势图，来直观地理解不同预测模型在实际交易中的效果。

---

上一节我们介绍了如何构建回归模型来预测股票涨跌。本节中，我们来看看如何评估这些模型的实战效果，并将其转化为具体的交易策略收益。

![](img/4332035e3f3ceabafa538e8ed167eaa2_5.png)

### 计算累计收益
首先，我们需要计算不同策略的最终累计收益。我们的数据已经过对数化处理，其性质满足：`log(A) + log(B) = log(A*B)`。因此，我们可以通过对预测的日收益率序列进行累加，再通过指数函数还原，来计算总收益。

以下是计算步骤：
1.  提取三个关键序列：原始收益率 (`returns`)、基于连续值预测的策略收益 (`regress_1`)、基于涨跌分类预测的策略收益 (`regress_2`)。
2.  对每个序列进行累加操作 (`cumsum`)，这相当于计算了从起始点到每个时间点的累计对数收益。
3.  使用指数函数 `np.exp` 将累计对数收益还原为实际的资产乘数（即1元本金最终变为多少元）。

```python
# 假设 data 是包含 returns, regress_1, regress_2 列的DataFrame
cumulative_results = data[['returns', 'regress_1', 'regress_2']].cumsum()
final_values = np.exp(cumulative_results.iloc[-1])  # 取最后一天的值
print(final_values)
```
执行上述代码后，我们得到类似以下的结果：
*   `returns: 0.8` 表示如果什么都不做，1元本金最终变为0.8元，亏损了。
*   `regress_1: 0.94` 表示使用第一个策略，1元本金变为0.94元，亏损减少。
*   `regress_2: 1.3` 表示使用第二个策略，1元本金变为1.3元，实现了盈利。

这个结果表明，第二个基于涨跌方向预测的策略效果更好。

### 评估预测准确率
为了理解策略表现差异的原因，我们可以计算模型的预测准确率。我们将模型预测的涨跌方向与实际涨跌方向进行比较。

![](img/4332035e3f3ceabafa538e8ed167eaa2_7.png)

以下是具体操作：
1.  数据中 `direction` 列代表实际的涨跌情况。
2.  将模型预测的连续值转换为涨跌方向（例如，预测值大于0则为涨）。
3.  比较预测方向与实际方向是否一致。

![](img/4332035e3f3ceabafa538e8ed167eaa2_9.png)

```python
# 计算第一个回归模型的预测准确率
pred_direction_1 = data['regress_1'] > 0
accuracy_series_1 = (pred_direction_1 == data['direction'])
value_counts_1 = accuracy_series_1.value_counts()
print("模型1准确率统计：")
print(value_counts_1)

![](img/4332035e3f3ceabafa538e8ed167eaa2_11.png)

# 计算第二个回归模型的预测准确率
pred_direction_2 = data['regress_2'] > 0 # 注意：regress_2 本身可能就是0/1标签，这里仅为示例逻辑
accuracy_series_2 = (pred_direction_2 == data['direction'])
value_counts_2 = accuracy_series_2.value_counts()
print("\n模型2准确率统计：")
print(value_counts_2)
```
执行结果可能显示：
*   模型1预测正确1042次，错误1093次，准确率略低于50%。
*   模型2预测正确1096次，错误1039次，准确率略高于50%。

![](img/4332035e3f3ceabafa538e8ed167eaa2_13.png)

![](img/4332035e3f3ceabafa538e8ed167eaa2_15.png)

虽然两个模型的准确率相差不大，但第二个模型在关键时点的正确预测可能带来了更高的收益，这解释了其最终累计收益更高的原因。

![](img/4332035e3f3ceabafa538e8ed167eaa2_17.png)

### 可视化策略走势
最后，我们通过绘制资产净值曲线来直观展示三种策略随时间的变化趋势。我们使用 `cumsum` 函数计算每个时点的累计收益，并还原后绘图。

![](img/4332035e3f3ceabafa538e8ed167eaa2_19.png)

以下是绘图步骤：
1.  对三个收益序列分别进行累加 (`cumsum`)。
2.  对累加结果应用指数函数 (`np.exp`) 进行还原。
3.  使用 Matplotlib 绘制三条曲线。

![](img/4332035e3f3ceabafa538e8ed167eaa2_21.png)

![](img/4332035e3f3ceabafa538e8ed167eaa2_23.png)

```python
import matplotlib.pyplot as plt

![](img/4332035e3f3ceabafa538e8ed167eaa2_25.png)

# 计算累计净值曲线
cumulative_curve = data[['returns', 'regress_1', 'regress_2']].cumsum()
cumulative_curve = np.exp(cumulative_curve) # 还原为实际净值

![](img/4332035e3f3ceabafa538e8ed167eaa2_27.png)

# 绘图
plt.figure(figsize=(16, 8))
plt.plot(cumulative_curve['returns'], label='实际走势', color='blue')
plt.plot(cumulative_curve['regress_1'], label='策略一', color='green')
plt.plot(cumulative_curve['regress_2'], label='策略二', color='red')

plt.title('策略累计收益走势对比')
plt.xlabel('时间')
plt.ylabel('资产净值（起始为1）')
plt.legend()
plt.grid(True)
plt.show()
```
生成的图表将清晰显示：
*   **蓝色曲线（实际走势）**：持续下跌，最终净值低于1。
*   **绿色曲线（策略一）**：虽然也下跌，但跌幅小于实际走势。
*   **红色曲线（策略二）**：在多数时间内跑赢实际走势，最终净值高于1，实现了盈利。

![](img/4332035e3f3ceabafa538e8ed167eaa2_29.png)

---

![](img/4332035e3f3ceabafa538e8ed167eaa2_31.png)

![](img/4332035e3f3ceabafa538e8ed167eaa2_33.png)

![](img/4332035e3f3ceabafa538e8ed167eaa2_35.png)

## 总结
本节课中我们一起学习了如何对回归分析的结果进行实战化评估。我们主要完成了三件事：
1.  **计算最终收益**：通过对数收益的累加与还原，量化了不同策略的最终盈亏情况。
2.  **分析预测准确率**：通过对比预测方向与实际方向，定性地理解了模型性能。
3.  **可视化走势对比**：绘制了资产净值曲线，直观展示了各策略在整个回测周期内的表现。

![](img/4332035e3f3ceabafa538e8ed167eaa2_37.png)

![](img/4332035e3f3ceabafa538e8ed167eaa2_39.png)

核心在于，我们演示了如何将简单的回归预测问题（用前两天的数据预测下一天）嵌入到交易策略中（根据预测值决定买卖），并证明了**数据和标签的定义方式（如预测连续收益率还是涨跌方向）会对策略的最终效果产生关键影响**。这为后续探索更复杂的模型奠定了基础。