# Python集成MT5量化交易：P1：基础集成与核心方法 🐍📈

![](img/aada0fe73d3ca74134e574fbb652e0d7_0.png)

在本节课中，我们将学习如何将Python与MetaTrader 5 (MT5) 交易平台集成，从而使用Python编写量化交易策略并利用MT5进行实盘交易。这种方法结合了Python强大的数据处理能力和MT5现成的免费市场数据与交易环境。

## 概述

许多交易者熟悉Python并希望用它来编写策略，同时又想利用MT5平台进行交易。本教程将介绍如何通过MT5官方提供的Python包，实现两者的连接与基本操作。

## 环境准备与连接

上一节我们介绍了课程目标，本节中我们来看看如何设置环境并与MT5建立连接。

首先，确保你已安装Python和MT5。本教程假设你已具备Python基础，因此不涉及安装步骤。

使用pip安装MT5的官方Python集成包。请注意，`numpy`的版本可能需要低于2.0以避免兼容性问题。

![](img/aada0fe73d3ca74134e574fbb652e0d7_2.png)

```python
pip install MetaTrader5
```

![](img/aada0fe73d3ca74134e574fbb652e0d7_4.png)

安装成功后，在Python脚本中导入该包并初始化连接。如果你的电脑上安装了多个MT5客户端，需要指定可执行文件路径。

![](img/aada0fe73d3ca74134e574fbb652e0d7_6.png)

![](img/aada0fe73d3ca74134e574fbb652e0d7_8.png)

```python
import MetaTrader5 as mt5

# 初始化连接，如果只有一个MT5，可直接调用 mt5.initialize()
mt5_connected = mt5.initialize(
    path="C:/Program Files/MetaTrader 5/terminal64.exe"
)
print(f"MT5连接状态: {mt5_connected}")
```

连接成功后，需要进行账户登录验证。你需要使用自己的MT5模拟账户或实盘账户信息。

```python
# 账户登录
account = 12345678  # 替换为你的账户ID
password = "your_password"  # 替换为你的密码
server = "YourBrokerServer"  # 替换为你的经纪商服务器

![](img/aada0fe73d3ca74134e574fbb652e0d7_10.png)

login_success = mt5.login(login=account, password=password, server=server)
print(f"登录状态: {login_success}")
```

![](img/aada0fe73d3ca74134e574fbb652e0d7_12.png)

## 账户信息查询

![](img/aada0fe73d3ca74134e574fbb652e0d7_14.png)

成功连接并登录后，我们就可以开始查询平台信息了。首先，让我们获取当前账户的基本信息。

![](img/aada0fe73d3ca74134e574fbb652e0d7_16.png)

以下是获取账户信息的核心方法：

![](img/aada0fe73d3ca74134e574fbb652e0d7_18.png)

```python
# 获取账户信息对象
account_info = mt5.account_info()

![](img/aada0fe73d3ca74134e574fbb652e0d7_20.png)

# 从对象中提取具体信息
print(f"服务器: {account_info.server}")
print(f"账户号: {account_info.login}")
print(f"账户余额: {account_info.balance}")
print(f"账户净值: {account_info.equity}")
```

你还可以使用 `account_info._asdict()` 查看所有可用的账户属性。

![](img/aada0fe73d3ca74134e574fbb652e0d7_22.png)

## 交易品种信息

![](img/aada0fe73d3ca74134e574fbb652e0d7_24.png)

了解账户情况后，下一步是获取市场数据。本节我们学习如何查询MT5平台支持的交易品种及其详细信息。

![](img/aada0fe73d3ca74134e574fbb652e0d7_26.png)

![](img/aada0fe73d3ca74134e574fbb652e0d7_28.png)

以下是获取品种信息的相关方法：

1.  **获取品种总数**：`mt5.symbols_total()` 返回经纪商支持的所有交易品种数量。
2.  **获取所有品种详情**：`mt5.symbols_get()` 返回一个列表，包含每个品种的完整属性对象。
3.  **获取特定品种信息**：`mt5.symbol_info(symbol)` 和 `mt5.symbol_info_tick(symbol)` 用于获取指定品种的静态属性或实时报价。

