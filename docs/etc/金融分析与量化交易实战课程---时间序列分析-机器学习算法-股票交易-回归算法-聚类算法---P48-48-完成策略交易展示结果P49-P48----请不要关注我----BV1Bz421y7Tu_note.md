# 量化交易实战课程：P48：完成策略交易展示结果

![](img/d723162e815e3b51400a4839a466322b_0.png)

![](img/d723162e815e3b51400a4839a466322b_0.png)

![](img/d723162e815e3b51400a4839a466322b_2.png)

在本节课中，我们将继续完成 `REBECON` 函数。上一节我们已经完成了股票排名筛选，本节我们将基于排名差异，实现具体的买卖交易逻辑。

## 获取当前持仓股票

![](img/d723162e815e3b51400a4839a466322b_4.png)

![](img/d723162e815e3b51400a4839a466322b_6.png)

首先，我们需要一个函数来获取当前账户持有的股票。以下是该函数的实现步骤。

![](img/d723162e815e3b51400a4839a466322b_8.png)

第一步，从 `context` 对象中获取当前的持仓信息 `position`。

![](img/d723162e815e3b51400a4839a466322b_10.png)

```python
position = context.portfolio.positions
```

![](img/d723162e815e3b51400a4839a466322b_12.png)

第二步，遍历持仓信息，筛选出持有数量大于零的股票，并将其加入一个列表。

![](img/d723162e815e3b51400a4839a466322b_14.png)

```python
holdings = []
for stock in position:
    if position[stock].quantity > 0:
        holdings.append(stock)
```

第三步，函数返回这个持仓股票列表。

```python
return holdings
```

![](img/d723162e815e3b51400a4839a466322b_16.png)

至此，我们完成了获取持仓股票的函数 `get_holdings`。

![](img/d723162e815e3b51400a4839a466322b_18.png)

## 计算买卖清单

获取了新的股票排名 `stocks` 和当前持仓 `holdings` 后，接下来我们需要计算需要买入和卖出的股票清单。

以下是计算逻辑：
*   **买入清单 (`to_buy`)**：在新排名中，但不在当前持仓中的股票。公式为：`to_buy = set(stocks) - set(holdings)`。
*   **卖出清单 (`to_sell`)**：在当前持仓中，但不在新排名中的股票。公式为：`to_sell = set(holdings) - set(stocks)`。

![](img/d723162e815e3b51400a4839a466322b_20.png)

```python
# 将列表转换为集合，便于进行集合运算
stocks_set = set(stocks)
holdings_set = set(holdings)

![](img/d723162e815e3b51400a4839a466322b_22.png)

# 计算买卖清单
to_buy = list(stocks_set - holdings_set)
to_sell = list(holdings_set - stocks_set)
```

![](img/d723162e815e3b51400a4839a466322b_24.png)

![](img/d723162e815e3b51400a4839a466322b_26.png)

## 执行交易操作

![](img/d723162e815e3b51400a4839a466322b_28.png)

计算出买卖清单后，我们就可以执行具体的交易指令了。交易原则是先卖后买。

**首先，执行卖出操作。** 遍历卖出清单，将每只股票的持仓目标设置为零。

```python
for stock in to_sell:
    order_target_percent(stock, 0)
```

**然后，执行买入操作。** 在买入前，需要先判断买入清单是否为空。如果为空，则直接返回。

```python
if len(to_buy) == 0:
    return
```

如果买入清单不为空，则需要计算每只股票应分配的资金。这里我们采用等权重分配的方式。

![](img/d723162e815e3b51400a4839a466322b_30.png)

第一步，获取当前账户的总资产价值。

```python
total_value = context.portfolio.total_value
```

![](img/d723162e815e3b51400a4839a466322b_32.png)

![](img/d723162e815e3b51400a4839a466322b_34.png)

第二步，计算平均每只股票应投入的资金。

![](img/d723162e815e3b51400a4839a466322b_36.png)

```python
average_allocation = total_value / len(to_buy)
```

![](img/d723162e815e3b51400a4839a466322b_38.png)

第三步，遍历买入清单，执行买入指令。

![](img/d723162e815e3b51400a4839a466322b_40.png)

```python
for stock in to_buy:
    order_target_value(stock, average_allocation)
```

![](img/d723162e815e3b51400a4839a466322b_42.png)

![](img/d723162e815e3b51400a4839a466322b_44.png)

## 代码整合与运行测试

![](img/d723162e815e3b51400a4839a466322b_46.png)

现在，我们将以上所有步骤整合到主函数 `rebalance` 中。

![](img/d723162e815e3b51400a4839a466322b_48.png)

```python
def rebalance(context):
    # 1. 获取新的股票排名
    stocks = get_rank(context)
    # 2. 获取当前持仓
    holdings = get_holdings(context)
    # 3. 计算买卖清单
    stocks_set = set(stocks)
    holdings_set = set(holdings)
    to_buy = list(stocks_set - holdings_set)
    to_sell = list(holdings_set - stocks_set)
    # 4. 执行交易：先卖后买
    for stock in to_sell:
        order_target_percent(stock, 0)
    if len(to_buy) == 0:
        return
    total_value = context.portfolio.total_value
    average_allocation = total_value / len(to_buy)
    for stock in to_buy:
        order_target_value(stock, average_allocation)
```

![](img/d723162e815e3b51400a4839a466322b_50.png)

代码编写完成后，进行运行测试。首次运行很可能遇到错误，需要根据报错信息进行调试。

**常见的调试步骤包括：**
1.  检查函数名、变量名拼写是否正确。
2.  检查标点符号，特别是中英文逗号、括号是否使用正确。
3.  检查数据类型，确保集合运算 (`-`) 是在 `set` 类型之间进行，而不是 `list`。
4.  检查数据获取的API调用是否正确。

![](img/d723162e815e3b51400a4839a466322b_52.png)

![](img/d723162e815e3b51400a4839a466322b_54.png)

例如，在测试中如果遇到“无效字符”或“不支持类型”的错误，就需要按照上述步骤逐一排查和修正。

## 总结

![](img/d723162e815e3b51400a4839a466322b_56.png)

本节课中，我们一起学习了量化交易策略中核心的交易执行模块。我们实现了三个关键部分：
1.  **获取持仓**：编写了 `get_holdings` 函数来获取当前账户持有的股票。
2.  **生成信号**：通过集合运算，根据新的股票排名和当前持仓，计算出需要买入和卖出的股票清单。
3.  **执行交易**：遵循先卖后买的原则，使用 `order_target_percent` 和 `order_target_value` 函数执行具体的买卖操作，并实现了等权重资金分配。

![](img/d723162e815e3b51400a4839a466322b_58.png)

通过本课的学习，你已经掌握了将一个股票排名列表转化为实际交易指令的完整流程。接下来，就可以对整个策略进行回测，观察其历史表现。