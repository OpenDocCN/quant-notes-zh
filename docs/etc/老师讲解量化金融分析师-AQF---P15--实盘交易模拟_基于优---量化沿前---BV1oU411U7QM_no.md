# 量化金融专业知识与实务：P15：优矿框架_Context对象用法 📚

![](img/b1fea7e9c5c929d3b6899dc625faa266_0.png)

![](img/b1fea7e9c5c929d3b6899dc625faa266_2.png)

![](img/b1fea7e9c5c929d3b6899dc625faa266_4.png)

![](img/b1fea7e9c5c929d3b6899dc625faa266_6.png)

在本节课中，我们将要学习优矿（Uqer）量化平台中一个至关重要的核心对象——`Context`。`Context`对象代表了策略运行的整个环境，包含了时间、数据、账户信息等关键内容。理解并掌握`Context`的用法，是编写和读懂优矿策略代码的基础。

![](img/b1fea7e9c5c929d3b6899dc625faa266_8.png)

上一节我们介绍了策略的基本框架，本节中我们来看看如何通过`Context`对象来获取策略运行时的各种信息。

## 概述：什么是Context对象？ 🧩

![](img/b1fea7e9c5c929d3b6899dc625faa266_10.png)

![](img/b1fea7e9c5c929d3b6899dc625faa266_12.png)

`Context`对象表示策略的运行环境。它内部存储了策略的运行时间、行情数据等内容，还可以保存策略运行过程中产生的临时数据。策略框架在启动时会创建`Context`类的一个实例，并以参数形式传递给`initialize`（初始化函数）和`handle_data`（策略主逻辑函数）。

对于我们来说，`Context`对象是策略逻辑与平台数据进行交互的主要桥梁。

## Context对象的属性 📊

![](img/b1fea7e9c5c929d3b6899dc625faa266_14.png)

属性是对象静态的特征。以下是`Context`对象中几个重要的属性：

*   **`context.now`**: 返回策略运行的当前时间点（精确到分钟）。
*   **`context.current_date`**: 返回策略运行的当前日期。
*   **`context.previous_date`**: 返回上一个交易日。**这个属性非常常用**，因为在回测中，我们通常无法获取当天（尚未收盘）的数据，需要获取前一日的数据进行计算。

## Context对象的方法 ⚙️

方法是对象动态的功能。以下是`Context`对象中几个核心的方法：

以下是获取账户和证券池的方法：

*   **`context.get_account(account_name)`**: 此方法用于获取指定名称的交易账户实例。在优矿的策略模板中，通常已默认写好，例如 `account = context.get_account(‘fantasy_account’)`。通过这个实例，我们可以查询持仓、资金、进行下单等操作。
*   **`context.get_universe(exclude_halt=False)`**: 此方法用于获取在回测当天，满足策略定义的`universe`（证券池）条件的资产列表。参数`exclude_halt`用于决定是否剔除停牌的股票（`True`为剔除）。**该方法返回一个股票代码的列表（List）**。

![](img/b1fea7e9c5c929d3b6899dc625faa266_16.png)

![](img/b1fea7e9c5c929d3b6899dc625faa266_18.png)

以下是获取历史数据的方法，这是最常用且功能强大的接口：

*   **`context.history(symbol, attribute, time_range, frequency=’1d’, rtype=’frame’, style=’sat’)`**: 此方法用于获取历史行情或因子数据。
    *   `symbol`: 股票代码或代码列表。
    *   `attribute`: 需要获取的数据属性，如 `’close’`（收盘价）、`’volume’`（成交量）。可以是单个字符串或字符串列表。
    *   `time_range`: 获取历史数据的长度（例如，`10`表示获取最近10个周期）。
    *   `frequency`: 数据频率，如 `’1d’`（日线）、`’1m’`（1分钟线）。
    *   `rtype`: 返回数据类型，`’frame’` 返回Pandas DataFrame，`’array’` 返回NumPy ndarray。
    *   `style`: **关键参数**，决定返回数据结构的组织形式。它由 `’s’`（symbol）、`’a’`（attribute）、`’t’`（time）三个字母排列组合，分别代表字典的键、DataFrame的列名和索引。

## 核心概念详解：history方法的style参数 🔍

`context.history()`方法的`style`参数是理解其返回数据结构的关键。它决定了返回的字典中，什么是键（key），以及对应的DataFrame中，什么是列（columns）和索引（index）。

*   **`style=’ast’`**: 返回的字典，其 **键（key）是属性（attribute）**。通过`data[‘close’]`可以拿到一个DataFrame，这个DataFrame的**列（columns）是股票代码（symbol）**，**索引（index）是时间（time）**。这种格式便于同时分析多只股票的同一指标。
*   **`style=’sat’`**: 返回的字典，其 **键（key）是股票代码（symbol）**。通过`data[‘000001.XSHE’]`可以拿到一个DataFrame，这个DataFrame的**列（columns）是属性（attribute）**，**索引（index）是时间（time）**。这种格式便于分析单只股票的多个指标。
*   **`style=’tas’`**: 返回的字典，其 **键（key）是时间（time）**。这种格式使用相对较少。

**示例代码**:
假设我们要获取股票`’000001.XSHE’`过去10天的收盘价，并以`’sat’`格式返回。
```python
data = context.history(symbol=’000001.XSHE’, attribute=’close’, time_range=10, frequency=’1d’, rtype=’frame’, style=’sat’)
# 此时 data 是一个字典，我们可以通过股票代码获取对应的DataFrame
price_df = data[‘000001.XSHE’]
print(price_df.head())
```

![](img/b1fea7e9c5c929d3b6899dc625faa266_20.png)

## 实战应用：小市值策略思路 💡

理解了`get_universe`和`history`方法后，我们可以构思一个简单的小市值策略。

1.  **定义动态证券池**：在`initialize`中，将`universe`设置为动态全A股，或每月末市值最小的N只股票。
2.  **在`handle_data`中获取当前证券池**：
    ```python
    current_universe = context.get_universe(exclude_halt=True)
    ```
3.  **遍历证券池进行交易**：因为`current_universe`是一个股票代码列表，我们可以用循环处理每只股票。
    ```python
    for stock in current_universe:
        # 这里可以添加你的选股或交易逻辑
        # 例如：order(stock, 10000) # 每只股票买入10000股
        pass
    ```
通过这种方式，一个基础的小市值轮动策略框架就搭建完成了。

## 总结 📝

![](img/b1fea7e9c5c929d3b6899dc625faa266_22.png)

本节课中我们一起学习了优矿框架的核心——`Context`对象。我们掌握了：
1.  `Context`对象作为策略运行环境容器的概念。
2.  如何使用`context.previous_date`等属性获取时间信息。
3.  如何使用`context.get_account()`获取账户实例，以及使用`context.get_universe()`获取动态证券池。
4.  **重点**是深入理解了`context.history()`方法，特别是`style`参数如何影响返回数据的结构（`’ast’`、`’sat’`、`’tas’`）。这是后续策略开发中数据调取的基石。

![](img/b1fea7e9c5c929d3b6899dc625faa266_24.png)

![](img/b1fea7e9c5c929d3b6899dc625faa266_25.png)

`Context`对象是与优矿平台交互的核心，熟练掌握其属性和方法，尤其是数据获取功能，是编写有效量化策略的关键一步。下一节课，我们将学习另一个核心对象——`Account`（账户），了解如何管理持仓和进行交易。