# Python金融分析量化交易：P38：4-股票池筛选 📊

![](img/e6fae3968390fd4937d61f114027be43_0.png)

![](img/e6fae3968390fd4937d61f114027be43_2.png)

![](img/e6fae3968390fd4937d61f114027be43_4.png)

![](img/e6fae3968390fd4937d61f114027be43_6.png)

![](img/e6fae3968390fd4937d61f114027be43_8.png)

在本节课中，我们将学习如何基于处理后的因子数据，构建并筛选一个股票池。我们将完成数据预处理（去极值、标准化、中性化）的代码整合，并基于选定的因子（如市净率）对股票进行筛选，为后续的调仓交易操作做好准备。

![](img/e6fae3968390fd4937d61f114027be43_10.png)

![](img/e6fae3968390fd4937d61f114027be43_12.png)

上一节我们介绍了因子的中性化处理，本节中我们来看看如何将预处理流程串联起来，并基于处理后的因子值筛选出目标股票。

![](img/e6fae3968390fd4937d61f114027be43_14.png)

![](img/e6fae3968390fd4937d61f114027be43_16.png)

---

![](img/e6fae3968390fd4937d61f114027be43_18.png)

![](img/e6fae3968390fd4937d61f114027be43_20.png)

![](img/e6fae3968390fd4937d61f114027be43_22.png)

## 数据预处理流程整合

![](img/e6fae3968390fd4937d61f114027be43_24.png)

![](img/e6fae3968390fd4937d61f114027be43_26.png)

![](img/e6fae3968390fd4937d61f114027be43_28.png)

现在，我们已经完成了中性化操作的函数编写。接下来，我们需要将之前定义的预处理步骤（去极值、标准化、中性化）整合起来，应用到我们的因子数据上。

![](img/e6fae3968390fd4937d61f114027be43_30.png)

![](img/e6fae3968390fd4937d61f114027be43_32.png)

![](img/e6fae3968390fd4937d61f114027be43_34.png)

以下是整合预处理操作的步骤：

![](img/e6fae3968390fd4937d61f114027be43_36.png)

![](img/e6fae3968390fd4937d61f114027be43_38.png)

![](img/e6fae3968390fd4937d61f114027be43_40.png)

1.  **去极值处理**：首先，我们对原始的因子数据（例如市净率）进行去极值处理，以消除异常值的影响。
    ```python
    factor_processed = winsorize(factor_data)
    ```

![](img/e6fae3968390fd4937d61f114027be43_42.png)

![](img/e6fae3968390fd4937d61f114027be43_44.png)

![](img/e6fae3968390fd4937d61f114027be43_46.png)

2.  **标准化处理**：接着，对去极值后的数据进行标准化，使其均值为0，标准差为1，便于不同因子间的比较。
    ```python
    factor_standardized = standardize(factor_processed)
    ```

![](img/e6fae3968390fd4937d61f114027be43_48.png)

![](img/e6fae3968390fd4937d61f114027be43_50.png)

![](img/e6fae3968390fd4937d61f114027be43_52.png)

3.  **中性化处理**：最后，对标准化后的数据进行行业中性化处理，以剥离行业因素对因子表现的影响。
    ```python
    factor_neutralized = neutralize(factor_standardized, industry_data)
    ```

![](img/e6fae3968390fd4937d61f114027be43_54.png)

![](img/e6fae3968390fd4937d61f114027be43_56.png)

完成以上三步后，我们就得到了一个经过完整预处理的、可用于选股的因子数据。

![](img/e6fae3968390fd4937d61f114027be43_58.png)

![](img/e6fae3968390fd4937d61f114027be43_60.png)

![](img/e6fae3968390fd4937d61f114027be43_62.png)

---

![](img/e6fae3968390fd4937d61f114027be43_64.png)

![](img/e6fae3968390fd4937d61f114027be43_66.png)

## 基于因子筛选股票池

![](img/e6fae3968390fd4937d61f114027be43_68.png)

![](img/e6fae3968390fd4937d61f114027be43_70.png)

![](img/e6fae3968390fd4937d61f114027be43_72.png)

预处理完成后，下一步是基于这个因子值来筛选我们的股票池。我们的目标是选出因子值较小（例如市净率较低）的股票，因为根据之前的分析，这类股票可能具有更高的预期收益。

![](img/e6fae3968390fd4937d61f114027be43_74.png)

