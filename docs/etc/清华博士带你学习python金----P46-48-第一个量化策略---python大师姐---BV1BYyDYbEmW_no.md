# Python金融量化投资：P46：第一个量化策略-2 📈

在本节课中，我们将学习量化策略回测框架的核心组成部分，包括如何获取数据以及如何执行买卖订单。我们将了解`initialize`和`handle_data`函数的作用，并掌握几个关键的API函数。

## 初始化与每日执行函数

上一节我们介绍了回测框架的基本结构。本节中我们来看看两个核心函数的具体作用。

`initialize`函数只在回测开始的第一天执行一次。我们可以通过打印语句来验证这一点。

![](img/abd133f52646acd318ef63faeff46751_1.png)

![](img/abd133f52646acd318ef63faeff46751_3.png)

```python
def initialize(context):
    print(context)  # 此打印语句仅在第一天出现一次
```

![](img/abd133f52646acd318ef63faeff46751_5.png)

![](img/abd133f52646acd318ef63faeff46751_7.png)

`handle_data`函数则会在**每个交易日**执行一次，用于编写每日的策略逻辑。

![](img/abd133f52646acd318ef63faeff46751_9.png)

```python
def handle_data(context):
    print("每日执行")  # 此打印语句在每个交易日都会出现
```

运行策略后，日志会显示该函数在每个交易日（如6月1日、2日、3日、6日等）都执行了一次，非交易日（如周末）则不执行。

![](img/abd133f52646acd318ef63faeff46751_11.png)

![](img/abd133f52646acd318ef63faeff46751_13.png)

![](img/abd133f52646acd318ef63faeff46751_15.png)

## 获取数据

在`handle_data`函数中，我们需要获取数据来制定交易决策。以下是获取数据的关键方法。

![](img/abd133f52646acd318ef63faeff46751_17.png)

### 获取当前数据

![](img/abd133f52646acd318ef63faeff46751_19.png)

`get_current_data`函数用于获取当前交易日的数据，返回一个字典对象。在按天回测的模式下，“最新价”通常等于当日的开盘价。

![](img/abd133f52646acd318ef63faeff46751_21.png)

```python
current_data = get_current_data(‘000001.XSHE’)
# 获取今日开盘价
open_price = current_data.day_open
```

![](img/abd133f52646acd318ef63faeff46751_23.png)

该函数返回的对象包含以下属性：
*   `last_price`：最新价（日回测下约等于开盘价）。
*   `high_limit`：涨停价。
*   `low_limit`：跌停价。
*   `paused`：是否停牌（True表示暂停交易）。
*   `is_st`：是否为ST股。
*   `day_open`：开盘价。
*   `name`：股票名称。

### 获取历史数据

`attribute_history`函数用于获取单只股票的历史数据，返回一个`pandas DataFrame`。

```python
# 获取股票‘000001.XSHE’过去5天的历史数据
hist_data = attribute_history(‘000001.XSHE’, count=5)
```

该`DataFrame`包含以下列：
*   `open`：开盘价
*   `close`：收盘价
*   `high`：最高价
*   `low`：最低价
*   `volume`：成交量（股数）
*   `money`：成交额（金额）

参数`count`指定获取最近几个交易日的数据。例如，`count=5`会返回截至当前交易日的前5个交易日的数据。

![](img/abd133f52646acd318ef63faeff46751_25.png)

## 执行交易订单

获取数据后，下一步是根据策略执行买卖操作。框架提供了多个下单函数。

以下是四个核心的下单函数：

![](img/abd133f52646acd318ef63faeff46751_27.png)

1.  **`order`：按股数下单**
    *   第一个参数：股票代码。
    *   第二个参数：买卖股数（正数为买入，负数为卖出）。
    *   注意：A股买入必须是100股的整数倍，系统会自动调整。全部卖出则无此限制。
    ```python
    # 买入100股
    order(‘000001.XSHE’, 100)
    # 卖出50股
    order(‘000001.XSHE’, -50)
    ```

![](img/abd133f52646acd318ef63faeff46751_29.png)

2.  **`order_value`：按金额下单**
    *   第一个参数：股票代码。
    *   第二个参数：买卖的金额（正数为买入，负数为卖出）。
    *   系统会根据当前价格计算可买股数，并自动调整为100股的整数倍。
    ```python
    # 用10000元买入股票
    order_value(‘000001.XSHE’, 10000)
    ```

3.  **`order_target`：调整至目标股数**
    *   第一个参数：股票代码。
    *   第二个参数：目标持有的总股数。
    *   系统会自动计算需要买入或卖出多少股以达到目标。
    ```python
    # 将持仓调整到200股（例如，当前有100股，则买入100股）
    order_target(‘000001.XSHE’, 200)
    # 全部卖出（将持仓调整到0股）
    order_target(‘000001.XSHE’, 0)
    ```

4.  **`order_target_value`：调整至目标市值**
    *   第一个参数：股票代码。
    *   第二个参数：目标持有的总市值（金额）。
    *   系统会自动计算需要买入或卖出多少金额的股票以达到目标市值。
    ```python
    # 将持仓市值调整到10000元
    order_target_value(‘000001.XSHE’, 10000)
    ```

![](img/abd133f52646acd318ef63faeff46751_31.png)

## 总结

![](img/abd133f52646acd318ef63faeff46751_33.png)

本节课中我们一起学习了量化策略回测框架的核心操作。我们明确了`initialize`和`handle_data`函数的不同执行频率，学会了使用`get_current_data`和`attribute_history`来获取当前与历史数据，并掌握了`order`、`order_value`、`order_target`和`order_target_value`这四个关键的下单函数。现在，我们已经具备了编写一个完整策略所需的基本工具。在下一个视频中，我们将运用这些知识，正式编写一个“低价买入，设置止盈止损”的实战策略。