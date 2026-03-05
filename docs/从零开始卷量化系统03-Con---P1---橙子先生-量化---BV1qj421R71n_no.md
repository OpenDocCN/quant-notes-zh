# 量化交易系统：03：Context类与相关函数 - P1 🧩

在本节课中，我们将学习量化交易系统中一个非常核心的概念：**Context类**。我们将了解它的作用、如何定义，以及与之配套的一些基础函数。理解这些内容，是构建和编写量化策略的重要基础。

---

## 概述 📋

Context类在各类量化交易平台中都非常常见。它的主要好处是能够将所有相关的信息封装在一起，并通过一个类实例变量进行传递。这使得在策略中编写代码更加方便和清晰。

为了让大家更好地理解，特别是对Python面向对象编程不太熟悉的同学，我们将从基础讲起，并会做一些修改来展示关键概念。

---

## 定义Context类 🏗️

首先，我们来看如何定义一个名为`Context`的类。这个类代表了交易的“上下文”环境。

```python
class Context:
    class_attribute = "这是一个类属性，所有实例共享"
```

上面这行代码定义了一个**类属性**。它类似于从父母那里遗传来的DNA，对于由这个类生成的每一个具体实例（对象）来说，这个属性是共享且不可变的（除非通过类名修改）。

---

## 初始化函数：`__init__`

上一节我们介绍了类的定义，本节中我们来看看如何为类创建具体的实例。`__init__`函数在每次创建类的新实例时都会自动执行。

通俗地解释：假设你有一个玩具工厂，专门生产“汽车”类。每生产一辆具体的汽车（比如一辆红色新能源车或一辆蓝色油车），这就是一次“实例化”。无论生产什么车，每辆车都必须有一些基本属性，比如颜色、车轮数、长度等。`__init__`函数的作用就是在实例化时，为这个具体的对象组装好这些它应该具备的属性。

在量化中，我们调用`Context`类时，它会通过`__init__`函数准备好所有必要的信息，方便后续复用，无需重复编写。

以下是`__init__`函数内部的属性，它们被称为**实例属性**，属于每个具体的对象。

```python
class Context:
    class_attribute = "这是一个类属性"

    def __init__(self, cash, portfolio):
        self.cash = cash          # 实例属性：可用现金
        self.portfolio = portfolio # 实例属性：投资组合
```

![](img/d7b083e54097385005bbebe8cdd2ae10_1.png)

![](img/d7b083e54097385005bbebe8cdd2ae10_1.png)

---

## 类的方法

在定义了初始化函数之后，一个类通常还会有一些“方法”。方法是类能够执行的操作。

例如，我们可以定义一个简单的方法来打印当前账户的资产情况。虽然在这个简单策略里并非必需，但有助于理解。

![](img/d7b083e54097385005bbebe8cdd2ae10_3.png)

```python
class Context:
    # ... 之前的属性定义 ...

    def print_balance(self):
        print(f"现金余额：{self.cash}")
        print(f"投资组合：{self.portfolio}")
```

![](img/d7b083e54097385005bbebe8cdd2ae10_3.png)

---

![](img/d7b083e54097385005bbebe8cdd2ae10_5.png)

## 全局变量与实例化

接下来，我们定义一个全局变量。它在各个函数中都是有效的，除非在某个函数内部用`global`关键字进行了特殊声明。

```python
# 全局变量，在整个程序中都有效
initial_cash = 100000
```

![](img/d7b083e54097385005bbebe8cdd2ae10_5.png)

现在，我们需要对`Context`类进行**实例化**。实例化是必须的，因为在策略开始运行前，总有一些初始信息是固定的，比如初始投入的资金。量化平台通常会提供一个可视化输入框让你填写这些数字，其背后原理就是对`Context`类的封装和实例化。这样做便于后续策略逻辑的使用。

本节课的核心操作就是完成这个实例化过程。

```python
# 实例化一个Context对象
context = Context(cash=initial_cash, portfolio={})
```

![](img/d7b083e54097385005bbebe8cdd2ae10_7.png)

![](img/d7b083e54097385005bbebe8cdd2ae10_7.png)

---

## 相关辅助函数 ⚙️

在搭建量化系统时，除了核心的Context类，我们还需要一些辅助函数。下面我们来逐一介绍。

### 设置基准函数

