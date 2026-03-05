# Python金融时间序列分析与量化交易实战教程：P22：21.股票池筛选 📊

![](img/46c9d81c7833158599eaa61039f2b484_1.png)

在本节课中，我们将学习如何构建一个股票池筛选策略。我们将使用财务数据作为筛选指标，从沪深300指数成分股中，根据营业收入筛选出排名靠前的股票，为后续的交易决策做准备。

上一节我们介绍了策略的基本框架，本节中我们来看看如何实现具体的股票筛选逻辑。

## 初始化股票池

![](img/46c9d81c7833158599eaa61039f2b484_3.png)

![](img/46c9d81c7833158599eaa61039f2b484_5.png)

首先，我们需要在策略的构造函数中定义我们的初始股票池。这里我们选择沪深300指数作为我们的备选股票池。

![](img/46c9d81c7833158599eaa61039f2b484_7.png)

```python
def initialize(context):
    # 设定初始股票池为沪深300指数成分股
    context.stock_pool = ['000300.XSHG']  # 沪深300指数代码
    # 或者使用指数名称
    # context.stock_pool = get_index_stocks('000300.XSHG')
```

![](img/46c9d81c7833158599eaa61039f2b484_9.png)

## 数据预处理与筛选

在每日交易开始前，我们需要获取股票的财务数据并进行筛选。以下是实现这一功能的核心步骤。

首先，我们需要使用平台提供的`query`功能来查询财务指标。这里我们以“营业总收入”为例进行筛选。

```python
def before_trading_start(context):
    # 构建查询语句
    q = query(
        # 选择要查询的财务指标字段，例如：营业总收入
        fundamentals.financial_indicator.operating_revenue
    ).filter(
        # 设置过滤条件：股票代码必须在我们的股票池中
        fundamentals.financial_indicator.code.in_(context.stock_pool)
    ).order_by(
        # 按营业总收入降序排列，选出营收最高的股票
        fundamentals.financial_indicator.operating_revenue.desc()
    ).limit(
        # 限制只取前10名
        10
    )
```

![](img/46c9d81c7833158599eaa61039f2b484_11.png)

![](img/46c9d81c7833158599eaa61039f2b484_13.png)

接下来，我们执行查询，并将结果存储在`context`中，以便在交易函数中使用。

![](img/46c9d81c7833158599eaa61039f2b484_15.png)

![](img/46c9d81c7833158599eaa61039f2b484_17.png)

![](img/46c9d81c7833158599eaa61039f2b484_19.png)

![](img/46c9d81c7833158599eaa61039f2b484_21.png)

```python
    # 执行查询，获取DataFrame格式的结果
    df = get_fundamentals(q)
    
    # 将筛选出的股票列表存入context
    context.filtered_stocks = df['code'].tolist()
    
    # 打印结果以供检查（实际运行时可以注释掉）
    print(df)
```

![](img/46c9d81c7833158599eaa61039f2b484_23.png)

![](img/46c9d81c7833158599eaa61039f2b484_25.png)

## 策略回测与验证

完成代码编写后，我们需要进行回测，以验证筛选逻辑是否正确工作。

![](img/46c9d81c7833158599eaa61039f2b484_27.png)

![](img/46c9d81c7833158599eaa61039f2b484_29.png)

在回测日志中，我们应该能看到每天筛选出的股票列表。如果结果为空，请检查股票池的代码或指数名称是否正确。确保在`filter`条件中正确引用了指数成分股。

![](img/46c9d81c7833158599eaa61039f2b484_31.png)

![](img/46c9d81c7833158599eaa61039f2b484_33.png)

本节课中我们一起学习了如何利用财务数据对股票池进行动态筛选。我们掌握了使用`query`函数查询特定指标、使用`filter`进行条件过滤、使用`order_by`进行排序以及使用`limit`限制结果数量的方法。这个筛选出的股票列表为后续制定买入卖出策略提供了基础。