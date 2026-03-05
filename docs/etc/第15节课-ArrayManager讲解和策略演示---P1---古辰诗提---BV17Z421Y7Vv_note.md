# VNPY量化课程：第15课：ArrayManager讲解与策略演示

在本节课中，我们将学习VNPY框架中一个核心组件——ArrayManager。它是策略进行技术指标计算的基础数据容器。我们将深入理解其工作原理，并基于它编写一个经典的双均线交易策略。

## ArrayManager：K线的数据容器

上一节我们介绍了如何将Tick数据合成K线，这是保证数据准确性的关键一步。本节中，我们来看看如何管理和计算这些K线数据。

ArrayManager可以理解为K线的数据容器。它将K线的数值（如开盘价、最高价等）存放在一个固定长度的数组中，并提供了数据更新和指标计算的功能。

### ArrayManager的核心作用

以下是ArrayManager的两个核心功能：

1.  **数据存储**：为技术指标计算提供基础数据。
2.  **数据迭代更新**：当新的K线生成时，ArrayManager会移除最旧的数据，并加入最新的数据，然后重新计算相关指标。

具体的指标计算由`talib`库完成。`talib`是一个成熟的金融技术分析库，ArrayManager为其提供了标准化的数据输入接口。

### 代码结构解析

![](img/35fde2c4a74cea92adffb3ef8a193ebf_1.png)

![](img/35fde2c4a74cea92adffb3ef8a193ebf_3.png)

让我们看一下ArrayManager的关键代码结构。

```python
class ArrayManager(object):
    def __init__(self, size=100):
        self.count = 0
        self.size = size
        self.inited = False

        # 初始化存储各类价格的数组
        self.open_array = np.zeros(size)
        self.high_array = np.zeros(size)
        self.low_array = np.zeros(size)
        self.close_array = np.zeros(size)
        self.volume_array = np.zeros(size)
```

*   `size`参数决定了容器能存放多少根K线。如果你的策略需要计算200周期均线，`size`必须大于200。
*   内部使用`numpy`数组进行高效存储和计算。

### 数据更新机制

数据更新是ArrayManager的核心逻辑，其`update_bar`方法实现了“滑动窗口”功能。

```python
def update_bar(self, bar):
    self.count += 1
    if not self.inited and self.count >= self.size:
        self.inited = True

    # 将数组中的数据向左移动一位，为最新数据腾出位置
    self.open_array[:-1] = self.open_array[1:]
    self.high_array[:-1] = self.high_array[1:]
    self.low_array[:-1] = self.low_array[1:]
    self.close_array[:-1] = self.close_array[1:]
    self.volume_array[:-1] = self.volume_array[1:]

    # 将最新一根K线的数据放入数组末尾
    self.open_array[-1] = bar.open_price
    self.high_array[-1] = bar.high_price
    self.low_array[-1] = bar.low_price
    self.close_array[-1] = bar.close_price
    self.volume_array[-1] = bar.volume
```

这个过程就像排队：新来的人站到队尾，队首的人离开，队伍始终保持固定长度。

### 策略中的初始化

在CTA策略模板中，`load_bar`方法的主要作用就是在策略启动时，用历史数据填充ArrayManager。

```python
def on_init(self):
    self.load_bar(10)  # 下载10天的历史数据来初始化ArrayManager
    self.write_log("策略初始化完成")
```

这一点至关重要。如果ArrayManager未被数据填满（`self.am.inited`为`False`），策略逻辑应直接返回，避免使用未初始化的零值进行计算。

### 指标计算与参数

ArrayManager封装了常用的指标计算方法，例如简单移动平均线（SMA）。

```python
def sma(self, n, array=False):
    """简单移动平均线"""
    result = talib.SMA(self.close_array, n)
    if array:
        return result
    return result[-1]
```

*   `n`：计算周期，例如20日均线则`n=20`。
*   `array`：返回数据类型。`array=False`（默认）只返回最新的指标值；`array=True`返回一个包含所有历史计算值的数组。在判断均线交叉时，需要获取当前值和前一个值，此时必须设置`array=True`。

## 实战：双均线策略开发

理解了ArrayManager后，我们来开发一个经典的双均线交叉策略。策略逻辑很简单：短期均线上穿长期均线时做多（金叉），下穿时做空（死叉）。

![](img/35fde2c4a74cea92adffb3ef8a193ebf_5.png)

![](img/35fde2c4a74cea92adffb3ef8a193ebf_7.png)

### 策略框架与参数定义

首先，我们搭建策略的基本框架并定义所需的参数和变量。

```python
from vnpy.app.cta_strategy import (
    CtaTemplate,
    StopOrder,
    TickData,
    BarData,
    TradeData,
    OrderData,
    BarGenerator,
    ArrayManager
)
from vnpy.trader.constant import Interval

class DoubleMaStrategy(CtaTemplate):
    """双均线策略"""
    author = "Your Name"

    # 策略参数
    fast_window = 20      # 快速均线周期
    slow_window = 60      # 慢速均线周期
    fixed_size = 1        # 每次交易数量

    # 策略变量
    fast_ma = 0.0         # 当前快线值
    slow_ma = 0.0         # 当前慢线值
    fast_ma_last = 0.0    # 上一根K线快线值
    slow_ma_last = 0.0    # 上一根K线慢线值

    parameters = ["fast_window", "slow_window", "fixed_size"]
    variables = ["fast_ma", "slow_ma", "fast_ma_last", "slow_ma_last"]

    def __init__(self, cta_engine, strategy_name, vt_symbol, setting):
        super().__init__(cta_engine, strategy_name, vt_symbol, setting)

        # 创建K线合成器：基于Tick合成1分钟K线，再基于1分钟K线合成30分钟K线
        self.bg = BarGenerator(self.on_bar, window=30, on_window_bar=self.on_30min_bar, interval=Interval.MINUTE)
        # 创建ArrayManager，容量设为150，应大于慢线周期
        self.am = ArrayManager(size=150)
```