在评价一个基金经理或策略时，我们通常不是只看绝对收益，而是将其与某个市场基准（如沪深300指数）进行比较，这样才能计算超额收益（Alpha）。因此，我们需要一个函数来设置这个基准。

![](img/d7b083e54097385005bbebe8cdd2ae10_9.png)

这个示例函数使用一只股票作为基准，你可以轻松地修改为使用指数价格。

```python
def set_benchmark(security):
    # 设置策略比较的基准
    benchmark = security
    return benchmark
```

![](img/d7b083e54097385005bbebe8cdd2ae10_11.png)

![](img/d7b083e54097385005bbebe8cdd2ae10_9.png)

### 获取历史数据函数

![](img/d7b083e54097385005bbebe8cdd2ae10_13.png)

策略的运行离不开历史数据。以下函数用于获取指定股票的历史行情数据，其名称`attribute_history`借鉴了主流量化平台的设计。

![](img/d7b083e54097385005bbebe8cdd2ae10_15.png)

```python
def attribute_history(security, count, field):
    # 获取历史数据的基础函数
    # security: 股票代码
    # count: 获取的数据条数
    # field: 需要获取的字段，如 'close'
    pass
```

![](img/d7b083e54097385005bbebe8cdd2ae10_11.png) ![](img/d7b083e54097385005bbebe8cdd2ae10_13.png) ![](img/d7b083e54097385005bbebe8cdd2ae10_15.png)

![](img/d7b083e54097385005bbebe8cdd2ae10_17.png)

以下是该函数一个更具体的实现示例，它包含了数据获取和处理的完整流程：

```python
import pandas as pd
from datetime import datetime

def get_history_data(security, start_date, end_date, fields=['open', 'close', 'high', 'low', 'volume']):
    """
    获取历史数据函数
    """
    # 将字符串日期转换为时间格式，以便计算机识别
    start = datetime.strptime(start_date, '%Y-%m-%d')
    end = datetime.strptime(end_date, '%Y-%m-%d')

    # 模拟：通过API获取股票日线行情数据
    # 这里假设有一个 get_stock_data 的函数
    # all_data = get_stock_data(security, start, end)

    # 对获取的数据进行截取，只保留需要的字段
    # history_data = all_data[fields]
    # return history_data
    return pd.DataFrame() # 示例返回一个空DataFrame
```

![](img/d7b083e54097385005bbebe8cdd2ae10_17.png) ![](img/d7b083e54097385005bbebe8cdd2ae10_19.png)

![](img/d7b083e54097385005bbebe8cdd2ae10_19.png)

### 获取今日行情函数

策略在每日运行时需要获取当天的行情数据。这个函数负责处理这个任务。

![](img/d7b083e54097385005bbebe8cdd2ae10_21.png)

```python
def get_today_data(security):
    """
    获取今日行情
    """
    # 获取今天的日期
    today = datetime.now().strftime('%Y-%m-%d')

    # 对日期进行同样的标准化处理
    try:
        # 尝试调用API获取今日数据
        # today_data = api_get_data(security, today)
        # return today_data
        return {"price": 100, "volume": 10000} # 示例数据
    except KeyError:
        # 如果出错（例如今天非交易日），则返回None或错误信息
        print(f"无法获取{security}在{today}的数据")
        return None
```

![](img/d7b083e54097385005bbebe8cdd2ae10_21.png) ![](img/d7b083e54097385005bbebe8cdd2ae10_23.png)

---

## 总结 🎯

本节课中，我们一起学习了量化系统基础构建模块的第二部分：

![](img/d7b083e54097385005bbebe8cdd2ae10_23.png)

1.  **Context类**：我们了解了它的核心作用——封装交易上下文信息。学习了如何定义类属性、实例属性以及初始化方法`__init__`。
2.  **实例化**：我们明白了为何需要对类进行实例化，以及如何用初始资金等参数创建一个具体的context对象。
3.  **相关函数**：我们介绍了几个关键的辅助函数，包括设置基准的`set_benchmark`、获取历史数据的`get_history_data`以及获取当日行情的`get_today_data`。这些函数为策略提供了必要的数据支持。

![](img/d7b083e54097385005bbebe8cdd2ae10_25.png)

理解并掌握Context类和这些基础函数，是迈向自主编写量化策略的重要一步。在接下来的课程中，我们将学习如何将这些模块组合起来，形成一个完整的策略框架。

![](img/d7b083e54097385005bbebe8cdd2ae10_25.png)