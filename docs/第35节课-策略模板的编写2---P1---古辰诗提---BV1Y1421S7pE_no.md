# Python量化基础：第35课：策略模板的编写2 🛠️

![](img/e6164f36291052047a9b9abc9d4eb130_1.png)

![](img/e6164f36291052047a9b9abc9d4eb130_3.png)

![](img/e6164f36291052047a9b9abc9d4eb130_5.png)

![](img/e6164f36291052047a9b9abc9d4eb130_7.png)

![](img/e6164f36291052047a9b9abc9d4eb130_9.png)

在本节课中，我们将继续学习策略模板的编写。上一节我们介绍了如何获取策略的参数和变量，本节我们将深入探讨如何获取策略的整体信息、实现委托的撤销与数据保存功能，并定义策略子类必须实现的核心方法。最后，我们将引入 `dataclasses` 库来优化数据管理。

![](img/e6164f36291052047a9b9abc9d4eb130_11.png)

![](img/e6164f36291052047a9b9abc9d4eb130_13.png)

![](img/e6164f36291052047a9b9abc9d4eb130_15.png)

## 获取策略信息

![](img/e6164f36291052047a9b9abc9d4eb130_17.png)

![](img/e6164f36291052047a9b9abc9d4eb130_19.png)

上一节我们介绍了如何获取策略的参数和变量，本节我们来看看如何整合这些信息，以获取策略实例的完整状态。

![](img/e6164f36291052047a9b9abc9d4eb130_21.png)

![](img/e6164f36291052047a9b9abc9d4eb130_23.png)

获取策略的整体信息对于监控和管理多个运行中的策略至关重要。例如，在Web界面或GUI中展示策略状态时，就需要用到这些信息。

以下是获取策略整体信息的方法：

![](img/e6164f36291052047a9b9abc9d4eb130_25.png)

![](img/e6164f36291052047a9b9abc9d4eb130_27.png)

```python
def get_strategy_data(self):
    data_dict = {}
    data_dict[‘strategy_name‘] = self.strategy_name
    data_dict[‘vt_symbol‘] = self.vt_symbol
    data_dict[‘parameters‘] = self.get_parameters()
    data_dict[‘variables‘] = self.get_variables()
    return data_dict
```

![](img/e6164f36291052047a9b9abc9d4eb130_29.png)

![](img/e6164f36291052047a9b9abc9d4eb130_31.png)

这个方法返回一个字典，包含了策略的唯一名称、交易合约、所有参数和所有变量的当前值。这样，无论是通过脚本还是可视化界面，都能清晰地了解每个策略的运行概况。

![](img/e6164f36291052047a9b9abc9d4eb130_33.png)

![](img/e6164f36291052047a9b9abc9d4eb130_35.png)

![](img/e6164f36291052047a9b9abc9d4eb130_37.png)

## 委托撤销功能

![](img/e6164f36291052047a9b9abc9d4eb130_39.png)

![](img/e6164f36291052047a9b9abc9d4eb130_41.png)

![](img/e6164f36291052047a9b9abc9d4eb130_43.png)

既然实现了委托发送，那么撤销委托的功能也必须配套实现。撤销功能分为两种：撤销指定委托单和撤销该策略的所有委托。

![](img/e6164f36291052047a9b9abc9d4eb130_44.png)

![](img/e6164f36291052047a9b9abc9d4eb130_46.png)

![](img/e6164f36291052047a9b9abc9d4eb130_48.png)

以下是撤销单个委托的实现：

![](img/e6164f36291052047a9b9abc9d4eb130_50.png)

![](img/e6164f36291052047a9b9abc9d4eb130_51.png)

![](img/e6164f36291052047a9b9abc9d4eb130_53.png)

```python
def cancel_order(self, vt_order_id):
    self.cta_engine.cancel_order(self, vt_order_id)
```

![](img/e6164f36291052047a9b9abc9d4eb130_55.png)

![](img/e6164f36291052047a9b9abc9d4eb130_57.png)

撤销所有委托的实现如下：

![](img/e6164f36291052047a9b9abc9d4eb130_59.png)

![](img/e6164f36291052047a9b9abc9d4eb130_61.png)

![](img/e6164f36291052047a9b9abc9d4eb130_63.png)

```python
def cancel_all(self):
    self.cta_engine.cancel_all(self)
```

![](img/e6164f36291052047a9b9abc9d4eb130_65.png)

在这两个方法中，我们都将策略实例 `self` 传递给CTA引擎。这是因为引擎需要知道是哪个策略发起的撤销请求，以便在其管理的委托单列表中准确找到并处理对应的委托。这再次强调了为每个策略实例设置唯一名称的重要性。

![](img/e6164f36291052047a9b9abc9d4eb130_67.png)

![](img/e6164f36291052047a9b9abc9d4eb130_69.png)

## 数据保存功能

![](img/e6164f36291052047a9b9abc9d4eb130_71.png)

![](img/e6164f36291052047a9b9abc9d4eb130_73.png)