![](img/aada0fe73d3ca74134e574fbb652e0d7_30.png)

![](img/aada0fe73d3ca74134e574fbb652e0d7_32.png)

```python
# 获取可交易品种总数
total_symbols = mt5.symbols_total()
print(f"可交易品种总数: {total_symbols}")

# 获取所有品种信息列表（示例：打印前5个品种的名称和是否在市场报价中显示）
all_symbols = mt5.symbols_get()
for sym in all_symbols[:5]:
    print(f"品种: {sym.name}, 市场报价可见: {sym.visible}")

![](img/aada0fe73d3ca74134e574fbb652e0d7_34.png)

# 获取特定品种（如EURUSD）的实时报价
eurusd_tick = mt5.symbol_info_tick("EURUSD")
print(f"EURUSD 时间: {eurusd_tick.time}")
print(f"EURUSD 卖价: {eurusd_tick.ask}")
print(f"EURUSD 买价: {eurusd_tick.bid}")
```

![](img/aada0fe73d3ca74134e574fbb652e0d7_36.png)

## 获取历史市场数据

![](img/aada0fe73d3ca74134e574fbb652e0d7_38.png)

对于量化交易而言，历史数据至关重要。本节我们将学习如何使用Python从MT5获取K线数据。

![](img/aada0fe73d3ca74134e574fbb652e0d7_40.png)

我们使用 `mt5.copy_rates_range()` 方法来获取指定品种、周期和时间范围的历史数据。为了便于处理和分析，我们通常会配合 `pandas` 库。

![](img/aada0fe73d3ca74134e574fbb652e0d7_42.png)

```python
import pandas as pd
import matplotlib.pyplot as plt
from datetime import datetime

![](img/aada0fe73d3ca74134e574fbb652e0d7_44.png)

# 定义参数
symbol = "BTCUSD"
timeframe = mt5.TIMEFRAME_D1  # 日线周期
start_date = datetime(2023, 1, 1)
end_date = datetime.now()

![](img/aada0fe73d3ca74134e574fbb652e0d7_46.png)

# 获取历史数据
rates = mt5.copy_rates_range(symbol, timeframe, start_date, end_date)

![](img/aada0fe73d3ca74134e574fbb652e0d7_48.png)

# 将数据转换为Pandas DataFrame
df = pd.DataFrame(rates)
df['time'] = pd.to_datetime(df['time'], unit='s')  # 转换时间戳

![](img/aada0fe73d3ca74134e574fbb652e0d7_50.png)

![](img/aada0fe73d3ca74134e574fbb652e0d7_52.png)

# 使用收盘价绘制折线图
plt.figure(figsize=(12,6))
plt.plot(df['time'], df['close'])
plt.title(f"{symbol} 价格走势")
plt.xlabel("时间")
plt.ylabel("价格")
plt.grid(True)
plt.show()

# 查看数据前几行
print(df.head())
```

## 订单与持仓管理

![](img/aada0fe73d3ca74134e574fbb652e0d7_54.png)

![](img/aada0fe73d3ca74134e574fbb652e0d7_56.png)

掌握了数据获取，接下来就是交易的核心操作：订单管理。本节介绍如何查询持仓、挂单以及进行下单和平仓操作。

![](img/aada0fe73d3ca74134e574fbb652e0d7_58.png)

![](img/aada0fe73d3ca74134e574fbb652e0d7_59.png)

以下是订单与持仓管理的核心方法：

1.  **获取当前持仓**：`mt5.positions_get()` 返回所有未平仓的订单。
2.  **获取当前挂单**：`mt5.orders_get()` 返回所有等待成交的挂单。
3.  **获取历史订单**：`mt5.history_orders_get()` 返回已平仓或已删除的订单历史。

