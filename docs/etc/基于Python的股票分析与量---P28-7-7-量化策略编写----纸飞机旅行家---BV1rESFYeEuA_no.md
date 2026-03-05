# 基于Python的股票分析与量化交易入门到实践 - P28：7.7 量化策略编写-Python量化交易编程第一步：账户信息 📊

在本节中，我们将学习量化交易策略中至关重要的“账户信息”。我们将深入理解两个核心对象：`Portfolio`（总账户）和`SubPortfolio`（子账户），并通过代码实战来查看它们的属性和数据。

上一节我们介绍了策略信息，本节中我们来看看账户信息。账户信息有两个非常重要的对象：`Portfolio`对象，它存放总账户的信息；另一个是`SubPortfolio`对象，它存放子账户信息。介绍完这两个对象后，我们将进入代码实战环节。

## Portfolio对象：总账户信息 💼

`Portfolio`对象代表整个策略下的总账户信息。它包含以下重要属性：

以下是`Portfolio`对象的核心属性列表：
*   **`long_positions`**：这是一个字典（Dictionary），`key`是证券代码，`value`是`Position`对象，代表多单的仓位。
*   **`short_positions`**：与`long_positions`类似，也是一个字典，代表空单的仓位。
*   **`total_value`**：总权益，包括现金、期货、保证金、股票等所有资产的总价值。主要用于计算收益。
*   **`returns`**：累计收益。其计算公式为：`(当前总资产 + 今日出入金 - 昨日总资产) / 昨日总资产`。这个值反映了总资产相对于前一日的增长比例。
*   **`starting_cash`**：初始资金。
*   **`positions_value`**：持仓的总价值。

## SubPortfolio对象：子账户信息 🧾

`SubPortfolio`对象代表子账户信息。它主要关注与资金流动相关的属性。

以下是`SubPortfolio`对象的核心属性列表：
*   **`inout_cash`**：累积出入金。其算法是简单的加减法，例如初始资金1000，转出100后为900，再转入100则为1100。
*   **`available_cash`**：可用资金。
*   **`transferable_cash`**：可取资金，即可提现的现金。注意，当日卖出证券所得的资金可能不包含在内，具体取决于证券类型（如基金可能需要T+1日才能取现）。
*   **`locked_cash`**：挂单锁住的资金。挂单后，这部分资金会被锁定，直到交易完成或失败才会返回账户。
*   **`type`**：账户所属类型，例如股票（`stock`）、基金、债券等。

## 代码实战环节 💻

接下来我们进入代码实战环节，通过实际代码来查看这些账户信息。

![](img/bf3f6ca658339622ed44f8eb9d232f31_1.png)

### 实战一：查看Portfolio对象信息

首先，我们介绍`Portfolio`对象的一些代码实现。如果没有持仓，我们先进行买卖操作。

```python
# 打印Portfolio对象的核心信息
print(“多单仓位:”, context.portfolio.long_positions)
print(“空单仓位:”, context.portfolio.short_positions)
print(“总权益:”, context.portfolio.total_value)
print(“累计收益:”, context.portfolio.returns)
print(“初始资金:”, context.portfolio.starting_cash)
print(“持仓价值:”, context.portfolio.positions_value)
```

编译运行后，可以看到类似以下结果：
*   多单仓位：`{‘000001.XSHE’: }`
*   空单仓位：`{}` （在A股市场通常为空）
*   总权益：`1000000.0`
*   累计收益：`0.05`
*   初始资金：`1000000.0`
*   持仓价值：`50000.0`

![](img/bf3f6ca658339622ed44f8eb9d232f31_3.png)

以上是总账户信息`Portfolio`对象的代码实战。

### 实战二：查看SubPortfolio对象信息

接下来我们介绍子账户对象`SubPortfolio`的代码实现。

```python
# 打印SubPortfolio对象的核心信息
print(“累积出入金:”, context.portfolio.subportfolios[0].inout_cash)
print(“可用资金:”, context.portfolio.subportfolios[0].available_cash)
print(“可取资金:”, context.portfolio.subportfolios[0].transferable_cash)
print(“挂单锁住资金:”, context.portfolio.subportfolios[0].locked_cash)
print(“账户所属类型:”, context.portfolio.subportfolios[0].type)
```

运行后，可以看到类似以下结果：
*   累积出入金：`0.0`
*   可用资金：`950000.0`
*   可取资金：`950000.0`
*   挂单锁住资金：`0.0`
*   账户所属类型：`stock`

![](img/bf3f6ca658339622ed44f8eb9d232f31_5.png)

可以看到，`Portfolio`对象主要关联证券持仓和权益，而`SubPortfolio`对象的属性则更多与账户资金本身的各种统计维度相关。代码实战部分到此完成。

## 本节总结 📝

本节课中我们一起学习了量化交易中的账户信息。本节主要介绍了两个最重要的账户对象：
1.  **`Portfolio`对象**：存储总账户信息，其属性主要与账户持仓相关，例如`long_positions`、`short_positions`，同时也包括`returns`（累计收益）、`total_value`（总权益）等。
2.  **`SubPortfolio`对象**：存储子账户信息，其属性主要与资金流动相关，例如`inout_cash`（累积出入金）、`available_cash`（可用资金）、`transferable_cash`（可取资金）等。

后面我们通过代码实战演示了如何访问和查看这些信息。感兴趣的同学可以回看视频内容或查看源码以加深理解。以上便是本节的全部内容。

![](img/bf3f6ca658339622ed44f8eb9d232f31_7.png)

我是米铁。