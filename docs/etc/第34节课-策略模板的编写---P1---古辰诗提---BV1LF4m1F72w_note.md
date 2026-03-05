# 从零开始量化：第34课：策略模板的编写 - P1 🧱

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_1.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_3.png)

在本节课中，我们将学习如何编写一个CTA策略模板。策略模板是所有具体策略的“蓝图”或“父类”，它定义了策略的通用结构和功能，而具体的交易逻辑则由继承它的子类去实现。通过构建模板，我们可以实现代码复用，并确保不同策略之间接口的统一。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_5.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_7.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_9.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_11.png)

## 策略模板的作用与结构 📐

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_13.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_15.png)

策略模板的核心作用是为所有具体策略提供一个统一的框架。它主要包含两部分功能：

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_17.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_19.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_21.png)

1.  **公用功能**：所有策略都需要的基础操作，如发送委托、输出日志、参数管理等。这些功能在模板中直接实现。
2.  **需单独实现的功能**：每个策略独有的交易逻辑，如处理行情数据（tick）、合成K线、处理成交等。这些功能在模板中定义为抽象方法，强制子类去实现。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_23.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_25.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_27.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_29.png)

这种设计模式使得一个模板可以被多个不同的策略继承，而每个策略实例（例如，同时交易螺纹钢和焦炭）又可以拥有自己独立的参数和状态。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_31.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_33.png)

上一节我们介绍了策略模板的基本概念，本节中我们来看看如何用代码构建这个模板。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_35.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_37.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_39.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_41.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_43.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_45.png)

## 构建基础引擎类 🚂

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_47.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_49.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_51.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_53.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_54.png)

在编写策略模板前，我们需要先定义一个基础的引擎类。策略模板并不直接与交易所或回测系统交互，而是通过一个“引擎”来中转。这个引擎可以是实盘引擎、回测引擎或模拟引擎。为了让模板能适配不同类型的引擎，我们首先创建一个引擎基类，规定所有引擎必须实现的接口。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_56.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_58.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_60.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_62.png)

以下是引擎基类的代码框架：

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_64.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_66.png)

```python
# engine.py
from abc import ABC, abstractmethod

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_68.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_69.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_71.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_73.png)

class BaseEngine(ABC):
    """
    引擎基类，定义了所有引擎（实盘、回测、模拟）必须实现的接口。
    """
    @abstractmethod
    def send_order(self, strategy, price: float, volume: int, direction, offset, stop: bool = True):
        """发送委托的抽象方法"""
        pass

    @abstractmethod
    def write_log(self, strategy, message: str):
        """输出日志的抽象方法"""
        pass
```

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_75.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_77.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_79.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_81.png)

同时，我们定义交易方向和开平仓的枚举类，使代码更清晰、不易出错：

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_83.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_85.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_87.png)

```python
# engine.py
from enum import Enum

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_89.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_91.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_93.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_95.png)

class Direction(Enum):
    """交易方向枚举"""
    LONG = "多"
    SHORT = "空"

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_97.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_98.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_100.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_102.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_104.png)

class Offset(Enum):
    """开平仓枚举"""
    OPEN = "开"
    CLOSE = "平"
```

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_106.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_108.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_110.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_112.png)

## 编写策略模板类 📝

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_114.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_116.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_118.png)

现在，我们可以开始编写核心的策略模板类 `CtaTemplate`。它将继承Python的 `ABC`（抽象基类），并包含我们之前讨论的两部分功能。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_120.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_122.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_124.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_126.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_128.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_129.png)

首先进行必要的导入并定义类：

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_131.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_133.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_135.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_137.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_139.png)

```python
# cta_template.py
from abc import ABC, abstractmethod
import copy
from engine import Direction, Offset, BaseEngine

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_141.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_143.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_145.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_147.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_149.png)

class CtaTemplate(ABC):
    """
    CTA策略模板类。
    所有具体策略都应继承此类，并实现其中的抽象方法。
    """
    # 类变量：用于存储所有子类共用的参数名和变量名列表
    parameters = []
    variables = []
```

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_151.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_153.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_155.png)

### 初始化方法 `__init__`

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_157.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_159.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_161.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_163.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_165.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_167.png)

在 `__init__` 方法中，我们需要初始化策略实例的基本属性，如名称、合约、引擎、参数等，并处理参数和变量的赋值。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_169.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_171.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_173.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_175.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_177.png)

```python
    def __init__(self, cta_engine: BaseEngine, strategy_name: str, vt_symbol: str, setting: dict):
        """
        初始化策略实例。
        :param cta_engine: 策略引擎（实盘、回测、模拟）
        :param strategy_name: 策略实例名称
        :param vt_symbol: 交易的合约代码
        :param setting: 参数字典
        """
        self.cta_engine = cta_engine
        self.strategy_name = strategy_name
        self.vt_symbol = vt_symbol
        self.setting = setting

        # 实例变量：仓位、初始化状态、交易状态
        self.pos = 0
        self.inited = False
        self.trading = False

        # 关键步骤：复制类变量给实例变量，避免多个实例共享同一列表
        self.variables = copy.copy(self.variables)
        # 将关键状态变量插入实例的变量列表，便于监控
        self.variables.insert(0, self.trading)
        self.variables.insert(1, self.inited)
        self.variables.insert(2, self.pos)

        # 根据传入的设置更新实例参数
        self.update_setting(setting)
```

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_179.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_181.png)