![](img/e6fae3968390fd4937d61f114027be43_76.png)

以下是筛选股票池的具体操作：

![](img/e6fae3968390fd4937d61f114027be43_78.png)

![](img/e6fae3968390fd4937d61f114027be43_80.png)

![](img/e6fae3968390fd4937d61f114027be43_82.png)

1.  **设定筛选阈值**：我们计划选择因子值排名在前20%的股票（即因子值最小的那20%）。这可以通过计算分位数来实现。
    ```python
    threshold = factor_neutralized.quantile(0.2)
    ```

![](img/e6fae3968390fd4937d61f114027be43_84.png)

![](img/e6fae3968390fd4937d61f114027be43_86.png)

![](img/e6fae3968390fd4937d61f114027be43_88.png)

2.  **执行筛选条件**：根据计算出的阈值，筛选出因子值小于或等于该阈值的所有股票。
    ```python
    selected_stocks = factor_neutralized[factor_neutralized <= threshold].index.tolist()
    ```

![](img/e6fae3968390fd4937d61f114027be43_90.png)

![](img/e6fae3968390fd4937d61f114027be43_92.png)

3.  **存储筛选结果**：将筛选出的股票列表存储到上下文变量中，供后续的交易逻辑使用。
    ```python
    context.stock_list = selected_stocks
    ```

![](img/e6fae3968390fd4937d61f114027be43_94.png)

![](img/e6fae3968390fd4937d61f114027be43_96.png)

![](img/e6fae3968390fd4937d61f114027be43_98.png)

这样，我们就得到了一个基于特定因子（市净率）筛选出的目标股票池。

![](img/e6fae3968390fd4937d61f114027be43_100.png)

![](img/e6fae3968390fd4937d61f114027be43_102.png)

![](img/e6fae3968390fd4937d61f114027be43_104.png)

---

![](img/e6fae3968390fd4937d61f114027be43_106.png)

![](img/e6fae3968390fd4937d61f114027be43_108.png)

![](img/e6fae3968390fd4937d61f114027be43_110.png)

## 准备调仓：识别需卖出的股票

![](img/e6fae3968390fd4937d61f114027be43_112.png)

![](img/e6fae3968390fd4937d61f114027be43_114.png)

![](img/e6fae3968390fd4937d61f114027be43_116.png)

在得到新的目标股票池后，我们需要对比当前持仓，以决定哪些股票需要卖出（即不在新股票池中的持仓股）。

![](img/e6fae3968390fd4937d61f114027be43_118.png)

![](img/e6fae3968390fd4937d61f114027be43_120.png)

以下是识别需卖出股票的逻辑：

![](img/e6fae3968390fd4937d61f114027be43_122.png)

![](img/e6fae3968390fd4937d61f114027be43_124.png)

![](img/e6fae3968390fd4937d61f114027be43_126.png)

1.  **获取当前持仓**：首先，从交易上下文中获取当前账户持有的所有股票。
    ```python
    current_holdings = context.portfolio.positions.keys()
    ```

![](img/e6fae3968390fd4937d61f114027be43_128.png)

![](img/e6fae3968390fd4937d61f114027be43_130.png)

2.  **计算持仓差异**：然后，找出那些在当前持仓中，但不在新目标股票池里的股票。这些股票就是我们需要考虑卖出的对象。
    ```python
    stocks_to_sell = set(current_holdings) - set(selected_stocks)
    ```

![](img/e6fae3968390fd4937d61f114027be43_132.png)

![](img/e6fae3968390fd4937d61f114027be43_134.png)

通过这一步，我们明确了需要从投资组合中移除的股票列表，为下一步执行买卖交易指令做好了准备。

![](img/e6fae3968390fd4937d61f114027be43_136.png)

![](img/e6fae3968390fd4937d61f114027be43_138.png)

---

![](img/e6fae3968390fd4937d61f114027be43_140.png)

![](img/e6fae3968390fd4937d61f114027be43_142.png)

![](img/e6fae3968390fd4937d61f114027be43_144.png)

本节课中我们一起学习了股票池筛选的完整流程。我们首先整合了数据预处理的三个步骤（去极值、标准化、中性化），然后基于处理后的因子值设定了筛选条件，得到了目标股票池。最后，我们通过对比新股票池与当前持仓，识别出了需要卖出的股票。下一节，我们将在此基础上，编写具体的交易逻辑来完成调仓操作。