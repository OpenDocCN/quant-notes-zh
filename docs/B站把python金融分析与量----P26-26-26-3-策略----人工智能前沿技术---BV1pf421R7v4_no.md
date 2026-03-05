# Python金融量化与股票交易：P26：策略效果演示与指标分析 📊

在本节课中，我们将学习如何对量化交易策略进行效果演示与指标分析。我们将通过一个具体的例子，展示如何调整数据结构、执行买卖操作，并最终解读策略回测报告中的关键指标。

![](img/2f16128045badd6f473bd3203bdd1891_1.png)

---

## 数据结构的调整与优化

上一节我们介绍了如何筛选股票。本节中我们来看看如何优化数据的展示方式。

![](img/2f16128045badd6f473bd3203bdd1891_3.png)

我们最初的想法是将数据横向排列，即每个股票名称作为一列。但通常大家更喜欢纵向排列数据，即每个股票名称作为一行，其财务指标紧随其后。

![](img/2f16128045badd6f473bd3203bdd1891_5.png)

![](img/2f16128045badd6f473bd3203bdd1891_1.png)

![](img/2f16128045badd6f473bd3203bdd1891_7.png)

因此，我们需要对结果进行修改。在执行结果后，我们将最终结果进行调整。对DataFrame进行操作，其实只需要进行一次转置即可。

![](img/2f16128045badd6f473bd3203bdd1891_9.png)

![](img/2f16128045badd6f473bd3203bdd1891_11.png)

以下是实现转置的代码：

```python
# 对DataFrame进行转置操作
transposed_result = original_dataframe.T
```

打印转置后的结果以验证是否正确。如果正确，我们将继续后续步骤。转置完成后，工作并未结束。

![](img/2f16128045badd6f473bd3203bdd1891_13.png)

在`before_trading`函数中，我们的最终目的不是仅仅打印数据，而是获取筛选出的股票列表。最终我们想要的结果是对数据先进行转置。

![](img/2f16128045badd6f473bd3203bdd1891_15.png)

![](img/2f16128045badd6f473bd3203bdd1891_3.png)

![](img/2f16128045badd6f473bd3203bdd1891_17.png)

转置后的结果显示为纵向排列。

![](img/2f16128045badd6f473bd3203bdd1891_19.png)

![](img/2f16128045badd6f473bd3203bdd1891_5.png)

![](img/2f16128045badd6f473bd3203bdd1891_21.png)

这样，每一行就是一个股票名称及其对应的指标值。第一列是索引（index），后面是实际的值。

![](img/2f16128045badd6f473bd3203bdd1891_23.png)

![](img/2f16128045badd6f473bd3203bdd1891_7.png)

![](img/2f16128045badd6f473bd3203bdd1891_25.png)

![](img/2f16128045badd6f473bd3203bdd1891_27.png)

此时，我们只需要获取索引（即股票名称）即可。

![](img/2f16128045badd6f473bd3203bdd1891_28.png)

![](img/2f16128045badd6f473bd3203bdd1891_30.png)

![](img/2f16128045badd6f473bd3203bdd1891_9.png)

![](img/2f16128045badd6f473bd3203bdd1891_32.png)

由于间隔太短，可能看不出明显变化。我们暂时不管，稍后会在策略分析中处理。在这里，我们将索引（股票名称）获取并重新设置，存入`context`中，作为后续交易要返回的结果。

![](img/2f16128045badd6f473bd3203bdd1891_34.png)

```python
# 获取股票名称列表并存入context
context.stock_list = transposed_result.index.tolist()
```

![](img/2f16128045badd6f473bd3203bdd1891_36.png)

![](img/2f16128045badd6f473bd3203bdd1891_13.png)

![](img/2f16128045badd6f473bd3203bdd1891_38.png)

这样，在`before_trading`阶段，我们就完成了从沪深300中筛选出十只股票的工作。后续的打印语句可以删除。

![](img/2f16128045badd6f473bd3203bdd1891_40.png)

![](img/2f16128045badd6f473bd3203bdd1891_42.png)

![](img/2f16128045badd6f473bd3203bdd1891_15.png)

![](img/2f16128045badd6f473bd3203bdd1891_44.png)

---

![](img/2f16128045badd6f473bd3203bdd1891_46.png)

## 实现交易逻辑（handle_bar）

![](img/2f16128045badd6f473bd3203bdd1891_48.png)

接下来，我们进入`handle_bar`函数，实现具体的交易逻辑。我们不再使用`pass`占位符。

![](img/2f16128045badd6f473bd3203bdd1891_50.png)

![](img/2f16128045badd6f473bd3203bdd1891_52.png)

首先，我们需要进行判断：当前账户中是否持有股票。我们将参考API文档来编写代码。

![](img/2f16128045badd6f473bd3203bdd1891_54.png)

