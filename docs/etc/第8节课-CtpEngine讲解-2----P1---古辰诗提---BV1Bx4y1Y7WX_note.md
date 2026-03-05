# 从零开始量化：第8课：CtaEngine讲解(2)

在本节课中，我们将继续深入讲解VNPY框架中的CtaEngine，重点分析其动态导入策略、初始化策略以及相关配置加载的核心流程。我们将通过解析代码，理解策略是如何被加载、实例化并初始化的。

![](img/398aa59823362630f55eee2772285d0b_1.png)

![](img/398aa59823362630f55eee2772285d0b_3.png)

上一节我们介绍了动态导入的基本概念，本节中我们来看看CtaEngine内部是如何具体实现策略的动态加载与管理的。

## 策略类的动态加载

![](img/398aa59823362630f55eee2772285d0b_5.png)

![](img/398aa59823362630f55eee2772285d0b_7.png)

CtaEngine通过`load_strategy_class`方法从指定路径加载所有策略类。以下是其核心步骤：

### 1. 确定策略文件搜索路径

首先，引擎会确定两个存放策略文件的路径。第一个路径是相对于CTA模块本身的`strategies`文件夹。第二个路径是用户主目录下的特定文件夹。

```python
# 示例：获取第一个路径（模块所在路径）
path1 = Path(__file__).parent.joinpath("strategies")
# 示例：获取第二个路径（用户主目录路径）
path2 = Path.home().joinpath(".vntrader", "strategies")
```

这里有一个关键点：为什么第二个路径会指向C盘用户目录？这是因为在主引擎`MainEngine`初始化时，执行了`os.chdir(Path.home())`，将当前工作目录切换到了用户主目录。因此，`Path.cwd()`获取到的就是用户主目录路径。

### 2. 从文件夹加载策略类

确定了路径后，引擎会遍历这些路径下所有符合特定后缀名的文件（`.py`, `.pyd`, `.so`），这些文件被视为策略模块。

以下是加载过程的代码逻辑概述：

```python
def load_strategy_class_from_folder(path: Path):
    for suffix in [".py", ".pyd", ".so"]:
        for file_path in path.glob(f"*{suffix}"):
            module_name = file_path.stem  # 获取文件名（不含后缀）作为模块名
            # 后续会尝试导入这个模块
```

`.pyd`文件是Windows平台下Python的C扩展模块，`.so`文件是Linux/Unix平台下的共享库文件。引擎通过这种方式兼容纯Python策略和用C/C++编写的策略。

### 3. 动态导入策略模块

获取到模块名后，引擎尝试动态导入该模块。这里利用了Python的`importlib`模块。

```python
import importlib
try:
    module = importlib.import_module(f"vnpy_ctastrategy.strategies.{module_name}")
    module = importlib.reload(module)  # 重载模块，使代码修改立即生效
except ImportError as e:
    print(f"策略文件加载失败：{e}")
```

一个重要的细节是：为什么导入时使用`vnpy_ctastrategy.strategies.{module_name}`这个路径就能成功？这是因为在`utility.py`等模块被导入时，系统路径`sys.path`中已经添加了用户主目录路径（即`Path.home()`）。这个添加操作发生在模块顶层代码中，因此在导入`utility`时自动执行，使得用户目录下的策略模块可以被找到。

![](img/398aa59823362630f55eee2772285d0b_9.png)

### 4. 筛选策略类

![](img/398aa59823362630f55eee2772285d0b_11.png)

导入模块后，需要从模块中筛选出真正的策略类。策略类必须继承自`CtaTemplate`基类，并且不能是`CtaTemplate`本身。

以下是筛选逻辑：

```python
for name in dir(module):
    value = getattr(module, name)
    # 判断：value是一个类，且是CtaTemplate的子类，且不是CtaTemplate本身
    if (isinstance(value, type) and
        issubclass(value, CtaTemplate) and
        value is not CtaTemplate):
        class_name = value.__name__
        # 将策略类存入字典，类名作为键
        self.classes[class_name] = value
```

`value.__name__`可以获取类的名称。这就是为什么在同一个策略文件中，类名必须是唯一的，因为类名将作为在引擎中标识该策略类的唯一键。

## 策略的加载与实例化

加载完策略类后，CtaEngine通过`load_strategy_setting`和`load_strategy_data`方法加载策略的配置和运行数据，然后实例化策略。

### 1. 加载策略配置与数据

策略配置（如参数）和运行数据（如变量值）通常以JSON格式文件存储。

```python
# 加载策略配置
settings = load_json("cta_strategy_setting.json")
# 加载策略数据
data = load_json("cta_strategy_data.json")
```