### 策略初始化与启动

在初始化方法中，我们需要完成数据加载和ArrayManager的填充。

```python
    def on_init(self):
        """策略初始化"""
        # 加载足够的历史数据用于初始化ArrayManager
        self.load_bar(10)
        self.write_log("策略初始化完成")

    def on_start(self):
        """策略启动"""
        self.write_log("策略启动")
        self.put_event()

    def on_stop(self):
        """策略停止"""
        self.write_log("策略停止")
        self.put_event()
```

### 核心逻辑：30分钟K线回调

策略的核心交易逻辑在`on_30min_bar`方法中实现。

```python
    def on_30min_bar(self, bar: BarData):
        """30分钟K线回调"""
        # 1. 更新ArrayManager数据
        self.am.update_bar(bar)
        if not self.am.inited:
            return

        # 2. 撤销所有未成交委托
        self.cancel_all()

        # 3. 计算双均线数值（需要获取数组以计算前值）
        fast_ma_array = self.am.sma(self.fast_window, array=True)
        slow_ma_array = self.am.sma(self.slow_window, array=True)

        self.fast_ma = fast_ma_array[-1]
        self.slow_ma = slow_ma_array[-1]
        self.fast_ma_last = fast_ma_array[-2]
        self.slow_ma_last = slow_ma_array[-2]

        # 4. 判断金叉与死叉
        # 金叉条件：当前快线 > 慢线，且上一根快线 <= 慢线
        long_cross = (self.fast_ma > self.slow_ma) and (self.fast_ma_last <= self.slow_ma_last)
        # 死叉条件：当前快线 < 慢线，且上一根快线 >= 慢线
        short_cross = (self.fast_ma < self.slow_ma) and (self.fast_ma_last >= self.slow_ma_last)

        # 5. 交易逻辑
        if long_cross:
            # 金叉信号
            if self.pos == 0:
                # 空仓，直接开多
                self.buy(bar.close_price, self.fixed_size)
            elif self.pos < 0:
                # 持有空仓，先平空再开多
                self.cover(bar.close_price, abs(self.pos), stop=True)  # 使用停止单平仓
                self.buy(bar.close_price, self.fixed_size)
        elif short_cross:
            # 死叉信号
            if self.pos == 0:
                # 空仓，直接开空
                self.short(bar.close_price, self.fixed_size)
            elif self.pos > 0:
                # 持有多仓，先平多再开空
                self.sell(bar.close_price, abs(self.pos), stop=True)  # 使用停止单平仓
                self.short(bar.close_price, self.fixed_size)

        # 更新图形界面显示
        self.put_event()
```

**关键点说明**：
1.  **数据准备**：首先更新ArrayManager并检查其是否就绪。
2.  **撤销旧单**：在新的K线周期开始时，撤销上一个周期可能未成交的限价单，避免订单堆积。
3.  **指标计算**：计算双均线时，通过`array=True`获取数组，以便得到当前值和前一个值用于交叉判断。
4.  **交叉判断**：金叉和死叉的判断必须包含等号（`<=` 或 `>=`），以处理均线值恰好相等的情况。
5.  **仓位管理**：
    *   开仓前检查当前持仓状态。
    *   反向信号出现时，需要先平掉原有仓位，再开新仓。平仓操作建议使用停止单（`stop=True`）以确保尽快成交。
    *   注意平仓和开仓指令的发送可能因网络或行情导致成交顺序不确定，在更复杂的策略中需要更精细的状态管理。

### 其他必要方法

策略还需要实现以下基础方法来处理Tick数据、订单和成交回报。

```python
    def on_tick(self, tick: TickData):
        """Tick数据回调"""
        # 将Tick推送至K线合成器
        self.bg.update_tick(tick)

    def on_bar(self, bar: BarData):
        """1分钟K线回调"""
        # 将1分钟K线推送至更高级别的K线合成器
        self.bg.update_bar(bar)

    def on_order(self, order: OrderData):
        """委托回报"""
        pass

    def on_trade(self, trade: TradeData):
        """成交回报"""
        # 成交后更新持仓和界面
        self.put_event()

    def on_stop_order(self, stop_order: StopOrder):
        """停止单回报"""
        pass
```

## 总结

本节课中，我们一起学习了VNPY中至关重要的`ArrayManager`组件。我们了解到：

1.  **ArrayManager的本质**：它是一个固定长度的K线数据容器，负责存储、更新K线数据，并为技术指标计算提供接口。
2.  **核心机制**：其`update_bar`方法通过滑动窗口的方式实现数据的迭代更新，`talib`库是其背后强大的计算引擎。
3.  **策略开发实践**：我们运用ArrayManager，从头构建了一个完整的双均线交叉策略。这个过程涵盖了策略参数定义、数据初始化、K线合成、指标计算、信号判断以及包含基本风控的订单管理。

![](img/35fde2c4a74cea92adffb3ef8a193ebf_9.png)

虽然这个双均线策略逻辑简单，但它清晰地展示了如何将ArrayManager集成到策略中，并点明了实盘开发中需要注意的关键细节，如数据初始化检查、订单撤销、交叉判断的边界条件以及平仓/开仓的顺序逻辑。掌握这些基础是开发更复杂、更稳健量化策略的必经之路。