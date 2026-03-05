# 从零开始量化：第17课：CtaBackEngine的实现(2) - P1

![](img/f93b738d5868b3cd1303b9de82637ff1_1.png)

在本节课中，我们将继续学习如何实现一个CTA回测引擎。上节课我们介绍了回测引擎的核心逻辑与执行顺序，本节我们将动手编写代码，通过实践来加深理解。我们将从定义回测引擎的类结构开始，逐步实现参数设置、数据加载和策略初始化等关键步骤。

## 设置回测引擎类框架

首先，我们需要创建一个回测引擎类，并定义CTA策略模板可能调用的所有方法。这是因为在实盘中，CTA引擎会调用这些方法，而回测引擎需要提供相同的接口，否则策略代码会报错。

以下是回测引擎类的基本框架，包含了策略可能调用的核心方法：

```python
from vnpy_ctastrategy import CtaTemplate
from vnpy.trader.constant import Interval, Exchange, Direction, Offset
from vnpy.trader.object import BarData, TickData
from typing import Optional, Callable
from vnpy_ctastrategy.base import EngineType
import traceback
import sys

class MyBackEngine:
    def __init__(self):
        self.strategy: Optional[CtaTemplate] = None
        self.vt_symbol: Optional[str] = None
        self.start: Optional[datetime] = None
        self.end: Optional[datetime] = None
        self.interval: Interval = Interval.MINUTE
        self.init_days: int = 10
        self.history_data: list = []
        self.db = None

    def set_parameters(self, vt_symbol: str, start: datetime, end: datetime, interval: Interval = Interval.MINUTE, setting: dict = {}):
        self.vt_symbol = vt_symbol
        self.start = start
        self.end = end
        self.interval = interval
        self.setting = setting

    def set_strategy(self, strategy_class):
        try:
            self.strategy = strategy_class(self, strategy_class.__name__, self.vt_symbol, self.setting)
        except Exception:
            traceback.print_exc()
            sys.exit(1)

    def send_order(self, strategy: CtaTemplate, direction: Direction, offset: Offset, price: float, volume: int, stop: bool = False, lock: bool = False, net: bool = False) -> list:
        pass

    def cancel_order(self, strategy: CtaTemplate, vt_orderid: str) -> None:
        pass

    def cancel_all(self, strategy: CtaTemplate) -> None:
        pass

    def write_log(self, msg: str, strategy: CtaTemplate = None) -> None:
        print(msg)

    def get_engine_type(self) -> EngineType:
        pass

    def get_pricetick(self, strategy: CtaTemplate = None) -> float:
        pass

    def get_size(self, strategy: CtaTemplate = None) -> float:
        pass

    def load_bar(self, vt_symbol: str, days: int, interval: Interval, callback: Callable, use_database: bool) -> list:
        return []

    def load_tick(self, vt_symbol: str, days: int, callback: Callable) -> list:
        return []

    def put_event(self, strategy: CtaTemplate) -> None:
        pass

    def send_email(self, msg: str, strategy: CtaTemplate = None) -> None:
        pass
```

## 实现数据加载功能

上一节我们定义了引擎的骨架，本节中我们来看看如何为引擎填充“血肉”，即实现具体的数据加载功能。这是回测能够运行的基础。

首先，我们需要从数据源（如米筐）获取历史数据，并将其保存到本地数据库中。

以下是实现数据加载的关键步骤：

1.  **导入必要模块**：我们需要使用米筐数据接口和数据库接口。
2.  **创建数据请求**：根据用户设置的合约、时间范围和周期构建数据请求。
3.  **查询并保存数据**：从米筐获取数据，并保存到SQLite数据库中。

```python
from vnpy_rqdata import rqdata_client
from vnpy_sqlite import SqliteDatabase
from vnpy.trader.utility import extract_vt_symbol
from vnpy.trader.object import HistoryRequest

def load_data_rq(self):
    # 初始化数据库连接
    self.db = SqliteDatabase()

    # 拆分vt_symbol为代码和交易所
    symbol, exchange = extract_vt_symbol(self.vt_symbol)

    # 构建历史数据请求
    req = HistoryRequest(
        symbol=symbol,
        exchange=exchange,
        interval=self.interval,
        start=self.start,
        end=self.end
    )

    # 从米筐获取数据
    bars = rqdata_client.query_bar_history(req, output=self.write_log)

    if bars:
        self.write_log(f"从RQ获取数据成功，一共{len(bars)}条")
        # 保存数据到数据库
        res = self.db.save_bar_data(bars)
        if res:
            self.write_log("数据库存储数据成功")
```

