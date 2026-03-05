# Python金融分析与量化交易实战教程：P25：股票池筛选 📊

## 概述
在本节课中，我们将学习如何在一个量化交易策略中，根据财务指标对股票池进行筛选和排序。我们将以沪深300指数成分股为初始股票池，通过查询其财务数据（如营业收入），筛选出排名靠前的股票，为后续的交易决策做准备。

## 代码实现步骤

![](img/1583583d6d0133a2873226caa86c23be_1.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何实现股票池的筛选逻辑。

![](img/1583583d6d0133a2873226caa86c23be_2.png)

![](img/1583583d6d0133a2873226caa86c23be_3.png)

首先，在策略的构造函数中，我们需要定义我们的初始股票池。这里我们选择沪深300指数。

![](img/1583583d6d0133a2873226caa86c23be_5.png)

```python
# 在策略的 __init__ 函数中定义股票池
self.stock_pool = ['沪深300']  # 也可以使用指数代码，如 '000300.SH'
```

接下来，在策略的预处理函数中，我们需要获取并筛选股票。以下是实现这一过程的关键步骤。

### 1. 查询财务数据
我们需要使用平台提供的API来查询所需的财务指标。所有可用的指标都在官方帮助文档中列出。

![](img/1583583d6d0133a2873226caa86c23be_7.png)

以下是查询财务数据（例如营业收入）的函数调用示例：

```python
# 导入必要的查询模块
from jqdata import query, valuation

![](img/1583583d6d0133a2873226caa86c23be_9.png)

![](img/1583583d6d0133a2873226caa86c23be_10.png)

# 构建查询
q = query(valuation.code, valuation.operating_revenue)
```

### 2. 应用过滤条件
查询构建好后，我们需要限定查询范围，只在我们关心的股票池（即沪深300成分股）内进行。

```python
# 应用过滤条件，筛选出属于沪深300指数的股票
q = q.filter(valuation.code.in_(self.stock_pool))
```

### 3. 对结果进行排序
获取数据后，我们通常需要根据某个指标进行排序，以选出“最好”的股票。这里我们按营业收入降序排列。

```python
# 按营业收入降序排序
q = q.order_by(valuation.operating_revenue.desc())
```

### 4. 限制返回数量
最后，我们可能不需要所有股票，只取排名前N的股票作为最终的投资候选池。

```python
# 限制只返回前10只股票
q = q.limit(10)
```

### 5. 执行查询并存储结果
将以上步骤组合起来，执行查询，并将结果存储在策略的上下文（context）中，供后续函数使用。

![](img/1583583d6d0133a2873226caa86c23be_12.png)

```python
# 执行查询，返回一个DataFrame
df = get_fundamentals(q)

![](img/1583583d6d0133a2873226caa86c23be_13.png)

![](img/1583583d6d0133a2873226caa86c23be_14.png)

# 将结果存入context
context.fundamental_df = df

![](img/1583583d6d0133a2873226caa86c23be_16.png)

![](img/1583583d6d0133a2873226caa86c23be_17.png)

# 打印结果进行检查
print(df)
```

## 完整代码片段与调试
将上述步骤整合到策略的 `before_trading_start` 函数中，其结构如下：

```python
def before_trading_start(context):
    # 1. 构建查询
    q = query(valuation.code, valuation.operating_revenue)
    
    # 2. 过滤股票池
    q = q.filter(valuation.code.in_(context.stock_pool))
    
    # 3. 排序
    q = q.order_by(valuation.operating_revenue.desc())
    
    # 4. 限制数量
    q = q.limit(10)
    
    # 5. 执行并存储
    context.fundamental_df = get_fundamentals(q)
    print(context.fundamental_df)
```

![](img/1583583d6d0133a2873226caa86c23be_19.png)

运行回测时，如果发现打印的结果为空，需要检查股票池的定义是否正确。确保在查询过滤时，使用的是指数成分股的正确标识。例如，`‘沪深300’` 需要确保是平台API认可的指数名称。可以在帮助文档的“指数”部分查找准确名称。

![](img/1583583d6d0133a2873226caa86c23be_21.png)

修正股票池定义后，重新运行回测，即可在日志中看到每天筛选出的前10只股票及其营业收入数据。

![](img/1583583d6d0133a2873226caa86c23be_22.png)

## 总结
本节课中我们一起学习了量化策略中股票池筛选的核心流程。我们掌握了如何：
1.  在策略中定义初始股票池。
2.  使用 `query()` 和 `get_fundamentals()` 查询财务数据。
3.  利用 `filter()`、`order_by()` 和 `limit()` 对查询结果进行过滤、排序和数量限制。
4.  将筛选结果存储起来，为下一步的交易逻辑提供数据基础。

![](img/1583583d6d0133a2873226caa86c23be_24.png)

这个过程是构建许多量化选股策略的基石，通过修改查询的指标和过滤条件，你可以实现各种各样的筛选规则。