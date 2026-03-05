# 期货套利价差监控平台：P1：基于无限易PythonGo的期货价差监控平台

在本节课中，我们将学习如何利用无限易的PythonGo接口，构建一个基础的期货套利价差监控平台。我们将从环境准备开始，逐步实现一个能够实时计算并展示两个期货合约价差的程序。

## 概述

套利交易的核心在于捕捉不同合约之间不合理的价差。手动监控多个合约的价格既繁琐又低效。本教程将指导你使用Python和无限易的API，自动化这一监控过程。我们将创建一个程序，能够订阅两个期货合约的实时行情，并动态计算它们之间的价差。

## 环境准备与配置

在开始编写代码之前，我们需要完成一些准备工作。这包括安装必要的软件、获取API接口文件以及配置开发环境。

以下是需要完成的步骤：

1.  **安装Python**：确保你的计算机上安装了Python（建议使用3.7或更高版本）。
2.  **安装无限易**：从官方渠道下载并安装无限易交易软件。
3.  **获取PythonGo接口文件**：在无限易安装目录下（通常为 `C:\Program Files\无限易\bin\` 或类似路径），找到名为 `PythonGo.pyd` 和 `PythonGo.py` 的文件。`PythonGo.pyd` 是核心的动态链接库，`PythonGo.py` 是其Python封装。
4.  **配置项目目录**：创建一个新的项目文件夹，将上述两个接口文件复制到该文件夹中。

![](img/002914dbee4212553cd0cd3a1e28b42e_0.png)

## 初始化与连接行情服务器

上一节我们准备好了开发环境，本节中我们来看看如何初始化API并连接到无限易的行情服务器。这是获取实时数据的第一步。

核心的初始化操作通过 `CreateGoid` 函数完成，它创建并返回一个API实例对象。连接服务器则需要调用该对象的 `Start` 方法。

```python
import PythonGo

![](img/002914dbee4212553cd0cd3a1e28b42e_1.png)

# 创建API实例
api = PythonGo.CreateGoid()

# 连接到行情服务器
# 参数1：服务器地址，通常为“tcp://xxx.xxx.xxx.xxx:xxxx”
# 参数2：用户名
# 参数3：密码
connect_result = api.Start("tcp://127.0.0.1:端口号", "你的用户名", "你的密码")
if connect_result:
    print("行情服务器连接成功")
else:
    print("行情服务器连接失败")
```

## 订阅合约行情

成功连接服务器后，下一步是订阅我们感兴趣的期货合约行情。我们需要知道合约的唯一标识符。

在无限易API中，订阅行情使用 `Sub` 方法。合约代码需要遵循特定的格式，例如股指期货为 `IC2309.CFFEX`，其中 `IC` 是品种，`2309` 是合约到期年月，`CFFEX` 是交易所代码。

![](img/002914dbee4212553cd0cd3a1e28b42e_2.png)

以下是订阅两个合约的示例代码：

```python
# 定义要监控的合约代码
contract_a = "IC2309.CFFEX"  # 例如，中证500指数期货2309合约
contract_b = "IF2309.CFFEX"  # 例如，沪深300指数期货2309合约

# 订阅合约A的行情
api.Sub(contract_a)
# 订阅合约B的行情
api.Sub(contract_b)

print(f"已订阅合约 {contract_a} 和 {contract_b} 的行情")
```

## 接收行情与计算价差

![](img/002914dbee4212553cd0cd3a1e28b42e_3.png)

订阅合约后，API会通过回调函数推送实时行情数据。我们需要编写一个函数来处理这些数据，并计算价差。

价差（Spread）的基本计算公式是：
**价差 = 合约A的最新价 - 合约B的最新价**

我们创建一个全局字典来存储各合约的最新价格，并在行情回调函数中更新它们并计算价差。

```python
# 用于存储合约最新价格的字典
latest_prices = {}

def on_tick(data):
    """
    行情回调函数
    data: 包含行情数据的字典，其中‘InstrumentID’是合约代码，‘LastPrice’是最新价
    """
    contract_id = data.get('InstrumentID')
    last_price = data.get('LastPrice')
    
    if contract_id and last_price is not None:
        # 更新价格字典
        latest_prices[contract_id] = last_price
        
        # 检查我们监控的两个合约价格是否都已更新
        if contract_a in latest_prices and contract_b in latest_prices:
            # 计算价差
            spread = latest_prices[contract_a] - latest_prices[contract_b]
            print(f"价差({contract_a} - {contract_b}): {spread:.2f}")

# 将回调函数注册到API实例
api.SetCallback(on_tick)
```

![](img/002914dbee4212553cd0cd3a1e28b42e_5.png)

## 保持程序运行

行情数据是持续推送的，因此我们需要让程序保持运行状态，而不是执行完代码就立即退出。一个简单的方法是使用循环。

```python
import time

print("价差监控程序已启动，按 Ctrl+C 终止...")
try:
    while True:
        # 让主线程休眠，避免空跑占用大量CPU
        time.sleep(1)
except KeyboardInterrupt:
    print("\n程序已手动终止。")
    # 在退出前，可以取消订阅并断开连接
    api.UnSub(contract_a)
    api.UnSub(contract_b)
    api.Stop()
```

## 总结

本节课中我们一起学习了构建期货价差监控平台的基础步骤。我们从环境配置开始，逐步实现了连接到无限易行情服务器、订阅特定期货合约、接收实时行情数据，并计算两个合约之间的价差。这个简单的监控平台是自动化套利策略的基石。你可以在此基础上，增加更多功能，如价差图表绘制、报警阈值设置、历史数据记录等，使其更加强大和实用。