## 执行回测与策略初始化

数据准备就绪后，我们就可以开始执行回测了。回测的第一步是初始化策略，这需要从数据库加载数据，并运行策略的`on_init`方法。

以下是执行回测和策略初始化的核心流程：

1.  **从数据库加载数据**：根据参数加载指定时间范围的历史K线数据。
2.  **策略初始化**：遍历历史数据，在达到预设的初始化天数前，调用策略的`on_bar`方法进行初始化计算。

```python
def run_backtest(self):
    # 1. 从数据库加载数据
    symbol, exchange = extract_vt_symbol(self.vt_symbol)
    self.history_data = self.db.load_bar_data(
        symbol, exchange, self.interval, self.start, self.end
    )

    if not self.history_data:
        self.write_log(f"数据库没有合约{self.vt_symbol}的数据")
        sys.exit(1)

    # 2. 策略初始化
    day_count = 0
    last_date = None
    init_bar_count = 0

    for ix, bar in enumerate(self.history_data):
        bar_date = bar.datetime.date()
        if last_date and bar_date != last_date:
            day_count += 1
        last_date = bar_date

        # 如果还在初始化天数内，则调用策略的on_bar方法
        if day_count < self.init_days:
            self.strategy.on_bar(bar)
            init_bar_count = ix
        else:
            # 初始化完成，记录断点并跳出循环
            self.write_log(f"策略初始化结束，共{day_count}天，耗费{init_bar_count + 1}条数据")
            self.strategy.inited = True
            break

    # 初始化后，可以检查策略内部变量是否已计算
    if hasattr(self.strategy, 'fast_ma') and hasattr(self.strategy, 'slow_ma'):
        self.write_log(f"策略初始化后， fast_ma: {self.strategy.fast_ma}, slow_ma: {self.strategy.slow_ma}")
```

![](img/f93b738d5868b3cd1303b9de82637ff1_3.png)

![](img/f93b738d5868b3cd1303b9de82637ff1_5.png)

## 主程序示例

最后，我们需要一个主程序来串联整个流程：设置参数、加载策略、获取数据并运行回测。

以下是一个主程序示例，展示了如何使用我们编写的回测引擎：

```python
# main.py
from my_back_engine import MyBackEngine
from datetime import datetime
import sys
from pathlib import Path
import importlib

# 1. 创建回测引擎实例
engine = MyBackEngine()

# 2. 设置回测参数
engine.set_parameters(
    vt_symbol="rb2305.SHFE",
    start=datetime(2022, 1, 1),
    end=datetime.now(),
    interval=Interval.MINUTE
)

# 3. 动态导入策略类
sys.path.append(str(Path.home()))
module_name = "strategies.avg_demo"
try:
    module = importlib.import_module(module_name)
    strategy_class = getattr(module, "AvgDemoStrategy")
    engine.set_strategy(strategy_class)
except Exception as e:
    print(f"策略导入出错: {e}")
    sys.exit(1)

# 4. 加载数据
engine.load_data_rq()

![](img/f93b738d5868b3cd1303b9de82637ff1_7.png)

# 5. 运行回测
engine.run_backtest()
```

![](img/f93b738d5868b3cd1303b9de82637ff1_9.png)

![](img/f93b738d5868b3cd1303b9de82637ff1_11.png)

本节课中我们一起学习了如何构建一个CTA回测引擎的核心部分。我们从定义类框架开始，实现了参数设置、从外部数据源加载数据、将数据存入数据库，以及最重要的——执行回测并初始化策略。通过动手实践，我们理解了回测引擎需要为策略提供哪些接口，以及数据如何流动。虽然我们只实现了基础的单合约、单策略回测，但这构成了所有复杂回测系统的基石。下节课我们将继续完善这个引擎，实现委托下单、成交仿真和绩效统计等功能。