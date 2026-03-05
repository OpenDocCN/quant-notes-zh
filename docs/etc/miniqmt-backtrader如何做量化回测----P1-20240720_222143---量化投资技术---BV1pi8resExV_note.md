# 量化回测教程：P1：使用MiniQMT数据源与Backtrader进行回测 🚀

## 概述
在本节课中，我们将学习如何利用免费的MiniQMT数据源，结合强大的开源回测框架Backtrader，构建一个完整的量化回测系统。我们将重点介绍一个名为`qmt_bt`的专用库，它能将MiniQMT数据无缝接入Backtrader，并讲解Backtrader策略编写的基础知识。

---

## MiniQMT数据源与回测的挑战

MiniQMT的免费版本提供了数据获取和程序化下单的接口，但其内置的回测系统并未对外开放。因此，我们需要借助第三方回测框架来实现策略验证。

上一节我们介绍了回测的需求，本节中我们来看看解决方案的核心思路：将MiniQMT作为数据源，接入一个成熟的开源回测引擎。这里我们选择的是Backtrader。

---

## 解决方案：qmt_bt库介绍

我开发了一个名为`qmt_bt`的Python库，它可以理解为Backtrader的一个插件。Backtrader框架支持通过开发`Store`来接入自定义数据源，`qmt_bt`库正是实现了这样一个Store，专门用于连接MiniQMT。

这个库的作用是封装数据获取与转换的逻辑，将MiniQMT下载的`pandas DataFrame`格式数据，转换为Backtrader可以直接使用的数据格式。

### 安装与基础使用

`qmt_bt`库已发布至PyPI，可以使用pip命令安装。

```bash
pip install qmt_bt
```

以下是基础的使用代码框架：

```python
import backtrader as bt
from qmt_bt import QMTStore  # 从qmt_bt库导入QMTStore

# 创建Cerebro引擎（大脑）
cerebro = bt.Cerebro()
# 创建QMT数据源Store
store = QMTStore()

![](img/ade6d97716d9a2e581788a02882d8546_1.png)

# 通过store获取数据，并添加到Cerebro中
data = store.getdata(
    dataname='600036.SH',  # 股票代码，例如：600036.SH
    timeframe=bt.TimeFrame.Days,  # 周期，例如日线
    fromdate=datetime(2023, 1, 1),  # 开始时间
    todate=datetime(2023, 12, 31),  # 结束时间
    adj='qfq'  # 复权方式，例如前复权('qfq')
)
cerebro.adddata(data)

# ... 后续添加策略、设置资金等操作
cerebro.run()
```

通过以上代码，我们就完成了将MiniQMT数据注入Backtrader的过程。库内部处理了所有数据对接的细节，用户只需调用`getdata`方法即可。

---

## Backtrader框架简介

Backtrader是一个功能强大且历史悠久的开源回测框架，在GitHub上拥有超过13k的star。虽然其官方文档为英文且学习曲线较陡，但借助现代AI工具（如ChatGPT、DeepSeek等），可以有效地辅助我们进行学习和策略开发。

接下来，我们通过一个简单的双均线策略示例，来了解Backtrader策略的核心构成。

---

## Backtrader策略核心模块解析

一个完整的Backtrader策略通常可以拆解为几个核心部分：**买入条件**、**卖出条件**和**仓位管理（Sizer）**。将这几部分独立出来，有利于策略的管理与复用。

以下是各部分的一个简单实现逻辑：

### 1. 买入/卖出条件
这通常由技术指标（如均线交叉）定义。
*   **买入条件（金叉）**：短期均线上穿长期均线。
*   **卖出条件（死叉）**：短期均线下穿长期均线。

### 2. 仓位管理（Sizer）
Sizer控制每次交易的资金分配。以下是一个简单的仓位管理逻辑：
*   **买入**：将可用资金平均分配给所有出现买入信号的股票。
*   **卖出**：卖出对应股票的全部持仓。

### 3. 策略主体
策略主体是一个类，主要包含两个方法：
*   `__init__`：初始化方法，在策略开始时执行一次，用于注册Sizer、计算指标等。
*   `next`：核心方法，在每个数据点（如每日）执行，根据当前条件发出交易指令。

```python
class MyStrategy(bt.Strategy):
    def __init__(self):
        # 注册自定义的仓位管理器
        self.sizer = MySizer()
        # 计算指标：短期均线与长期均线
        self.sma_short = {data: bt.ind.SMA(data, period=10) for data in self.datas}
        self.sma_long = {data: bt.ind.SMA(data, period=30) for data in self.datas}
        # 计算买卖条件
        self.buy_signal = {data: self.sma_short[data] > self.sma_long[data] for data in self.datas}
        self.sell_signal = {data: self.sma_short[data] < self.sma_long[data] for data in self.datas}

    def next(self):
        # 收集当前时刻的买卖信号
        buy_list = [data for data in self.datas if self.buy_signal[data]]
        sell_list = [data for data in self.datas if self.sell_signal[data]]

        # 动态设置本次循环的买入数量（用于Sizer）
        self.buy_count = len(buy_list)

        # 先执行卖出
        for data in sell_list:
            self.close(data=data)
        # 再执行买入
        for data in buy_list:
            self.buy(data=data)
```

---

![](img/ade6d97716d9a2e581788a02882d8546_3.png)

## 完整回测流程演示

现在，我们将`qmt_bt`数据源和双均线策略组合起来，运行一个完整的回测。

以下是回测执行的主要步骤：

1.  **初始化环境**：创建Cerebro引擎和QMTStore。
2.  **加载数据**：通过`store.getdata()`加载多只股票数据并添加到Cerebro。
3.  **添加策略**：将我们定义好的策略类添加到Cerebro。
4.  **设置参数**：配置初始资金、交易佣金等。
5.  **执行回测**：调用`cerebro.run()`运行策略。
6.  **结果可视化**：调用`cerebro.plot()`生成回测图表。

运行成功后，Backtrader会输出图表。图表顶部显示资金曲线，底部展示各股票的价格、均线以及买卖点标记。需要注意的是，内置绘图功能更适合分析单只标的，多股同时绘图可能会显得杂乱。

![](img/ade6d97716d9a2e581788a02882d8546_5.png)

---

## 学习资源与工具

![](img/ade6d97716d9a2e581788a02882d8546_7.png)

为了更深入地学习Backtrader，你可以利用以下资源：
*   **AI辅助**：使用大语言模型（如DeepSeek）辅助阅读文档和编写策略代码。
*   **源码文档**：我整理了一份Backtrader的源码文档并托管在GitHub上，方便查询各个类和方法的具体说明。
*   **浏览器翻译插件**：帮助快速阅读英文的官方文档或源码注释。

---

## 总结

![](img/ade6d97716d9a2e581788a02882d8546_9.png)

本节课中我们一起学习了：
1.  **连接数据与回测引擎**：使用`qmt_bt`库将MiniQMT数据源便捷地接入Backtrader回测框架。
2.  **理解Backtrader策略结构**：将一个策略分解为**买入条件**、**卖出条件**和**仓位管理**三个核心模块，并编写了策略主体类。
3.  **运行完整回测**：掌握了从数据加载、策略添加、参数设置到执行和可视化的完整回测流程。

![](img/ade6d97716d9a2e581788a02882d8546_11.png)

通过本教程，你可以利用免费的MiniQMT数据和强大的Backtrader框架，开始构建和测试自己的量化交易策略了。