![](img/2f16128045badd6f473bd3203bdd1891_21.png)

API文档中包含了丰富的解释。我们需要查找`context`的相关属性。

![](img/2f16128045badd6f473bd3203bdd1891_25.png)

![](img/2f16128045badd6f473bd3203bdd1891_56.png)

在交易相关的部分，可以找到`context`的信息。其中，`context.portfolio.positions`是一个包含所有持仓信息的字典，描述了当前持有的股票。

![](img/2f16128045badd6f473bd3203bdd1891_58.png)

![](img/2f16128045badd6f473bd3203bdd1891_44.png)

以下是实现交易逻辑的步骤：

![](img/2f16128045badd6f473bd3203bdd1891_60.png)

1.  **判断持仓状态**：检查当前持仓字典是否为空。如果为空，说明是首次运行，需要买入股票。
2.  **执行卖出操作**：如果持仓不为空，则需要遍历当前持有的股票。如果某只股票不在当前筛选出的股票池中，则将其卖出。
3.  **执行买入操作**：遍历当前筛选出的股票池。如果某只股票不在当前持仓中，则将其买入。

![](img/2f16128045badd6f473bd3203bdd1891_62.png)

以下是具体的代码实现：

![](img/2f16128045badd6f473bd3203bdd1891_64.png)

```python
def handle_bar(context):
    # 获取当前持仓字典
    current_positions = context.portfolio.positions

    # 判断持仓是否为空
    if not current_positions:
        # 首次运行，执行买入逻辑（此处略，将在后续补充）
        pass
    else:
        # 持仓不为空，执行调仓逻辑

        # 卖出不在股票池中的股票
        for stock in current_positions.keys():
            if stock not in context.stock_list:
                # 使用order_target_percent函数卖出全部持仓
                order_target_percent(stock, 0)

        # 买入股票池中的股票（平均分配资金）
        for stock in context.stock_list:
            # 计算每只股票应占的资金比例（平均分配）
            target_percent = 1.0 / len(context.stock_list)
            order_target_percent(stock, target_percent)
```

![](img/2f16128045badd6f473bd3203bdd1891_66.png)

![](img/2f16128045badd6f473bd3203bdd1891_68.png)

在买入操作中，我们采用了简单的平均分配资金策略。您也可以根据财务数据或其他指标动态调整买入比例。

![](img/2f16128045badd6f473bd3203bdd1891_78.png)

代码编写完成后，我们运行回测，查看结果。

![](img/2f16128045badd6f473bd3203bdd1891_70.png)

---

## 策略回测结果分析

运行策略后，我们会得到一份回测报告。报告可能显示亏损，这可能与所选回测时间段有关。

![](img/2f16128045badd6f473bd3203bdd1891_72.png)

![](img/2f16128045badd6f473bd3203bdd1891_84.png)

![](img/2f16128045badd6f473bd3203bdd1891_74.png)

我们可以尝试更换回测时间区间再次验证。现在，我们来解读回测报告中的关键指标。

![](img/2f16128045badd6f473bd3203bdd1891_76.png)

报告主要分为几个部分：

1.  **收益概况**：对比策略收益与基准收益。
2.  **交易详情**：记录每一笔交易。

![](img/2f16128045badd6f473bd3203bdd1891_78.png)

以下是需要关注的核心指标：

![](img/2f16128045badd6f473bd3203bdd1891_80.png)

*   **年化收益率**：策略在一年内的平均收益率。图中显示策略收益与基准收益均为负，但策略亏损较少。
*   **最大回撤**：策略净值从高点回落的最大幅度。图中显示为17%左右，属于可控范围。
*   **夏普比率**：衡量单位风险所获得的超额回报。由于收益为负，夏普比率也为负，表示风险调整后收益不佳。

![](img/2f16128045badd6f473bd3203bdd1891_82.png)

![](img/2f16128045badd6f473bd3203bdd1891_88.png)

![](img/2f16128045badd6f473bd3203bdd1891_84.png)

此外，右侧的“交易详情”可以点开查看每一笔具体的买卖记录，用于分析交易频率和成本。

![](img/2f16128045badd6f473bd3203bdd1891_86.png)

---

## 总结

本节课中我们一起学习了量化交易策略的完整流程：

1.  **数据准备**：在`before_trading`中筛选股票并调整数据结构，将股票列表存入`context`。
2.  **交易逻辑**：在`handle_bar`中实现动态调仓逻辑，包括卖出不在池中的股票和买入新股票。
3.  **结果分析**：运行回测后，解读关键指标如年化收益率、最大回撤和夏普比率，以评估策略效果。

![](img/2f16128045badd6f473bd3203bdd1891_88.png)

通过本课的学习，您已经掌握了构建和初步评估一个简单量化策略的基本方法。后续可以尝试修改选股条件、资金分配方式或回测参数，以优化策略表现。