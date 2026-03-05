# Python金融量化投资：P67：下单函数封装

![](img/eec9e1af585aa0cfb9f318473f46313a_1.png)

## 概述
在本节课中，我们将继续学习量化交易框架中下单功能的实现。我们将基于上一节创建的基础下单函数，封装出四个更高级、更易用的下单函数，分别是`order`、`order_target`、`order_value`和`order_target_value`。这些函数将构成我们策略执行的核心工具。

![](img/eec9e1af585aa0cfb9f318473f46313a_3.png)

---

![](img/eec9e1af585aa0cfb9f318473f46313a_5.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_7.png)

## 封装四个下单函数

上一节我们介绍了底层下单函数`_order`，它负责处理具体的交易逻辑。本节中，我们来看看如何在此基础上构建更便捷的上层函数。

![](img/eec9e1af585aa0cfb9f318473f46313a_9.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_11.png)

### 1. `order`函数：按股数下单
`order`函数是最直接的交易指令，用于买入或卖出指定数量的股票。其参数与底层`_order`函数保持一致。

![](img/eec9e1af585aa0cfb9f318473f46313a_13.png)

以下是`order`函数的实现步骤：
1.  获取指定股票当日的行情数据。
2.  调用底层`_order`函数执行交易。

![](img/eec9e1af585aa0cfb9f318473f46313a_15.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_17.png)

```python
def order(security, amount):
    # 获取当日数据
    today_data = get_today_data(security)
    # 调用底层下单函数
    _order(today_data, security, amount)
```

### 2. `order_target`函数：调整至目标股数
`order_target`函数用于将某只股票的持仓调整到指定的目标股数。它会自动计算需要买入或卖出的差额。

以下是`order_target`函数的实现步骤：
1.  检查目标股数`amount`，确保其不为负值。
2.  获取当前持仓股数。
3.  计算目标股数与当前持仓的差额`delta_amount`。
4.  调用`order`函数执行差额交易。

```python
def order_target(security, amount):
    # 检查目标股数不能为负
    if amount < 0:
        print("目标股数不能为负，已调整为0")
        amount = 0

    # 获取当日数据
    today_data = get_today_data(security)
    # 获取当前持仓股数，若未持仓则为0
    hold_amount = context.positions.get(security, 0)
    # 计算需要交易的差额股数
    delta_amount = amount - hold_amount
    # 执行交易
    order(security, delta_amount)
```

![](img/eec9e1af585aa0cfb9f318473f46313a_19.png)

**关于T+1制度的说明**：目前我们的框架简化了交易规则，买入的股票可以立即卖出。在实际A股市场中，实行T+1制度，即当日买入的股票需等到下一个交易日才能卖出。如需完善，可以在`context.positions`中区分`total_amount`（总持仓）和`closeable_amount`（可卖持仓），并在每日结束时更新。

![](img/eec9e1af585aa0cfb9f318473f46313a_21.png)

### 3. `order_value`函数：按金额下单
`order_value`函数允许我们直接指定买入或卖出多少金额的股票，系统会自动根据当前股价计算对应的股数。

![](img/eec9e1af585aa0cfb9f318473f46313a_23.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_25.png)

以下是`order_value`函数的实现步骤：
1.  获取指定股票当日的行情数据。
2.  用目标金额`value`除以当日开盘价，计算出需要交易的近似股数`amount`。
3.  调用`order`函数执行交易。

![](img/eec9e1af585aa0cfb9f318473f46313a_27.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_29.png)

```python
def order_value(security, value):
    # 获取当日数据
    today_data = get_today_data(security)
    # 根据金额和价格计算需要交易的股数（取整）
    amount = int(value / today_data['open'])
    # 执行交易
    order(security, amount)
```

![](img/eec9e1af585aa0cfb9f318473f46313a_31.png)

### 4. `order_target_value`函数：调整至目标市值
`order_target_value`函数结合了`order_target`和`order_value`的功能，用于将某只股票的持仓市值调整到指定的目标价值。

![](img/eec9e1af585aa0cfb9f318473f46313a_33.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_35.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_37.png)

以下是`order_target_value`函数的实现步骤：
1.  检查目标价值`value`，确保其不为负值。
2.  获取当前持仓股数和当日股价。
3.  计算当前持仓的总价值`hold_value`。
4.  计算目标价值与当前持仓价值的差额`delta_value`。
5.  调用`order_value`函数执行差额价值的交易。

```python
def order_target_value(security, value):
    # 检查目标价值不能为负
    if value < 0:
        print("目标价值不能为负，已调整为0")
        value = 0

    # 获取当日数据
    today_data = get_today_data(security)
    # 获取当前持仓股数，若未持仓则为0
    hold_amount = context.positions.get(security, 0)
    # 计算当前持仓价值
    hold_value = hold_amount * today_data['open']
    # 计算需要交易的差额价值
    delta_value = value - hold_value
    # 执行交易
    order_value(security, delta_value)
```

![](img/eec9e1af585aa0cfb9f318473f46313a_39.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_40.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_42.png)

---

![](img/eec9e1af585aa0cfb9f318473f46313a_44.png)

## 函数测试
我们可以编写简单的策略来测试这些下单函数是否正常工作。例如，尝试买入不同数量和金额的股票，并打印持仓信息来验证结果。

![](img/eec9e1af585aa0cfb9f318473f46313a_46.png)

```python
# 示例：测试order函数
order('600519', 100)  # 买入100股茅台
print(context.positions)

![](img/eec9e1af585aa0cfb9f318473f46313a_48.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_50.png)

# 示例：测试order_value函数
order_value('600519', 30000)  # 买入价值3万元的茅台
print(context.positions)

![](img/eec9e1af585aa0cfb9f318473f46313a_52.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_54.png)

# 示例：测试order_target函数
order_target('600519', 500)  # 将茅台持仓调整至500股
print(context.positions)

![](img/eec9e1af585aa0cfb9f318473f46313a_56.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_58.png)

# 示例：测试order_target_value函数
order_target_value('600519', 100000)  # 将茅台持仓市值调整至10万元
print(context.positions)
```

![](img/eec9e1af585aa0cfb9f318473f46313a_60.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_62.png)

运行测试后，检查`context.positions`中的持仓变化，可以确认各个函数逻辑正确，能够按预期执行买入、卖出和调整操作。

---

![](img/eec9e1af585aa0cfb9f318473f46313a_64.png)

![](img/eec9e1af585aa0cfb9f318473f46313a_66.png)

## 总结
本节课中我们一起学习了如何封装四个实用的下单函数：
1.  **`order(security, amount)`**：按指定股数交易。
2.  **`order_target(security, amount)`**：将持仓调整至目标股数。
3.  **`order_value(security, value)`**：按指定金额交易。
4.  **`order_target_value(security, value)`**：将持仓市值调整至目标价值。

![](img/eec9e1af585aa0cfb9f318473f46313a_68.png)

这些函数构建在底层`_order`函数之上，提供了更符合策略编写习惯的接口。目前，我们的交易框架已具备数据获取和订单执行的核心能力。接下来，我们将进入回测引擎的开发阶段，学习如何模拟交易日循环、计算收益并可视化结果。