策略的变量数据（如当前仓位）需要被持久化保存，以便在程序重启后能够恢复状态。通常，这个工作会交给CTA引擎统一处理，以提高效率。

![](img/e6164f36291052047a9b9abc9d4eb130_75.png)

![](img/e6164f36291052047a9b9abc9d4eb130_77.png)

![](img/e6164f36291052047a9b9abc9d4eb130_79.png)

以下是数据保存方法的实现：

![](img/e6164f36291052047a9b9abc9d4eb130_81.png)

![](img/e6164f36291052047a9b9abc9d4eb130_83.png)

![](img/e6164f36291052047a9b9abc9d4eb130_85.png)

```python
def sync_data(self):
    pass
    # 通常将数据保存任务委托给CTA引擎
    # self.cta_engine.sync_strategy_data(self)
```

![](img/e6164f36291052047a9b9abc9d4eb130_87.png)

![](img/e6164f36291052047a9b9abc9d4eb130_89.png)

![](img/e6164f36291052047a9b9abc9d4eb130_90.png)

![](img/e6164f36291052047a9b9abc9d4eb130_92.png)

在实际应用中，我们会在 `sync_data` 方法中调用引擎的接口，将策略数据保存到文件或数据库中。当策略停止时，引擎会调用此方法。

![](img/e6164f36291052047a9b9abc9d4eb130_94.png)

![](img/e6164f36291052047a9b9abc9d4eb130_96.png)

![](img/e6164f36291052047a9b9abc9d4eb130_98.png)

![](img/e6164f36291052047a9b9abc9d4eb130_100.png)

![](img/e6164f36291052047a9b9abc9d4eb130_101.png)

![](img/e6164f36291052047a9b9abc9d4eb130_103.png)

## 子类必须实现的方法

![](img/e6164f36291052047a9b9abc9d4eb130_105.png)

![](img/e6164f36291052047a9b9abc9d4eb130_107.png)

策略模板定义了框架，具体的交易逻辑则需要在其子类中实现。以下是一些子类必须重写（或至少定义）的核心方法，它们构成了策略的生命周期和事件响应机制。

![](img/e6164f36291052047a9b9abc9d4eb130_109.png)

![](img/e6164f36291052047a9b9abc9d4eb130_111.png)

![](img/e6164f36291052047a9b9abc9d4eb130_112.png)

![](img/e6164f36291052047a9b9abc9d4eb130_113.png)

![](img/e6164f36291052047a9b9abc9d4eb130_115.png)

![](img/e6164f36291052047a9b9abc9d4eb130_117.png)

以下是子类需要实现的关键方法列表：

![](img/e6164f36291052047a9b9abc9d4eb130_119.png)

![](img/e6164f36291052047a9b9abc9d4eb130_121.png)

![](img/e6164f36291052047a9b9abc9d4eb130_123.png)

![](img/e6164f36291052047a9b9abc9d4eb130_125.png)

1.  **`on_init`**: 策略初始化方法。通常用于下载历史数据，为指标计算做准备。
2.  **`on_start`**: 策略启动方法。用于将策略状态设置为可交易，并通知界面。
3.  **`on_stop`**: 策略停止方法。通常用于保存数据并通知界面。
4.  **`on_tick`**: Tick数据更新回调。处理逐笔行情数据，可用于合成K线或执行高频逻辑。
5.  **`on_bar`**: K线数据更新回调。大多数CTA策略的核心逻辑都在这里实现，包括指标计算和信号产生。
6.  **`on_order`**: 委托状态更新回调。当委托被接受、成交、拒单或撤销时触发。
7.  **`on_stop_order`**: 本地停止单状态更新回调。
8.  **`on_trade`**: 成交回报回调。这是计算真实盈亏、设置止盈止损的最佳位置。

![](img/e6164f36291052047a9b9abc9d4eb130_127.png)

![](img/e6164f36291052047a9b9abc9d4eb130_129.png)

![](img/e6164f36291052047a9b9abc9d4eb130_131.png)

![](img/e6164f36291052047a9b9abc9d4eb130_133.png)

特别需要注意的是，在 `on_tick` 和 `on_bar` 中发送委托前，应检查 `self.trading` 标志，确保策略已进入可交易状态，避免在初始化或回测历史数据时发出无效委托。

![](img/e6164f36291052047a9b9abc9d4eb130_135.png)

![](img/e6164f36291052047a9b9abc9d4eb130_137.png)

![](img/e6164f36291052047a9b9abc9d4eb130_139.png)

![](img/e6164f36291052047a9b9abc9d4eb130_141.png)

## 数据加载方法

![](img/e6164f36291052047a9b9abc9d4eb130_143.png)

![](img/e6164f36291052047a9b9abc9d4eb130_145.png)

为了方便子类在初始化时获取历史数据，模板中提供了一个通用的数据加载方法。

![](img/e6164f36291052047a9b9abc9d4eb130_147.png)

