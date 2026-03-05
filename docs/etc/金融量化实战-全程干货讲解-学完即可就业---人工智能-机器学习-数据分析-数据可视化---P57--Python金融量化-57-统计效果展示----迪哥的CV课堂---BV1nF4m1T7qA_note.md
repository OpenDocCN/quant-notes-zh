# Python金融量化：P57：统计效果展示

![](img/cc9feba8e5360201e195153c08b58eae_0.png)

在本节课中，我们将学习如何对金融数据进行统计分析，以探索特征与市场走势之间的关系。我们将使用`groupby`操作进行数据分组统计，并根据统计结果制定一个简单的交易策略，最后评估该策略的预测准确性和收益表现。

---

![](img/cc9feba8e5360201e195153c08b58eae_2.png)

## 数据准备与分组统计

![](img/cc9feba8e5360201e195153c08b58eae_4.png)

上一节我们介绍了如何构建特征，本节中我们来看看如何对这些特征进行统计分析。首先，我们需要对数据进行变换，以便观察不同特征组合下对应的结果分布。

![](img/cc9feba8e5360201e195153c08b58eae_6.png)

我们的目标是分析“前一天涨跌”和“前两天涨跌”这两个特征，与“当天涨跌”这个结果之间的关系。目前数据中，特征和结果是组合在一起的。

![](img/cc9feba8e5360201e195153c08b58eae_8.png)

我们需要对数据结果列进行操作。具体来说，是对每个特征组合组内的结果进行计数。

![](img/cc9feba8e5360201e195153c08b58eae_10.png)

![](img/cc9feba8e5360201e195153c08b58eae_12.png)

以下是关键的数据处理步骤：

![](img/cc9feba8e5360201e195153c08b58eae_14.png)

```python
# 假设 df 是包含特征列和结果列‘direction’的DataFrame
# 使用 groupby 对特征列进行分组，并对结果进行计数
grouped_result = df.groupby(['feature1', 'feature2'])['direction'].size()
```

![](img/cc9feba8e5360201e195153c08b58eae_16.png)

执行上述代码后，我们得到一个序列，它展示了不同特征组合下，对应结果（涨或跌）出现的次数。为了更清晰地展示所有可能性（包括计数为0的组合），我们可以使用`unstack`方法并填充缺失值为0。

![](img/cc9feba8e5360201e195153c08b58eae_18.png)

```python
# 将分组结果转换为数据透视表，并填充缺失值为0
result_pivot = grouped_result.unstack(fill_value=0)
```

![](img/cc9feba8e5360201e195153c08b58eae_20.png)

![](img/cc9feba8e5360201e195153c08b58eae_22.png)

此时，我们得到一个清晰的表格。表格的行和列索引分别代表两个特征的不同状态（例如，1代表涨，-1代表跌），表格中的值则代表在该特征组合下，结果为“涨”或“跌”的次数。这使我们能够直观地看到特征与结果之间的关联。

![](img/cc9feba8e5360201e195153c08b58eae_24.png)

![](img/cc9feba8e5360201e195153c08b58eae_26.png)

---

![](img/cc9feba8e5360201e195153c08b58eae_28.png)

## 制定基于统计的策略

![](img/cc9feba8e5360201e195153c08b58eae_30.png)

基于上一步得到的统计表格，我们可以分析哪种特征组合下，市场上涨或下跌的可能性更大。

![](img/cc9feba8e5360201e195153c08b58eae_32.png)

例如，我们观察表格发现，当“前一天涨”（值为1）且“前两天涨”（值为1）时，结果为“跌”（值为-1）的次数远多于结果为“涨”（值为1）的次数。这意味着，如果连续两天上涨，第三天更有可能下跌。

根据这个观察，我们可以制定一个简单的策略：
*   当 `前一天特征 == 1 且 前两天特征 == 1` 时，我们预测下一个交易日会`跌 (-1)`。
*   在其他所有特征组合情况下，我们预测下一个交易日会`涨 (1)`。

![](img/cc9feba8e5360201e195153c08b58eae_34.png)

![](img/cc9feba8e5360201e195153c08b58eae_36.png)

以下是使用NumPy的`where`函数实现该策略的代码：