```python
# 获取当前所有持仓
positions = mt5.positions_get()
if positions:
    for pos in positions:
        print(f"订单号: {pos.ticket}, 品种: {pos.symbol}, 手数: {pos.volume}, 开仓价: {pos.price_open}")
else:
    print("没有未平仓持仓")

# 获取当前所有挂单
orders = mt5.orders_get()
if orders:
    for ord in orders:
        print(f"挂单号: {ord.ticket}, 品种: {ord.symbol}, 类型: {ord.type}")
```

![](img/aada0fe73d3ca74134e574fbb652e0d7_61.png)

![](img/aada0fe73d3ca74134e574fbb652e0d7_63.png)

## 执行交易

最后，我们学习最关键的步骤：如何通过Python代码在MT5上执行开仓和平仓。

![](img/aada0fe73d3ca74134e574fbb652e0d7_65.png)

![](img/aada0fe73d3ca74134e574fbb652e0d7_67.png)

我们使用 `mt5.order_send(request)` 方法执行交易。需要构建一个包含交易指令的字典 `request`。

![](img/aada0fe73d3ca74134e574fbb652e0d7_69.png)

**开仓（市价单）示例：**

![](img/aada0fe73d3ca74134e574fbb652e0d7_71.png)

```python
# 构建开仓请求
symbol = "BTCUSD"
lot = 0.1
point = mt5.symbol_info(symbol).point
price = mt5.symbol_info_tick(symbol).ask

![](img/aada0fe73d3ca74134e574fbb652e0d7_73.png)

request = {
    "action": mt5.TRADE_ACTION_DEAL,
    "symbol": symbol,
    "volume": lot,
    "type": mt5.ORDER_TYPE_BUY,
    "price": price,
    "sl": price - 1000 * point,  # 止损
    "tp": price + 2000 * point,  # 止盈
    "deviation": 20,
    "magic": 234000,
    "comment": "Python开仓",
    "type_time": mt5.ORDER_TIME_GTC,
    "type_filling": mt5.ORDER_FILLING_IOC,
}

![](img/aada0fe73d3ca74134e574fbb652e0d7_75.png)

# 发送交易请求
result = mt5.order_send(request)
print(f"开仓结果: {result}")
```

**平仓示例：**
平仓时需要指定要平仓的订单号（ticket）。

```python
# 假设要平仓的订单号为 123456
position_id = 123456

# 首先获取该持仓的信息
position = mt5.positions_get(ticket=position_id)
if position:
    pos = position[0]
    # 构建平仓请求，类型与开仓相反
    close_request = {
        "action": mt5.TRADE_ACTION_DEAL,
        "symbol": pos.symbol,
        "volume": pos.volume,
        "type": mt5.ORDER_TYPE_SELL if pos.type == 0 else mt5.ORDER_TYPE_BUY, # 判断方向
        "position": pos.ticket, # 指定要平的仓位
        "price": mt5.symbol_info_tick(pos.symbol).bid, # 平仓价格
        "deviation": 20,
        "magic": pos.magic,
        "comment": "Python平仓",
        "type_time": mt5.ORDER_TIME_GTC,
        "type_filling": mt5.ORDER_FILLING_IOC,
    }
    # 发送平仓请求
    close_result = mt5.order_send(close_request)
    print(f"平仓结果: {close_result}")
```

## 总结

![](img/aada0fe73d3ca74134e574fbb652e0d7_77.png)

![](img/aada0fe73d3ca74134e574fbb652e0d7_79.png)

本节课中我们一起学习了Python集成MT5进行量化交易的基础流程。我们从环境配置和连接MT5开始，逐步掌握了如何查询账户与品种信息、获取历史市场数据、管理订单与持仓，并最终实现了通过Python代码执行开仓和平仓交易。

![](img/aada0fe73d3ca74134e574fbb652e0d7_81.png)

所有方法均基于 `MetaTrader5` 官方包，更详细的说明请参考其官方文档。掌握了这些核心方法后，你就可以将它们组合起来，构建属于自己的自动化交易程序了。