### 参数更新方法 `update_setting`

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_183.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_185.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_187.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_189.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_191.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_192.png)

此方法用于将传入的参数字典 `setting` 中的值，赋给实例中对应的属性。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_194.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_196.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_198.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_199.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_201.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_203.png)

```python
    def update_setting(self, setting: dict):
        """
        用传入的字典更新策略实例的参数。
        :param setting: 参数字典，key为参数名，value为参数值
        """
        for name in setting.keys():
            if name in self.parameters:  # 只更新在类变量parameters中定义的参数
                value = setting[name]
                setattr(self, name, value)  # 动态地为实例设置属性
```

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_205.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_207.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_209.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_211.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_213.png)

### 委托发送相关方法

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_215.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_216.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_218.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_220.png)

以下是发送委托的核心方法及其便捷方法。核心的 `send_order` 方法调用引擎的功能，而 `buy`, `sell`, `short`, `cover` 方法则提供了更直观的接口。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_222.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_224.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_226.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_228.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_230.png)

```python
    # 抽象方法：发送委托，具体由引擎实现
    @abstractmethod
    def send_order(self, strategy, price: float, volume: int, direction: Direction, offset: Offset, stop: bool = True):
        pass

    # 便捷方法：开多仓
    def buy(self, price: float, volume: int, stop: bool = True):
        return self.send_order(self, price, volume, Direction.LONG, Offset.OPEN, stop)

    # 便捷方法：平多仓
    def sell(self, price: float, volume: int, stop: bool = True):
        return self.send_order(self, price, volume, Direction.SHORT, Offset.CLOSE, stop)

    # 便捷方法：开空仓
    def short(self, price: float, volume: int, stop: bool = True):
        return self.send_order(self, price, volume, Direction.SHORT, Offset.OPEN, stop)

    # 便捷方法：平空仓
    def cover(self, price: float, volume: int, stop: bool = True):
        return self.send_order(self, price, volume, Direction.LONG, Offset.CLOSE, stop)
```

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_232.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_234.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_236.png)

### 日志与参数管理方法

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_238.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_240.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_242.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_244.png)

策略需要记录运行日志，并且能够获取当前实例或类的参数设置。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_246.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_248.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_250.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_252.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_254.png)

```python
    # 抽象方法：写日志，具体由引擎实现
    @abstractmethod
    def write_log(self, strategy, message: str):
        pass

    def get_parameters(self):
        """
        获取当前策略实例的参数（字典形式）。
        """
        strategy_params = {}
        for name in self.parameters:  # 遍历类变量中的参数名
            value = getattr(self, name, None)  # 从实例中获取该参数的值
            strategy_params[name] = value
        return strategy_params

    @classmethod
    def get_class_parameters(cls):
        """
        获取策略类的默认参数（字典形式）。
        这是一个类方法。
        """
        class_params = {}
        for name in cls.parameters:  # 遍历（子）类变量中的参数名
            value = getattr(cls, name, None)  # 从类中获取该参数的值
            class_params[name] = value
        return class_params
```

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_256.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_258.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_260.png)

### 需子类实现的抽象方法

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_262.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_264.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_266.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_268.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_270.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_272.png)

最后，我们定义那些必须由具体策略子类来实现的抽象方法，它们对应着策略独有的交易逻辑。

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_274.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_275.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_277.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_279.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_281.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_283.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_284.png)

```python
    @abstractmethod
    def on_tick(self, tick):
        """收到Tick行情推送时触发，由子类实现具体逻辑"""
        pass

    @abstractmethod
    def on_bar(self, bar):
        """收到K线数据时触发，由子类实现具体逻辑"""
        pass

    @abstractmethod
    def on_trade(self, trade):
        """收到成交推送时触发，由子类实现具体逻辑（如止盈止损）"""
        pass

    @abstractmethod
    def on_stop(self):
        """策略停止时触发，用于执行清理工作"""
        pass
```

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_286.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_288.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_289.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_291.png)

## 总结 🎯

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_293.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_295.png)

![](img/cf36ec3f724de3c94bb2f027cb8bdb8d_297.png)

本节课中我们一起学习了如何从零开始构建一个CTA策略模板。我们首先理解了策略模板作为“父类”的作用，它分离了**公用功能**（如委托、日志）和**需单独实现的功能**（如交易逻辑）。接着，我们创建了引擎基类 `BaseEngine` 和相关的枚举类来规范接口。最后，我们逐步编写了 `CtaTemplate` 类，实现了初始化、参数管理、委托发送、日志记录等公用功能，并定义了 `on_tick`, `on_bar` 等抽象方法，为具体策略的编写打下了坚实的基础。通过这个模板，后续开发具体策略时，我们只需要关注核心的交易算法即可，极大地提高了开发效率和代码的可维护性。