![](img/e6164f36291052047a9b9abc9d4eb130_149.png)

![](img/e6164f36291052047a9b9abc9d4eb130_151.png)

![](img/e6164f36291052047a9b9abc9d4eb130_153.png)

以下是 `load_bar_data` 方法的定义：

![](img/e6164f36291052047a9b9abc9d4eb130_155.png)

![](img/e6164f36291052047a9b9abc9d4eb130_157.png)

![](img/e6164f36291052047a9b9abc9d4eb130_159.png)

![](img/e6164f36291052047a9b9abc9d4eb130_161.png)

```python
def load_bar_data(self, vt_symbol, days=10, interval=Interval.MINUTE, use_database=True, callback=None):
    bars = self.cta_engine.load_bar_data(self, vt_symbol, days, interval, use_database)
    if bars:
        # 如果未指定回调函数，则默认使用on_bar方法处理数据
        on_bar_func = callback or self.on_bar
        for bar in bars:
            on_bar_func(bar)
```

![](img/e6164f36291052047a9b9abc9d4eb130_163.png)

![](img/e6164f36291052047a9b9abc9d4eb130_165.png)

![](img/e6164f36291052047a9b9abc9d4eb130_166.png)

![](img/e6164f36291052047a9b9abc9d4eb130_168.png)

![](img/e6164f36291052047a9b9abc9d4eb130_170.png)

这个方法将数据加载请求转发给CTA引擎，并在获取数据后，逐条推送给指定的回调函数（默认为 `on_bar`）。这种设计使得策略能够灵活加载不同周期（如1分钟、30分钟）的数据。

![](img/e6164f36291052047a9b9abc9d4eb130_172.png)

![](img/e6164f36291052047a9b9abc9d4eb130_174.png)

## 使用Dataclasses管理数据

![](img/e6164f36291052047a9b9abc9d4eb130_176.png)

![](img/e6164f36291052047a9b9abc9d4eb130_178.png)

![](img/e6164f36291052047a9b9abc9d4eb130_180.png)

在策略中，我们需要处理大量的结构化数据，如Tick、Bar、委托、成交等。使用字典来传递这些数据不仅繁琐，而且容易出错。Python的 `dataclasses` 库为此提供了优雅的解决方案。

![](img/e6164f36291052047a9b9abc9d4eb130_182.png)

![](img/e6164f36291052047a9b9abc9d4eb130_183.png)

![](img/e6164f36291052047a9b9abc9d4eb130_184.png)

![](img/e6164f36291052047a9b9abc9d4eb130_186.png)

![](img/e6164f36291052047a9b9abc9d4eb130_188.png)

`dataclass` 是一个装饰器，可以自动生成类中的 `__init__`、`__repr__` 等方法，极大地简化了数据类的创建。

以下是使用 `dataclass` 定义Bar数据类的示例：

```python
from dataclasses import dataclass, field
from typing import List

@dataclass
class BarData:
    symbol: str
    exchange: str
    datetime: str
    open_price: float
    high_price: float
    low_price: float
    close_price: float
    volume: float = 0.0
    # 对于可变默认值（如列表），需使用field
    vt_order_ids: List[str] = field(default_factory=list)

    def __post_init__(self):
        # 该方法在__init__之后自动调用，可用于生成衍生字段
        self.vt_symbol = f"{self.symbol}.{self.exchange}"
```

![](img/e6164f36291052047a9b9abc9d4eb130_190.png)

定义后，可以像使用普通类一样实例化和访问属性：

![](img/e6164f36291052047a9b9abc9d4eb130_192.png)

![](img/e6164f36291052047a9b9abc9d4eb130_194.png)

```python
bar = BarData(symbol=‘RB2405‘, exchange=‘SHFE‘, datetime=‘2023-01-01 09:01:00‘, open_price=5000, high_price=5020, low_price=4990, close_price=5010)
print(bar.vt_symbol)  # 输出: RB2405.SHFE
print(bar.open_price) # 输出: 5000
```

![](img/e6164f36291052047a9b9abc9d4eb130_196.png)

使用 `dataclasses` 能让代码更清晰、更安全，也更容易维护，是管理量化交易中各类数据的推荐方式。

![](img/e6164f36291052047a9b9abc9d4eb130_198.png)

![](img/e6164f36291052047a9b9abc9d4eb130_200.png)

![](img/e6164f36291052047a9b9abc9d4eb130_202.png)

## 总结

![](img/e6164f36291052047a9b9abc9d4eb130_204.png)

![](img/e6164f36291052047a9b9abc9d4eb130_206.png)

本节课中我们一起学习了策略模板编写的进阶内容。我们实现了获取策略完整信息、委托撤销和数据保存的功能。我们明确了子类策略必须实现的几个核心回调方法，它们构成了策略运行的骨架。最后，我们引入了 `dataclasses` 库来高效、规范地管理交易数据。理解并掌握这些内容，是构建健壮、可维护的量化策略系统的关键一步。