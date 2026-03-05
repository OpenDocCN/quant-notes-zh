# Python金融量化投资分析与股票交易：P66：下单函数1

![](img/f68668d8ff469e27e02aec9f61ce6ef6_1.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_3.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_5.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_7.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_9.png)

## 概述
在本节课中，我们将学习如何编写一个核心的下单函数。这个函数是模拟股票交易的基础，它会处理买入和卖出操作，并更新我们的虚拟账户信息，包括现金和持仓。我们将从获取当日股价开始，逐步构建一个健壮的下单函数，它会考虑现金是否充足、交易数量是否为100的整数倍、卖出数量是否超过持仓等多种现实交易中的约束条件。

![](img/f68668d8ff469e27e02aec9f61ce6ef6_11.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_13.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_15.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_17.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_19.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_21.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_22.png)

## 获取当日股价数据
上一节我们介绍了如何获取历史数据。本节中，我们来看看如何获取当日的股价数据，因为下单时需要知道股票当天的价格。

![](img/f68668d8ff469e27e02aec9f61ce6ef6_24.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_26.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_28.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_30.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_32.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_34.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_36.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_38.png)

我们需要一个函数来获取指定股票在`context.dt`（当前模拟日期）这一天的数据。我们假设以开盘价作为当日的交易价格。

![](img/f68668d8ff469e27e02aec9f61ce6ef6_40.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_42.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_44.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_46.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_48.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_50.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_52.png)

```python
def get_today_data(security):
    """
    获取指定股票在context.dt日的股价数据。
    参数:
        security: 股票代码，例如 '601318'
    返回:
        一个pandas Series，包含该股票当日的开盘价、收盘价等数据。
        如果当日无数据（如非交易日或停牌），则返回一个空的Series。
    """
    # 将context.dt转换为字符串格式的日期
    today = context.dt.strftime('%Y-%m-%d')
    try:
        # 尝试从本地CSV文件读取数据
        with open(f'{security}.csv', 'r') as f:
            df = pd.read_csv(f, index_col='date', parse_dates=['date'])
        # 选取当天的数据行
        data = df.loc[today]
    except FileNotFoundError:
        # 如果本地没有文件，则从在线数据源获取
        data = ts.get_k_data(security, start=today, end=today)
        if not data.empty:
            data = data.iloc[0]  # 取第一行（也是唯一一行）
        else:
            data = pd.Series()  # 如果没有数据，返回空Series
    except KeyError:
        # 如果在DataFrame中找不到‘today’这一天（非交易日或停牌）
        data = pd.Series()
    return data
```

![](img/f68668d8ff469e27e02aec9f61ce6ef6_54.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_55.png)

## 构建核心下单函数
有了当日股价数据，我们现在可以构建核心的下单函数 `_order`。这个函数是私有的（以下划线开头），它将处理具体的交易逻辑，并被后续更上层的函数（如`order`, `order_target`等）调用。

![](img/f68668d8ff469e27e02aec9f61ce6ef6_57.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_59.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_61.png)

以下是`_order`函数需要考虑的关键步骤和逻辑：

![](img/f68668d8ff469e27e02aec9f61ce6ef6_63.png)

1.  **检查当日是否可交易**：调用`get_today_data`，如果返回空数据，则说明股票停牌或当日非交易日，无法下单。
2.  **检查现金是否充足**（针对买入操作）：计算交易所需金额（股数 * 股价），并与当前现金`context.cash`比较。
3.  **调整交易数量**：A股交易规则要求买卖数量通常是100股（1手）的整数倍，除非是清仓卖出。我们需要对用户传入的数量进行调整。
4.  **检查卖出数量是否超过持仓**：卖出数量不能超过当前持有该股票的数量。
5.  **执行交易并更新账户**：通过上述检查后，更新持仓`context.positions`和现金`context.cash`。
6.  **清理零持仓**：如果某只股票持仓数量变为0，则从持仓字典中删除该条目。

![](img/f68668d8ff469e27e02aec9f61ce6ef6_65.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_67.png)