`load_json`和`save_json`函数负责文件的读写。它们的一个重要功能是自动处理文件路径。默认情况下，文件存储在用户主目录下的`.vntrader`文件夹中。`get_file_path`函数封装了路径获取逻辑，支持自定义交易目录，便于实现多账户隔离管理。

### 2. 实例化策略对象

引擎遍历配置字典，为每个配置创建策略实例。

```python
for strategy_name, strategy_config in settings.items():
    class_name = strategy_config["class_name"]
    vt_symbol = strategy_config["vt_symbol"]

    # 检查是否重名
    if strategy_name in self.strategies:
        print("创建策略实例失败，存在重名")
        continue

    # 获取策略类
    strategy_class = self.classes.get(class_name)
    if not strategy_class:
        print("创建策略实例失败，找不到策略类")
        continue

    # 实例化策略
    strategy = strategy_class(
        self, strategy_name, vt_symbol, strategy_config["setting"]
    )
    # 存储策略实例
    self.strategies[strategy_name] = strategy
```

实例化时，需要传入引擎实例本身、策略实例名称、交易合约代码`vt_symbol`以及参数字典。策略实例名称是运行时的唯一标识，因此不能重复。

## 策略的初始化

所有策略加载完毕后，引擎调用`init_all_strategies`方法初始化每一个策略实例。

### 1. 初始化流程

初始化主要在每个策略的`on_init`方法中完成，通常包括历史数据下载等操作。

![](img/398aa59823362630f55eee2772285d0b_13.png)

![](img/398aa59823362630f55eee2772285d0b_15.png)

```python
def _init_strategy(self, strategy_name):
    strategy = self.strategies[strategy_name]
    if strategy.inited:
        return

    # 调用策略的on_init方法
    self.call_strategy_func(strategy, strategy.on_init)

    # 加载本地保存的变量数据
    data = self.strategy_data.get(strategy_name, {})
    for name, value in data.items():
        setattr(strategy, name, value)  # 为策略属性赋值

    # 订阅行情
    contract = self.main_engine.get_contract(strategy.vt_symbol)
    if contract:
        self.main_engine.subscribe(contract.symbol, contract.exchange)
    else:
        print("行情订阅失败")

    strategy.inited = True
    # 发送策略初始化完成的事件，更新GUI界面
    self.put_strategy_event(strategy)
```

`call_strategy_func`是一个包装函数，用于安全地调用策略方法，并捕获异常，防止单个策略出错影响整个引擎。

### 2. 一个需要注意的问题

在初始化过程中，有一个顺序问题需要注意：策略`on_init`方法中通常会通过`load_bar`等函数计算并初始化一些变量（如指标）。随后，引擎又会从本地文件`strategy_data`中加载之前保存的变量值，并覆盖掉刚刚计算出的值。

如果策略中途停止运行（例如昨天没开机），那么本地保存的变量值就是前天的状态。今天开机初始化时，`on_init`计算出的最新变量值会被旧的本地值覆盖，导致策略状态不准确。

最简单的解决方法是：在策略中断运行后重启前，删除对应的`cta_strategy_data.json`文件，让策略完全重新初始化。

## 事件注册与传递

CtaEngine需要接收来自网关（Gateway）的数据（如行情、成交、委托等），并将其传递给对应的策略。这是通过向主引擎的事件引擎（Event Engine）注册事件监听函数实现的。

```python
# 注册事件处理函数
self.main_engine.register(EVENT_TICK, self.process_tick_event)
self.main_engine.register(EVENT_ORDER, self.process_order_event)
self.main_engine.register(EVENT_TRADE, self.process_trade_event)
# 注意：默认不注册账户资金事件，如需使用需手动添加
# self.main_engine.register(EVENT_ACCOUNT, self.process_account_event)
```

注册后，当事件引擎接收到相应事件时，就会调用CtaEngine中对应的`process_xxx_event`方法，进而将数据分发到各个活跃的策略实例中。

---

![](img/398aa59823362630f55eee2772285d0b_17.png)

本节课中我们一起学习了CtaEngine加载和初始化策略的完整流程。我们从动态导入策略类开始，分析了搜索路径、模块导入和类筛选的机制。接着，我们探讨了如何通过JSON文件加载策略配置和运行数据，并实例化策略对象。最后，我们深入了解了策略初始化的步骤、其中可能遇到的问题以及引擎内部的事件注册机制。理解这些底层流程，将帮助我们在开发和使用策略时更加得心应手，并能有效排查相关问题。