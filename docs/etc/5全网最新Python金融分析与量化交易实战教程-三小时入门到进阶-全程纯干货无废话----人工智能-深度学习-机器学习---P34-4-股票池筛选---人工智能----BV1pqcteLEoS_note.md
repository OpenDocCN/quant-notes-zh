# Python金融分析与量化交易实战教程：P34：4-股票池筛选 📊

![](img/189d2e8cbab6d420199a8cc0c5bc019d_1.png)

![](img/189d2e8cbab6d420199a8cc0c5bc019d_3.png)

在本节课中，我们将学习如何基于处理后的因子数据，对股票池进行筛选，并准备执行调仓操作。我们将完成因子预处理流程，并实现一个简单的选股策略。

![](img/189d2e8cbab6d420199a8cc0c5bc019d_5.png)

![](img/189d2e8cbab6d420199a8cc0c5bc019d_7.png)

---

![](img/189d2e8cbab6d420199a8cc0c5bc019d_9.png)

上一节我们介绍了因子的中性化操作，本节中我们来看看如何利用处理好的因子数据来筛选股票池。

现在，我们已经完成了中性化操作。接下来，我们可以执行之前写好的几个预处理步骤。

以下是执行预处理操作的步骤：

![](img/189d2e8cbab6d420199a8cc0c5bc019d_11.png)

首先，对因子数据进行离群值处理，使用三西格玛方法。这里，我们需要传入因子数据。

![](img/189d2e8cbab6d420199a8cc0c5bc019d_13.png)

```python
fundamental = pd.DataFrame(fundamental_data)
factor_data = fundamental['市净率']
factor_no_outliers = three_sigma(factor_data)
```

![](img/189d2e8cbab6d420199a8cc0c5bc019d_15.png)

完成离群值处理后，我们得到一个没有极端值的因子序列。但这还不够，我们还需要进行下一步操作。

![](img/189d2e8cbab6d420199a8cc0c5bc019d_17.png)

![](img/189d2e8cbab6d420199a8cc0c5bc019d_19.png)

接下来，对处理后的因子数据进行标准化操作。

![](img/189d2e8cbab6d420199a8cc0c5bc019d_21.png)

![](img/189d2e8cbab6d420199a8cc0c5bc019d_23.png)

```python
factor_standardized = standardize(factor_no_outliers)
```

![](img/189d2e8cbab6d420199a8cc0c5bc019d_25.png)

做完标准化操作之后，最后一步是进行中性化操作。

![](img/189d2e8cbab6d420199a8cc0c5bc019d_27.png)

```python
factor_neutralized = neutralize(factor_standardized)
preprocessed_factor = factor_neutralized
```

现在，我们就完成了所有必需的因子预处理操作。

![](img/189d2e8cbab6d420199a8cc0c5bc019d_29.png)

---

接下来，我们需要基于这个处理好的因子对股票池进行筛选。

以下是基于因子进行股票筛选的思路：

我们希望选择因子值较小的股票，因为根据之前的分析，较小的市净率可能带来更高的期望收益。我们将选择因子值最小的前20%的股票。

![](img/189d2e8cbab6d420199a8cc0c5bc019d_31.png)

![](img/189d2e8cbab6d420199a8cc0c5bc019d_33.png)

```python
threshold = preprocessed_factor.quantile(0.2)
selected_stocks = preprocessed_factor[preprocessed_factor <= threshold].index.tolist()
```

我们得到了一个选中的股票列表。接下来，需要将这个列表存入上下文环境，以便后续交易操作使用。

![](img/189d2e8cbab6d420199a8cc0c5bc019d_35.png)

```python
context.stock_list = selected_stocks
```

![](img/189d2e8cbab6d420199a8cc0c5bc019d_37.png)

![](img/189d2e8cbab6d420199a8cc0c5bc019d_39.png)

---

有了选中的股票池后，下一步是执行调仓操作，这涉及到买入新股票和卖出不再符合条件的股票。

首先，我们需要识别并删除那些不在当前选中股票池中的持仓股票。

![](img/189d2e8cbab6d420199a8cc0c5bc019d_41.png)

以下是删除不在当前股票池中的持仓股票的步骤：

![](img/189d2e8cbab6d420199a8cc0c5bc019d_43.png)

![](img/189d2e8cbab6d420199a8cc0c5bc019d_45.png)

1.  获取当前账户的持仓信息。
2.  找出持仓股票与当前选中股票池的差异部分（即持有但不在新池子中的股票）。

```python
current_positions = context.portfolio.positions.keys()  # 获取当前持仓股票代码
stocks_to_sell = list(set(current_positions).difference(set(context.stock_list)))
```

这样，我们就得到了一个需要卖出的股票列表。这为后续的买卖交易指令做好了准备。

---

![](img/189d2e8cbab6d420199a8cc0c5bc019d_47.png)

本节课中我们一起学习了如何将预处理后的因子应用于股票池筛选。我们完成了从离群值处理、标准化到中性化的完整因子预处理流程，并基于处理后的因子值实现了简单的选股策略（选择因子值最小的前20%股票）。最后，我们为调仓操作做好了准备，识别出了需要从当前持仓中移除的股票。这些步骤构成了量化交易策略中股票筛选与组合调整的核心环节。