```python
def _order(today_data, security, amount):
    """
    核心下单函数。
    参数:
        today_data: 通过get_today_data函数获取的当日数据Series。
        security: 股票代码。
        amount: 买卖数量。正数表示买入，负数表示卖出。
    """
    # 1. 检查当日是否可交易
    if today_data.empty:
        print(f"{security} 今日停牌或无数据，无法交易。")
        return

    # 获取当日开盘价作为交易价格
    price = today_data['open']

    # 2. 检查现金是否充足 (仅对买入操作有效，amount > 0)
    if amount > 0 and context.cash < amount * price:
        # 现金不足，计算最大可买数量
        max_amount = int(context.cash // price)
        # 调整为100的整数倍（向下取整）
        max_amount = (max_amount // 100) * 100
        print(f"现金不足，买入数量由 {amount} 调整为 {max_amount}")
        amount = max_amount
        if amount == 0:
            print("现金不足以购买任何数量。")
            return

    # 3. 调整交易数量为100的整数倍
    # 判断是否为清仓卖出（卖出数量等于当前持仓）
    current_amount = context.positions.get(security, 0)
    is_sell_all = (amount < 0 and abs(amount) == current_amount)

    if amount % 100 != 0 and not is_sell_all:
        # 如果不是清仓卖出，则调整数量为最接近的100的整数倍（向零取整）
        adjusted_amount = (int(amount / 100)) * 100
        print(f"交易数量不是100的整数倍，已由 {amount} 调整为 {adjusted_amount}")
        amount = adjusted_amount

    # 4. 检查卖出数量是否超过持仓
    if amount < 0 and abs(amount) > current_amount:
        # 卖出数量超过持仓，调整为全部卖出
        print(f"卖出数量超过当前持仓({current_amount})，已调整为卖出全部。")
        amount = -current_amount  # 负号表示卖出
        # 清仓卖出后，数量可能不是100的倍数，这是允许的。

    # 如果经过所有调整后，交易数量为0，则直接返回
    if amount == 0:
        return

    # 5. 执行交易，更新账户信息
    # 更新持仓
    new_amount = current_amount + amount
    context.positions[security] = new_amount

    # 更新现金 (买入减钱，卖出的负amount会使得这里变成加钱)
    context.cash -= amount * price

    # 6. 清理零持仓
    if new_amount == 0:
        del context.positions[security]

    # 打印交易信息（可选）
    action = "买入" if amount > 0 else "卖出"
    print(f"已{action} {security} {abs(amount)}股，价格{price:.2f}。当前现金：{context.cash:.2f}")
```

## 测试核心下单函数
让我们写一段简单的代码来测试这个函数。首先需要初始化`context`对象。

![](img/f68668d8ff469e27e02aec9f61ce6ef6_69.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_71.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_73.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_75.png)

```python
# 初始化上下文和账户
class Context:
    def __init__(self):
        self.dt = pd.to_datetime('2023-01-10') # 假设一个交易日
        self.cash = 100000.0  # 初始资金10万元
        self.positions = {}   # 持仓字典，格式：{‘股票代码’: 持股数量}

![](img/f68668d8ff469e27e02aec9f61ce6ef6_77.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_79.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_81.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_83.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_85.png)

context = Context()

![](img/f68668d8ff469e27e02aec9f61ce6ef6_87.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_89.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_91.png)

# 测试1：买入100股平安银行
data = get_today_data('601318')
_order(data, '601318', 100)
print(f"持仓: {context.positions}")
print(f"现金: {context.cash}")

![](img/f68668d8ff469e27e02aec9f61ce6ef6_93.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_95.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_97.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_98.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_100.png)

# 测试2：尝试买入非100倍数量（如125股）
_order(data, '601318', 125)
print(f"持仓: {context.positions}")
print(f"现金: {context.cash}")

![](img/f68668d8ff469e27e02aec9f61ce6ef6_102.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_104.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_106.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_108.png)

# 测试3：尝试卖出超过持仓的数量
_order(data, '601318', -500) # 假设此时只有225股
print(f"持仓: {context.positions}")
print(f"现金: {context.cash}")

![](img/f68668d8ff469e27e02aec9f61ce6ef6_110.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_112.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_114.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_116.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_118.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_120.png)

# 测试4：清仓卖出（数量可能不是100的倍数）
_order(data, '601318', -context.positions.get('601318', 0))
print(f"持仓: {context.positions}")
print(f"现金: {context.cash}")
```

![](img/f68668d8ff469e27e02aec9f61ce6ef6_122.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_124.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_126.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_128.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_130.png)

## 总结
本节课中我们一起学习了如何构建一个模拟交易的核心下单函数 `_order`。我们首先实现了获取当日股价数据的函数 `get_today_data`。然后，我们详细设计了 `_order` 函数，它包含了现实交易中的多种风控和规则检查：
*   检查股票是否可交易。
*   检查买入时现金是否充足，并自动调整可买数量。
*   将交易数量调整为100股的整数倍（清仓卖出除外）。
*   检查卖出数量是否超过当前持仓。
*   成功执行交易后，更新现金和持仓信息。

![](img/f68668d8ff469e27e02aec9f61ce6ef6_132.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_134.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_136.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_138.png)

![](img/f68668d8ff469e27e02aec9f61ce6ef6_140.png)

这个函数是我们量化交易策略得以执行的基础。在下一节课中，我们将基于这个核心函数，封装出类似于聚宽（JoinQuant）平台的 `order`, `order_value`, `order_target`, `order_target_value` 这四个更易用的上层下单函数。