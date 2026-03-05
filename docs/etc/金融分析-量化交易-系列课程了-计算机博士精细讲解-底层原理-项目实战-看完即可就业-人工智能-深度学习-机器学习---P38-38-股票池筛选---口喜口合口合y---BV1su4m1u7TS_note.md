# Python金融分析+量化交易：P38：股票池筛选 📊

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_0.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_2.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_4.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_6.png)

## 概述
在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并构建一个简单的选股策略。我们将完成因子预处理后的最后一步——股票筛选，并开始为后续的交易操作做准备。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_8.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何利用处理好的因子来筛选股票。

---

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_10.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_12.png)

## 执行预处理操作
现在，我们已经完成了中性化操作的代码编写。接下来，我们可以执行之前写好的几个预处理步骤。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_14.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_16.png)

首先，我们改写预处理操作。在预处理操作中，我们首先进行3-Sigma去离群值处理。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_18.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_20.png)

```python
# 对因子数据（例如市净率）进行去离群值处理
factor_processed = three_sigma(fundamental_data, factor_name='PB')
```

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_22.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_24.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_26.png)

这里，`fundamental_data` 是一个DataFrame，包含了我们需要的因子指标。`three_sigma` 函数会对指定的因子列进行去离群值处理。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_28.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_30.png)

处理完成后，我们得到了一个没有极端值的因子数据。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_32.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_34.png)

---

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_36.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_38.png)

## 标准化与中性化处理
去完离群值之后，我们的因子数据已经剔除了极值。但预处理还没有结束，我们还需要进行标准化操作。

以下是标准化操作的代码：
```python
# 对去离群值后的因子数据进行标准化
factor_standardized = standardize(factor_processed)
```

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_40.png)

标准化操作完成后，我们还需要进行中性化处理。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_42.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_44.png)

以下是中性化操作的代码：
```python
# 对标准化后的因子数据进行中性化处理
factor_neutralized = neutralize(factor_standardized, exposure=['industry', 'market_cap'])
```

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_46.png)

现在，我们就完成了所有必需的因子预处理操作，得到了一个经过清洗和调整的因子值。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_48.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_50.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_52.png)

---

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_54.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_56.png)

## 基于因子筛选股票
接下来，我们需要基于这个处理好的因子对股票池进行筛选，或者说进行调仓。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_58.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_60.png)

以下是进行筛选操作的思路：
我们需要指定一个筛选条件。例如，对于市净率（PB）因子，通常认为值较小的股票未来表现可能更好。因此，我们可以选择因子值最小的前20%的股票。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_62.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_64.png)

以下是筛选操作的代码示例：
```python
# 计算因子值的分位数，选择最小的20%
threshold = factor_neutralized.quantile(0.2)
# 筛选出因子值小于等于该阈值的股票
selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
```

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_66.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_68.png)

这段代码首先计算了因子值的20%分位点作为阈值，然后筛选出所有因子值小于等于这个阈值的股票，并将它们的代码存入列表。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_70.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_72.png)

我们将筛选出的股票列表存入上下文（context）中，供后续交易使用。
```python
context.stock_list = selected_stocks
```

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_74.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_76.png)

---

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_78.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_80.png)

## 准备调仓：识别需卖出的股票
有了目标股票池后，下一步是执行调仓操作，包括买入新股票和卖出不再持有的股票。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_82.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_84.png)

首先，我们需要识别出哪些当前持有的股票不在新的目标股票池中，这些股票需要被卖出。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_86.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_88.png)

以下是识别需卖出股票的代码：
```python
# 获取当前账户持有的所有股票
current_holdings = list(context.portfolio.positions.keys())
# 找出当前持有但不在新股票池中的股票（即需要卖出的股票）
stocks_to_sell = list(set(current_holdings) - set(context.stock_list))
```

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_90.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_92.png)

这段代码的逻辑是：计算当前持仓股票集合与新目标股票池集合的差集。结果就是那些我们持有但已被移出股票池的股票，它们是需要被卖出的对象。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_94.png)

---

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_96.png)

## 总结
本节课中我们一起学习了股票池筛选的完整流程。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_98.png)

1.  **回顾与执行预处理**：我们首先执行了因子的去离群值、标准化和中性化处理，为筛选准备了干净、有效的因子数据。
2.  **设定筛选条件**：我们以市净率因子为例，设定了选择因子值最小的前20%股票作为筛选条件。
3.  **生成目标股票池**：根据筛选条件，我们得到了一个新的目标股票列表，并存入上下文。
4.  **准备交易逻辑**：最后，我们通过对比当前持仓和新股票池，识别出了需要卖出的股票，为下一节课实现具体的买卖交易指令打下了基础。

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_100.png)

![](img/af313b2aa1cc8d68b5a7ea4a7ef4b159_102.png)

通过以上步骤，我们构建了一个基于单因子的简单选股策略框架。在接下来的课程中，我们将在此基础上实现完整的交易逻辑。