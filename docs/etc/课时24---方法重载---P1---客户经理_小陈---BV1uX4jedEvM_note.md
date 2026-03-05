# Python量化开发：课时24：方法重载

在本节课中，我们将要学习面向对象编程中的一个重要概念——**方法重载**。我们将了解如何通过重载子类中的方法，来改变或扩展从父类继承而来的行为。

## 概述

![](img/1f803461fb0d8c15b70aacc358ce8b26_1.png)

上一节我们介绍了类的继承概念。本节中，我们来看看继承一个父类之后，如何通过**方法重载**来实现子类中特定方法的行为。

## 什么是方法重载？

重载的英文是 **overload**，意为“重新加载”或“重新实现”。它指的是在子类中重新定义从父类继承来的方法，以改变其行为。

我们有一个父类 `TradeData`，它实现了一个 `to_string` 方法。这个方法的作用是将内部的成交细节数据，转换成一个适合人类阅读的字符串。

```python
class TradeData:
    def to_string(self):
        # 将数据转换为字符串的逻辑
        return text
```

当我们从 `TradeData` 继承出一个子类 `FutureTradeData` 时，子类自然也就继承了父类的 `to_string` 方法。方法重载要做的，就是在子类 `FutureTradeData` 中，重新定义（覆盖）这个 `to_string` 方法，以实现一些额外的功能，例如输出期货交易特有的保证金信息。

## 代码演示：实现方法重载

以下是实现方法重载的步骤。

首先，我们保留父类 `TradeData` 和子类 `FutureTradeData` 的定义。在子类中，我们重新编写 `to_string` 方法。

```python
class FutureTradeData(TradeData):
    def to_string(self):
        # 调用父类方法获取基础信息
        base_text = super().to_string()
        # 计算并添加期货特有的保证金信息
        margin = self.calculate_cash_change()
        new_text = f"{base_text}\n消耗保证金: {margin}"
        return new_text
```

注意，在重载方法时，我们通常保持方法的名称、参数和返回值类型不变。这里，我们在子类的方法内部调用了 `self.calculate_cash_change()`，这是一个只在子类中定义的方法，用于计算期货的保证金。

为了使重载的代码生效，需要重新启动Python交互环境或重新加载模块。

## 方法重载的优势：多态

方法重载的一个核心优势是实现**多态**。多态允许我们使用父类类型来引用子类对象，并根据实际对象类型调用相应的方法。

![](img/1f803461fb0d8c15b70aacc358ce8b26_3.png)

以下是一个多态实践的示例。

![](img/1f803461fb0d8c15b70aacc358ce8b26_5.png)

我们在父类中定义一个标准方法 `write_trade_log`。

```python
class TradeData:
    def write_trade_log(self, strategy_name):
        print(f"{strategy_name}的成交数据: {self.to_string()}")
```

然后，在两个子类中分别重载这个方法，添加各自的类型标识。

```python
class StockTradeData(TradeData):
    def write_trade_log(self, strategy_name):
        print(f"[股票] {strategy_name}的成交数据: {self.to_string()}")

class FutureTradeData(TradeData):
    def write_trade_log(self, strategy_name):
        print(f"[期货] {strategy_name}的成交数据: {self.to_string()}")
```

接下来，我们定义一个函数，它接收一个 `TradeData` 类型的参数（父类类型）。

![](img/1f803461fb0d8c15b70aacc358ce8b26_7.png)

```python
def print_strategy_trade_twice(trade, strategy_name):
    trade.write_trade_log(strategy_name)
    trade.write_trade_log(strategy_name)
```

现在，我们可以向这个函数传入 `TradeData`、`StockTradeData` 或 `FutureTradeData` 的实例。函数内部调用的 `write_trade_log` 方法会自动根据传入对象的实际类型来执行，这就是多态。

```python
# 创建不同类型的对象
fx_trade = TradeData(...)
stock_trade = StockTradeData(...)
future_trade = FutureTradeData(...)

# 调用同一个函数，产生不同行为
print_strategy_trade_twice(fx_trade, "DEMO")
print_strategy_trade_twice(stock_trade, "DEMO")
print_strategy_trade_twice(future_trade, "DEMO")
```

对于 `print_strategy_trade_twice` 函数来说，它不需要关心传入的对象具体是哪个子类，只要这些类都继承自 `TradeData`，程序就能正确工作并调用对应的方法。这就是使用**标准类型参数**和**多态**带来的好处。

## 总结

![](img/1f803461fb0d8c15b70aacc358ce8b26_9.png)

本节课中我们一起学习了**方法重载**。我们了解到，通过在子类中重新定义从父类继承的方法，可以改变或扩展该方法的行为。同时，我们探讨了方法重载如何与**多态**概念结合，使得代码能够基于对象的实际类型来调用相应的方法，从而编写出更通用、更灵活的代码。下一节课，我们将把这些概念应用到交易API的实际开发中。