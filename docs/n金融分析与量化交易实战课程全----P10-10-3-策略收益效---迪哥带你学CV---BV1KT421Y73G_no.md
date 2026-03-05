# Python金融分析与量化交易实战课程：P10：10.3-策略收益效果分析 📈

在本节课中，我们将学习如何计算和比较量化交易策略的收益。我们将基于股价数据，计算“什么都不做”的基准收益，并与我们设计的“双均线策略”收益进行对比，从而评估策略的有效性。

![](img/aaa0faeba66dd77b493bab537ad85591_1.png)

![](img/aaa0faeba66dd77b493bab537ad85591_3.png)

上一节我们介绍了如何通过双均线交叉来生成交易信号。本节中，我们来看看如何将这些信号转化为具体的收益数据，并进行效果分析。

## 计算基准收益率

首先，我们需要计算股票的基准收益率。这是指在不采取任何策略、单纯持有股票的情况下，每日的收益率变化。计算收益率时，我们通常使用对数收益率，因为它具有更好的数学性质（如可加性）。

以下是计算对数收益率的公式和代码：

![](img/aaa0faeba66dd77b493bab537ad85591_5.png)

![](img/aaa0faeba66dd77b493bab537ad85591_7.png)

**公式**：`log_return = log(price_t / price_{t-1})`

![](img/aaa0faeba66dd77b493bab537ad85591_9.png)

**代码**：
```python
# 计算对数收益率
data['returns'] = np.log(data['close'] / data['close'].shift(1))
```
在这段代码中，`data[‘close’]`代表股价序列，`.shift(1)`表示将数据向下移动一行，从而用当天的价格除以前一天的价格。

## 计算策略收益率

![](img/aaa0faeba66dd77b493bab537ad85591_11.png)

![](img/aaa0faeba66dd77b493bab537ad85591_13.png)

接下来，我们根据之前生成的交易信号来计算策略收益率。我们的策略逻辑是：当`position`信号为1时，表示我们跟随市场正常走势；当`position`信号为-1时，表示我们采取与市场相反的操作（即做空或反向交易）。

![](img/aaa0faeba66dd77b493bab537ad85591_15.png)

以下是计算策略收益率的步骤：

![](img/aaa0faeba66dd77b493bab537ad85591_17.png)

1.  将交易信号`position`进行位移操作，以确保信号与收益率在时间上对齐。
2.  将位移后的信号与基准收益率相乘，得到策略的每日收益率。

**代码**：
```python
# 计算策略收益率
data['strategy'] = data['position'].shift(1) * data['returns']
```
这里，`data[‘position’].shift(1)`确保了我们在前一天收盘时就已经知道了第二天的交易信号，这符合实际交易中“信号先行”的逻辑。

![](img/aaa0faeba66dd77b493bab537ad85591_19.png)

## 累计收益与结果对比

![](img/aaa0faeba66dd77b493bab537ad85591_21.png)

计算出每日收益率后，我们需要将其累加起来，得到从期初到期末的总收益率。由于我们之前使用的是对数收益率，累加操作就是简单的求和。最后，我们需要将对数收益率还原为常规的累计收益倍数。

![](img/aaa0faeba66dd77b493bab537ad85591_23.png)

![](img/aaa0faeba66dd77b493bab537ad85591_25.png)

以下是计算和对比累计收益的步骤：

![](img/aaa0faeba66dd77b493bab537ad85591_27.png)

![](img/aaa0faeba66dd77b493bab537ad85591_29.png)

1.  分别对基准收益率列和策略收益率列进行求和，得到总的对数收益率。
2.  使用指数函数`np.exp()`将对数总收益率还原为最终的收益倍数。

**代码**：
```python
# 计算总的对数收益率
total_log_return_baseline = data['returns'].sum()
total_log_return_strategy = data['strategy'].sum()

# 还原为收益倍数
final_value_baseline = np.exp(total_log_return_baseline)
final_value_strategy = np.exp(total_log_return_strategy)

![](img/aaa0faeba66dd77b493bab537ad85591_31.png)

print(f"基准策略最终收益倍数: {final_value_baseline:.2f}")
print(f"双均线策略最终收益倍数: {final_value_strategy:.2f}")
```
执行以上代码后，我们可以得到类似“1元钱最终变成了X元”的结果。通过比较两个数值，就能直观地判断我们的双均线策略是否跑赢了简单的持有策略。

![](img/aaa0faeba66dd77b493bab537ad85591_33.png)

![](img/aaa0faeba66dd77b493bab537ad85591_35.png)

## 结果解读

![](img/aaa0faeba66dd77b493bab537ad85591_37.png)

在本案例中，我们可能会得到如下结果：
*   基准策略（买入并持有）：1元 → 约4元
*   双均线策略：1元 → 约5.8元

这个结果表明，在我们测试的这段历史数据上，应用双均线策略获得的收益超过了单纯持有股票的收益。这初步验证了该策略在特定条件下的有效性。

![](img/aaa0faeba66dd77b493bab537ad85591_39.png)

本节课中我们一起学习了量化策略收益分析的全过程。我们从计算基准对数收益率开始，接着将交易信号转化为策略收益率，最后通过累加和还原操作，得到了策略的最终收益表现，并与基准进行了对比。这是评估任何一个量化策略是否有效的核心步骤。