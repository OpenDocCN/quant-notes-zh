# Python金融分析量化交易：P14：3-策略收益效果分析 📈

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_1.png)

在本节课中，我们将学习如何计算和比较量化策略的收益。我们将通过一个具体的例子，分析使用双均线策略与不采取任何策略（即持有原始资产）的收益差异。核心内容包括计算对数收益率、根据策略信号调整收益，以及最终将累计对数收益还原为实际收益。

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_3.png)

---

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_5.png)

上一节我们介绍了如何生成交易信号，本节中我们来看看如何基于这些信号计算策略的最终收益。

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_7.png)

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_9.png)

首先，我们需要计算资产价格的对数收益率。对数收益率是金融分析中常用的指标，因为它具有可加性，便于进行多期收益的累加计算。

以下是计算对数收益率的步骤：
1.  获取股价序列 `data['close']`。
2.  使用公式 `np.log(data['close'] / data['close'].shift(1))` 计算每日的对数收益率。
3.  将计算结果赋值给新列，例如 `data['returns']`。

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_11.png)

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_13.png)

对应的代码如下：
```python
import numpy as np
# 假设data是一个包含‘close’列的DataFrame
data['returns'] = np.log(data['close'] / data['close'].shift(1))
```

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_15.png)

---

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_17.png)

上一节我们计算了基础收益率，本节中我们来看看如何根据交易策略调整这些收益。

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_19.png)

我们的策略信号由 `position` 列表示，其值为1或-1。当 `position` 为1时，表示我们遵循原始价格走势；当 `position` 为-1时，表示我们采取与原始走势相反的操作（例如，在预期下跌时做空或卖出）。

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_21.png)

因此，策略调整后的收益率应为原始收益率乘以对应的 `position` 信号。同样，为了对齐时间，我们需要对 `position` 列进行移位操作。

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_23.png)

以下是计算策略收益的步骤：
1.  确保 `position` 列已经过移位对齐，即 `data['position'].shift(1)`。
2.  计算策略收益：`strategy_returns = data['position'].shift(1) * data['returns']`。
3.  将计算结果赋值给新列，例如 `data['strategy_returns']`。

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_25.png)

对应的代码如下：
```python
data['strategy_returns'] = data['position'].shift(1) * data['returns']
```

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_27.png)

---

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_29.png)

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_31.png)

在分别计算出原始累计收益和策略累计收益后，我们需要将它们进行比较，以评估策略的有效性。

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_33.png)

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_35.png)

为了得到从期初1元本金到期末的总价值，我们需要对处理后的收益率序列进行累加，然后将对数收益还原为实际收益。还原公式为 `np.exp(cumulative_returns)`。

以下是计算并比较最终收益的步骤：
1.  分别对 `data['returns']` 和 `data['strategy_returns']` 剔除缺失值（`dropna`）。
2.  分别对两个序列进行累加求和（`sum`），得到累计对数收益。
3.  使用指数函数 `np.exp()` 将累计对数收益还原为实际的总收益倍数。
4.  比较两个结果。

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_37.png)

对应的代码如下：
```python
# 剔除缺失值并计算累计对数收益
cum_log_returns_buy_hold = data['returns'].dropna().sum()
cum_log_returns_strategy = data['strategy_returns'].dropna().sum()

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_39.png)

# 还原为实际收益倍数
final_return_buy_hold = np.exp(cum_log_returns_buy_hold)
final_return_strategy = np.exp(cum_log_returns_strategy)

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_41.png)

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_43.png)

print(f"不做任何操作的最终收益倍数: {final_return_buy_hold:.2f}")
print(f"采用双均线策略的最终收益倍数: {final_return_strategy:.2f}")
```

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_45.png)

在本示例中，假设原始持有策略的1元本金最终变为4元，而采用双均线策略后，1元本金最终变为5.8元。这表明在该段历史数据上，双均线策略获得了更高的收益。

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_47.png)

---

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_49.png)

![](img/7744ae38c6b0d663ad8f33ad0b8faa3d_51.png)

本节课中我们一起学习了量化策略收益分析的全过程。我们首先计算了对数收益率，然后根据交易信号（`position`）调整得到策略收益率，最后通过累加和还原操作，比较了策略收益与基准收益。结果显示，双均线策略在本案例中取得了优于简单持有策略的效果。这个过程是量化交易中评估策略性能的基础环节。