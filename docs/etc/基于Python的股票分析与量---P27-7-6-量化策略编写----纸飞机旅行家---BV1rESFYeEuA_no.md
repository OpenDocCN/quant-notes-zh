# 基于Python的股票分析与量化交易入门到实践 - P27：7.6 量化策略编写-Python量化交易编程第一步：策略信息 📊

在本节课中，我们将要学习量化策略中的核心信息对象：`context`对象和`position`对象。它们是策略编写的基础，用于存储和查询账户、持仓、时间等关键信息。

上一节我们介绍了交易函数与交易对象，本节中我们来看看策略信息相关的对象。

## `context`对象：策略信息总览

`context`对象是策略信息的总览，它存储了整个策略的核心信息，包括账户信息、时间信息、持仓信息等。

以下是`context`对象中一些常用的参数和属性：

*   **`subportfolios`**：它是一个数组，存储单个操作仓位的资金或标的信息。可以通过索引（如0, 1, 2...）进行遍历。
*   **`portfolio`**：这是你的账户信息，是上面`subportfolios`数组的汇总信息。
*   **`subportfolios[0]`**：这个属性单独指向数组的第一个元素。当你仅进行单个标的操作时，它等同于`portfolio`，否则它只是数组的一部分。
*   **`current_dt`**：当前单位时间的开始时间，是一个`datetime`对象。
*   **`previous_date`**：前一个交易日。请注意，这是一个`date`类型，而非`datetime`类型。在Python中处理时间格式时需要特别注意这一点。
*   **`universe`**：通过`set_universe`函数设定的股票池。这意味着`context`对象不仅可以操作单只股票，还可以操作多只股票。它本质上是一个数组。

以上是初学者可能会常用到的一些`context`对象属性。接下来，我们将通过代码实战让大家更深入地理解。

## `position`对象：持仓信息

![](img/b4cb28df147eb87d344c4162dc3f1034_1.png)

`position`对象代表持仓，即你持有的标的（如股票）的信息。

以下是`position`对象的一些关键属性：

*   **证券代码**：你持有的股票代码。
*   **最新行情价格**：因为股票价格实时变化，这个属性提供最新的价格。
*   **`total_amount`**：总仓位数量。**注意**：这不包括挂单冻结的仓位。
*   **`init_time`**：建仓时间，同样是`datetime`类型。

下面，我们进入代码实战环节，从代码层面观察这两个对象能返回哪些重要信息。

## 代码实战演示

### 1. 演示`context`对象

为了清晰地展示`context`对象的属性，我们使用一段简化的代码。

```python
def handle_data(context):
    # 设置初始账户总资产（此处仅为演示，通常由初始化函数设置）
    context.portfolio.starting_cash = 10000
    context.portfolio.cash = 10000

    # 输出账户总资产
    log.info(f‘账户总资产：{context.portfolio.total_value}‘)
    # 输出持仓金额
    log.info(f‘持仓金额：{context.portfolio.positions_value}‘)
    # 输出当前日期时间
    log.info(f‘当前日期：{context.current_dt.year}年{context.current_dt.month}月{context.current_dt.day}日‘)
    # 输出累计收益率
    log.info(f‘累计收益：{context.portfolio.returns}‘)
    # 获取subportfolios[0]的可用资金
    log.info(f‘可用资金：{context.subportfolios[0].available_cash}‘)
```
运行这段代码，可以输出每天的账户总资产、持仓金额（初始为0）、当前日期、累计收益（初始为0）以及可用资金（初始为10000）等信息。

### 2. 演示`position`对象

`position`对象与持仓相关，因此我们需要先进行下单操作。

```python
def handle_data(context):
    # 下单买入1000股某股票（例如‘000001.XSHE‘）
    order(‘000001.XSHE‘, 1000)

    # 获取该股票的持仓对象
    pos = context.portfolio.positions[‘000001.XSHE‘]

    # 查看position对象的类型
    log.info(f‘position对象类型：{type(pos)}‘)
    # 输出总仓位（股数）
    log.info(f‘总仓位（股数）：{pos.total_amount}‘)
    # 输出持仓价值
    log.info(f‘持仓价值：{pos.value}‘)
    # 输出建仓时间
    log.info(f‘建仓时间：{pos.init_time}‘)
```
运行这段代码后，可以查看到`position`对象实际上是一个字典结构，并打印出指定股票的总持股数、持仓价值以及建仓时间。

![](img/b4cb28df147eb87d344c4162dc3f1034_3.png)

---

本节课中我们一起学习了量化策略中的两个核心信息对象。

*   **`context`对象**是策略信息的总览，存储了账户、时间、股票池等策略运行所需的基本信息，是查询各种参数和数据的重要入口。
*   **`position`对象**则具体代表了持仓信息，可以快速查询你持有的标的、数量、价值及建仓时间等。

![](img/b4cb28df147eb87d344c4162dc3f1034_5.png)

通过本节的理论讲解和代码实战，你应该已经对如何在策略中获取和利用这些关键信息有了初步的了解。它们是构建更复杂量化策略的基石。