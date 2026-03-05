# Python金融分析量化交易：P25：股票池筛选 📊

![](img/70f891ab1de44ebcb81540b30fdb791d_1.png)

## 概述
在本节课中，我们将学习如何构建一个股票池筛选策略。我们将使用一个量化交易框架，从沪深300指数成分股中，根据财务指标（如营业收入）筛选出表现最佳的股票，并限制数量，为后续的交易决策做准备。

![](img/70f891ab1de44ebcb81540b30fdb791d_3.png)

上一节我们介绍了量化交易的基本框架和回测设置，本节中我们来看看如何实现一个具体的股票筛选逻辑。

## 策略初始化与股票池设定
首先，在策略的构造函数中，我们需要定义我们的初始股票池。这里我们选择沪深300指数作为我们的备选股票池。可以使用指数名称或代码来指定。

![](img/70f891ab1de44ebcb81540b30fdb791d_5.png)

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    set_universe('沪深300')
```

![](img/70f891ab1de44ebcb81540b30fdb791d_7.png)

![](img/70f891ab1de44ebcb81540b30fdb791d_9.png)

打印信息的代码可以移除，以保持代码简洁。设定好股票池后，接下来我们需要编写预处理函数来获取每日的财务数据。

![](img/70f891ab1de44ebcb81540b30fdb791d_11.png)

![](img/70f891ab1de44ebcb81540b30fdb791d_13.png)

## 数据查询与预处理
在每日交易开始前，我们需要获取股票的财务数据并进行筛选。这通过框架提供的 `get_fundamentals` 函数实现。该函数允许我们查询各种财务指标，并使用过滤和排序条件来精炼结果。

以下是使用 `get_fundamentals` 函数的核心步骤：
1.  **构建查询（query）**：指定要查询的财务指标字段。
2.  **应用过滤（filter）**：限定查询范围在我们的股票池内。
3.  **进行排序（order_by）**：根据指标对结果进行排序（如降序）。
4.  **限制数量（limit）**：只保留排名前N的股票。

为了正确使用该函数，必须参考官方帮助文档，其中列出了所有可用的查询字段和函数用法。

## 实现筛选逻辑
现在，我们将上述步骤转化为代码。在 `before_trading` 函数中执行筛选。

```python
def before_trading(context):
    # 构建查询，获取营业收入指标
    q = query(
        fundamentals.eod_derivative_indicator.operating_revenue
    ).filter(
        # 过滤条件：股票代码必须在我们的股票池中
        fundamentals.eod_derivative_indicator.code.in_(context.universe)
    ).order_by(
        # 按营业收入降序排列，选出营收最高的股票
        fundamentals.eod_derivative_indicator.operating_revenue.desc()
    ).limit(
        # 限制只取前10名
        10
    )
    
    # 执行查询，将结果存入context中供后续使用
    context.fundamental_df = get_fundamentals(q)
    
    # 打印结果以供检查（实际运行时可以注释掉）
    print(context.fundamental_df)
```

![](img/70f891ab1de44ebcb81540b30fdb791d_15.png)

这段代码首先查询所有股票的营业收入，然后过滤出属于沪深300成分股的股票，接着按营业收入从高到低排序，最后只选取排名前十的股票，并将结果存储在 `context.fundamental_df` 中。

![](img/70f891ab1de44ebcb81540b30fdb791d_17.png)

![](img/70f891ab1de44ebcb81540b30fdb791d_19.png)

## 调试与验证
编写完代码后，运行回测以验证筛选逻辑是否正确。如果打印出的 `DataFrame` 为空，需要检查问题。

![](img/70f891ab1de44ebcb81540b30fdb791d_21.png)

![](img/70f891ab1de44ebcb81540b30fdb791d_23.png)

![](img/70f891ab1de44ebcb81540b30fdb791d_25.png)

一个常见错误是股票池设置不正确。确保在 `initialize` 函数中正确设置了指数。例如，使用 `set_universe(‘000300.XSHG’)` 或正确的指数名称。

```python
def initialize(context):
    # 使用指数代码精确指定沪深300
    set_universe('000300.XSHG')
```

![](img/70f891ab1de44ebcb81540b30fdb791d_27.png)

![](img/70f891ab1de44ebcb81540b30fdb791d_29.png)

修正后再次运行，观察输出日志。如果成功，日志将显示每个交易日筛选出的前十只股票及其营业收入数据。

![](img/70f891ab1de44ebcb81540b30fdb791d_31.png)

![](img/70f891ab1de44ebcb81540b30fdb791d_33.png)

## 总结
本节课中我们一起学习了如何实现一个基础的股票池筛选策略。我们掌握了以下关键点：
1.  在策略初始化时设定初始股票池。
2.  使用 `get_fundamentals` 函数查询财务数据。
3.  通过 `query().filter().order_by().limit()` 链式调用构建复杂的筛选、排序和限制逻辑。
4.  将筛选结果存储在上下文（`context`）中，为下一步的交易信号生成做好准备。

![](img/70f891ab1de44ebcb81540b30fdb791d_35.png)

![](img/70f891ab1de44ebcb81540b30fdb791d_37.png)

这个过程本质上是数据挖掘，从海量金融数据中提取出符合我们投资逻辑的标的。掌握了这个方法，你就可以尝试使用更多元化的指标（如市盈率、净利润增长率等）来构建更复杂的筛选策略。