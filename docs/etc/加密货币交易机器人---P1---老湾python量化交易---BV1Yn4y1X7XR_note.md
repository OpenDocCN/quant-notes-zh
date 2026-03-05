# 加密货币交易机器人：P1：连接币安交易所与核心功能入门 🚀

在本节课中，我们将学习如何在一个免费的云端Python服务器上连接币安交易所，并初步了解程序化交易中的几个核心功能。我们将使用`binder`平台来解决Google Colab无法连接币安的问题，并演示如何安装必要的工具包以及使用关键的函数。

---

## 概述

上一节我们介绍了在Google Colab上运行加密货币自动化交易代码。对于初学者或使用币安交易所的用户，可能会遇到环境配置和连接问题。本节我们将转向`binder`平台，这是一个位于欧洲的免费Jupyter Notebook服务器，可以顺利连接币安API。我们将在此环境中安装关键工具，并讲解程序化交易中查询数据、获取账户信息和下单等基本功能。

---

## 选择与配置免费Python云平台

为了简化Python环境配置，我们可以使用配置好Jupyter的免费云服务器，例如Google Colab和`binder`。这些平台能轻松安装第三方Python模块，避免本地安装时可能遇到的各种问题。

以下是选择`binder`平台的具体步骤：

1.  在谷歌上同时搜索 `binder`、`jupyter`、`notebook` 这几个关键字，找到该平台。
2.  在`binder`上打开一个Python 3的Jupyter Notebook，其界面与本地使用类似。

![](img/efaa0ad98303331f2edc56ff10a63bbe_1.png)

首先，我们运行一段代码来查询服务器的公网IP，以确认其位置。

```python
import requests
ip = requests.get('https://api.ipify.org').text
print(f"服务器IP地址是: {ip}")
```

![](img/efaa0ad98303331f2edc56ff10a63bbe_3.png)

通过`whois`查询可发现该IP位于欧洲。从欧洲服务器可以顺利连接到币安交易所的API。

---

## 安装核心工具包CCXT

`CCXT`是一个强大的库，它将众多加密货币交易所的API封装成统一的Python语法。使用它，你可以用几乎相同的代码跨交易所运行策略。

在Notebook中执行以下命令来安装`CCXT`：

```bash
!pip install ccxt
```

安装完成后，**务必重启一下Notebook内核**，然后导入模块并检查安装是否正确。

```python
import ccxt
# 使用dir()函数查看ccxt模块有哪些功能，确认安装正确
print(dir(ccxt))
```

一段完整的程序化交易代码，通常就是将这些功能模块有序地组织起来，以实现特定的交易逻辑。

---

## 登录交易所

在开始使用各项功能前，需要先登录交易所。如果你已经创建了币安交易所的API密钥，只需在代码中替换相应的参数即可。

以下是登录币安交易所的示例代码，该方法同样适用于其他主流加密货币交易所。

![](img/efaa0ad98303331f2edc56ff10a63bbe_5.png)

```python
exchange = ccxt.binance({
    'apiKey': '你的API_KEY',
    'secret': '你的SECRET_KEY',
    # 启用测试网（如需）: 'options': {'defaultType': 'future', 'test': True}
})
```

![](img/efaa0ad98303331f2edc56ff10a63bbe_7.png)

---

## 掌握查询文档的方法

![](img/efaa0ad98303331f2edc56ff10a63bbe_9.png)

在学习Python程序化交易时，培养查询官方文档的习惯至关重要。无论是处理数据还是使用像`CCXT`这样拥有数千个函数的库，官方文档都是最权威的指南。

`CCXT`功能繁多，没有任何教程能覆盖全部。这里示范如何查找和使用函数文档。本节将演示四个功能函数，前三个用于查询，最后一个用于下单。

例如，要查找获取K线数据的函数，可以在`dir(ccxt.binance())`的结果中寻找类似`fetch_ohlcv`的函数。使用`help()`函数可以查看该函数的详细用法、参数和示例。

![](img/efaa0ad98303331f2edc56ff10a63bbe_11.png)

```python
# 示例：查询fetch_ohlcv函数的帮助文档
help(exchange.fetch_ohlcv)
```

![](img/efaa0ad98303331f2edc56ff10a63bbe_13.png)

---

## 核心功能函数演示

现在，我们来看看几个在程序化交易中至关重要的功能函数。

![](img/efaa0ad98303331f2edc56ff10a63bbe_15.png)

### 1. 获取K线数据

K线数据包含开盘价、最高价、最低价和收盘价。使用`fetch_ohlcv`函数可以获取这些数据。获取K线数据通常不需要登录交易所。

![](img/efaa0ad98303331f2edc56ff10a63bbe_17.png)

以下是获取比特币5分钟K线数据的示例：

```python
# 获取BTC/USDT交易对的5分钟K线，最多获取10根
ohlcv = exchange.fetch_ohlcv('BTC/USDT', timeframe='5m', limit=10)
print(ohlcv)
```

### 2. 查询账户余额

查询账户余额需要使用`fetch_balance`函数。我们将返回的字符串格式余额转换为浮点数格式以便查看。

```python
# 查询账户余额
balance = exchange.fetch_balance()
# 打印USDT的可用余额
usdt_balance = float(balance['USDT']['free'])
print(f"可用USDT余额: {usdt_balance}")
```

### 3. 查询未成交订单

![](img/efaa0ad98303331f2edc56ff10a63bbe_19.png)

查询当前未成交的订单，可以使用`fetch_open_orders`函数。查询时需要指定交易对。

```python
# 查询BTC/USDT交易对的未成交订单
open_orders = exchange.fetch_open_orders('BTC/USDT')
print(open_orders)
```

### 4. 创建订单（下单）

加密货币的下单函数相对复杂，涉及更多参数。同样，建议先用`help(exchange.create_order)`查看详细说明。

![](img/efaa0ad98303331f2edc56ff10a63bbe_21.png)

以下是一个创建限价单的示例：

*   **第一个参数**：交易对代码，例如 `‘BTC/USDT’`
*   **第二个参数**：订单类型，例如 `‘limit’`（限价单）或 `‘market’`（市价单）
*   **第三个参数**：交易方向，`‘buy’`（买入）或 `‘sell’`（卖出）
*   **第四个参数**：订单数量

![](img/efaa0ad98303331f2edc56ff10a63bbe_23.png)

对于合约交易，参数更为复杂，可能还需要指定杠杆倍数。请注意，币安交易所对API交易有严格的IP限制，要求锁定IP，并且会检测是否使用代理。

![](img/efaa0ad98303331f2edc56ff10a63bbe_25.png)

```python
# 示例：尝试下一个BTC/USDT合约的限价买单（此订单可能因余额不足而失败）
try:
    order = exchange.create_order('BTC/USDT', 'limit', 'buy', 0.001, 66150, {'leverage': 10})
    print(order)
except Exception as e:
    print(f"下单失败: {e}")
```

运行上述代码可能会返回“余额不足”的错误，但这恰好证明了交易接口是通畅的，能够与币安API进行通信。

---

## 总结

![](img/efaa0ad98303331f2edc56ff10a63bbe_27.png)

本节课我们一起学习了如何利用`binder`免费云服务器连接币安交易所。我们完成了`CCXT`库的安装与验证，掌握了登录交易所的方法，并理解了查询官方文档的重要性。最后，我们实践了获取K线、查询余额、查询未成交订单以及创建订单这四个程序化交易的核心功能。这些功能是构建自动化交易策略的基石，通过组合它们，就能实现复杂的交易逻辑。在接下来的课程中，我们将学习如何将这些功能组织成一个完整的交易策略。