```python
import numpy as np

# 假设 data 是包含特征列 ‘feature1‘ (前一天) 和 ‘feature2‘ (前两天) 的DataFrame
# 计算两个特征值的和
feature_sum = data[['feature1', 'feature2']].sum(axis=1)

![](img/cc9feba8e5360201e195153c08b58eae_38.png)

![](img/cc9feba8e5360201e195153c08b58eae_40.png)

# 根据特征和制定策略：和为2（即两个特征值都为1）时预测为-1（跌），否则预测为1（涨）
strategy_position = np.where(feature_sum == 2, -1, 1)
```

这样，我们就得到了一个基于历史统计规律的每日交易信号序列 `strategy_position`。

![](img/cc9feba8e5360201e195153c08b58eae_42.png)

---

![](img/cc9feba8e5360201e195153c08b58eae_44.png)

![](img/cc9feba8e5360201e195153c08b58eae_46.png)

## 策略效果评估

![](img/cc9feba8e5360201e195153c08b58eae_48.png)

制定策略后，我们需要评估它的有效性。评估分为两个方面：预测准确率和模拟收益。

首先，我们计算策略预测结果与实际市场走势`direction`的一致性。

![](img/cc9feba8e5360201e195153c08b58eae_50.png)

![](img/cc9feba8e5360201e195153c08b58eae_52.png)

```python
# 比较策略预测与实际走势
correct_predictions = (strategy_position == data['direction']).value_counts()
print(correct_predictions)
```
输出结果可能显示，在总共2135个交易日中，预测正确的天数（True）为1102天，预测错误的天数（False）为1033天。准确率略高于50%，说明这个简单的统计策略比随机猜测稍好一些。

![](img/cc9feba8e5360201e195153c08b58eae_54.png)

![](img/cc9feba8e5360201e195153c08b58eae_56.png)

其次，我们模拟按照此策略进行交易的收益情况，并与单纯跟随大盘走势的收益进行对比。

![](img/cc9feba8e5360201e195153c08b58eae_58.png)

以下是计算累积收益的代码框架：

![](img/cc9feba8e5360201e195153c08b58eae_60.png)

```python
# 计算策略收益：策略信号 * 次日收益率
strategy_returns = data['returns'] * strategy_position.shift(1) # 注意信号滞后一期
cumulative_strategy_returns = (1 + strategy_returns).cumprod()

![](img/cc9feba8e5360201e195153c08b58eae_62.png)

![](img/cc9feba8e5360201e195153c08b58eae_64.png)

# 计算大盘收益（假设始终持有，即信号始终为1）
market_returns = data['returns']
cumulative_market_returns = (1 + market_returns).cumprod()

# 绘制收益曲线对比图
import matplotlib.pyplot as plt
plt.figure(figsize=(10,6))
plt.plot(cumulative_market_returns, label='Market Returns')
plt.plot(cumulative_strategy_returns, label='Strategy Returns')
plt.legend()
plt.show()
```

模拟结果可能显示，虽然基于统计的策略最终也产生了亏损，但其亏损幅度（例如，最终净值0.998）小于单纯持有大盘的亏损幅度（例如，最终净值0.997）。这表明该策略在一定程度上起到了减少损失的作用，尽管它并非一个盈利策略。

![](img/cc9feba8e5360201e195153c08b58eae_66.png)

![](img/cc9feba8e5360201e195153c08b58eae_68.png)

---

![](img/cc9feba8e5360201e195153c08b58eae_70.png)

![](img/cc9feba8e5360201e195153c08b58eae_72.png)

## 总结

![](img/cc9feba8e5360201e195153c08b58eae_74.png)

本节课中我们一起学习了如何利用`groupby`进行金融数据的统计分析。我们通过统计不同特征组合下的结果分布，发现“连续两日上涨后第三日更易下跌”的规律，并据此制定了一个简单的交易策略。

![](img/cc9feba8e5360201e195153c08b58eae_76.png)

![](img/cc9feba8e5360201e195153c08b58eae_78.png)

我们评估了该策略，发现其预测准确率仅略高于50%，模拟交易结果虽未能盈利，但相比大盘减少了部分损失。这个案例主要解释了统计分析在量化交易中的基本应用思路：**寻找历史数据中的概率规律，并假设该规律在未来会延续**。

![](img/cc9feba8e5360201e195153c08b58eae_80.png)

需要强调的是，这类基于简单统计的策略通常不够稳健可靠。本节课的目的在于展示方法的基本流程和出发点，为后续学习更复杂的模型打下基础。在实际应用中，需要考虑过拟合、交易成本、市场环境变化等诸多复杂因素。