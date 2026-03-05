# Python量化开发：课时26：可变vs不可变 - P1

在本节课中，我们将要学习Python中一个深入且重要的概念：对象的可变性与不可变性。理解这个概念对于编写正确的、无Bug的量化策略代码至关重要。

## 概述

![](img/735df45110a70483e3ba2ca5fc5e0ff2_1.png)

在上一节课中，我们学习了如何将面向对象的知识与底层交易接口开发结合起来。本节中，我们将探讨Python内部对象的可变性（Mutable）与不可变性（Immutable）。我们将从一个具体的例子开始，揭示初学者常犯的错误，并解释其背后的原理。

## 从一个例子开始：K线序列缓存类

我们首先创建一个名为 `BarSeries` 的K线序列缓存类。这个类的主要作用是在实盘或回测中，当新的K线数据不断传入时，将其缓存下来，用于后续的技术指标或统计分析计算。

以下是这个类的初始定义：

```python
class BarSeries:
    open = []
    high = []
    low = []
    close = []

    def update_bar(self, bar):
        self.open.append(bar['open'])
        self.high.append(bar['high'])
        self.low.append(bar['low'])
        self.close.append(bar['close'])

    def print_data(self):
        print("Open:", self.open)
        print("High:", self.high)
        print("Low:", self.low)
        print("Close:", self.close)
```

这个类定义了四个成员：`open`, `high`, `low`, `close`，它们是四个浮点数列表。`update_bar` 方法用于将一根新K线的数据（以字典形式传入）追加到对应的列表中。`print_data` 方法则用于打印当前缓存的所有数据。

## 遭遇数据冲突

![](img/735df45110a70483e3ba2ca5fc5e0ff2_3.png)

接下来，我们尝试使用这个类，并观察一个意想不到的现象。

```python
# 创建第一根K线数据
bar = {'open': 100, 'high': 102, 'low': 99, 'close': 101}

![](img/735df45110a70483e3ba2ca5fc5e0ff2_5.png)

# 创建第一个缓存实例并更新数据
bs1 = BarSeries()
bs1.update_bar(bar)
bs1.print_data()
```

运行上述代码，输出符合预期，列表中各只有一个数据点。

现在，我们进行第二步操作：创建第二个缓存实例并进行同样的更新。

```python
# 创建第二个缓存实例并更新数据
bs2 = BarSeries()
bs2.update_bar(bar)
bs2.print_data()
```

此时，问题出现了。`bs2` 的打印结果显示，每个列表里都有两条相同的数据，仿佛我们更新了两次。但我们的代码逻辑明明只对 `bs2` 更新了一次。

## 探究原因：`id`函数与内存地址

为了理解这个问题，我们需要引入Python的内置函数 `id()`。`id()` 函数用于获取对象在内存中的地址。

```python
print(id(bs1))  # 输出 bs1 的内存地址
print(id(bs2))  # 输出 bs2 的内存地址
```

我们会发现 `bs1` 和 `bs2` 的地址不同，说明它们是两个不同的对象。这符合我们的认知。

然而，问题的关键不在于实例本身，而在于实例的成员。让我们检查一下 `bs1.open` 和 `bs2.open` 的地址：

```python
print(id(bs1.open))  # 输出 bs1.open 列表的内存地址
print(id(bs2.open))  # 输出 bs2.open 列表的内存地址
```

**结果：`bs1.open` 和 `bs2.open` 的地址是相同的！**

这意味着，尽管 `bs1` 和 `bs2` 是两个不同的实例对象，但它们内部的 `open` 列表（以及 `high`, `low`, `close` 列表）却指向内存中**同一个列表对象**。因此，无论通过哪个实例更新数据，实际上都是在修改这同一个列表，从而导致了数据“冲突”。

## 类成员 vs 实例成员

上一节我们介绍了问题现象，本节中我们来看看其根本原因。在Python的类定义中，成员变量有两种：

1.  **类成员（Class Attribute）**：在 `class` 关键词下方直接定义的变量。它属于类本身，所有由这个类创建的实例**共享**同一个类成员。
2.  **实例成员（Instance Attribute）**：通常在 `__init__` 方法中，通过 `self.xxx = ...` 定义的变量。它属于实例对象，每个实例都拥有自己独立的一份。

![](img/735df45110a70483e3ba2ca5fc5e0ff2_7.png)

在我们的 `BarSeries` 初始定义中，`open`, `high`, `low`, `close` 被定义为了**类成员**。因此，所有 `BarSeries` 的实例都共享这四份列表，从而引发了数据混乱。

## 解决方案：使用实例成员

![](img/735df45110a70483e3ba2ca5fc5e0ff2_9.png)

要解决这个问题，我们需要将列表的定义从类成员改为实例成员。正确的做法是在 `__init__` 方法中初始化这些列表。

以下是修改后的 `NewBarSeries` 类：

```python
class NewBarSeries:
    def __init__(self):
        self.open = []
        self.high = []
        self.low = []
        self.close = []

    def update_bar(self, bar):
        self.open.append(bar['open'])
        self.high.append(bar['high'])
        self.low.append(bar['low'])
        self.close.append(bar['close'])

    def print_data(self):
        print("Open:", self.open)
        print("High:", self.high)
        print("Low:", self.low)
        print("Close:", self.close)
```

现在，每次创建 `NewBarSeries` 实例时，`__init__` 方法都会为这个实例创建四个全新的空列表。让我们测试一下：

```python
bar = {'open': 100, 'high': 102, 'low': 99, 'close': 101}

nbs1 = NewBarSeries()
nbs1.update_bar(bar)
nbs1.print_data()

nbs2 = NewBarSeries()
nbs2.update_bar(bar)
nbs2.print_data()

print(id(nbs1.open) == id(nbs2.open))  # 输出：False
```

现在，`nbs1` 和 `nbs2` 的行为完全独立，数据不再冲突，它们的列表地址也不同。这才是我们期望的结果。

## 核心概念：可变对象与不可变对象

![](img/735df45110a70483e3ba2ca5fc5e0ff2_11.png)

理解了上面的例子，我们就可以正式引入Python中**可变（Mutable）**与**不可变（Immutable）**对象的概念。这是造成上述现象的本质原因。

*   **不可变对象（Immutable）**：对象创建后，其内部状态（值）不能被改变。对它的任何“修改”操作，实际上都会创建一个新的对象。
*   **可变对象（Mutable）**：对象创建后，其内部状态可以被改变。修改操作是在原对象上进行的。

以下是Python中常见类型的分类：

**不可变对象示例：**
*   **基本类型**：`int`, `float`, `bool`, `None`
*   **字符串**：`str` （注意：Python中的字符串是不可变的，这与某些语言不同）
*   **元组**：`tuple`
*   **自定义类的实例**（通常，除非你特意让它可变）

**可变对象示例：**
*   **列表**：`list`
*   **字典**：`dict`
*   **集合**：`set`

### 赋值操作的区别

![](img/735df45110a70483e3ba2ca5fc5e0ff2_13.png)

对于不可变对象，赋值操作是安全的：

```python
a = 999
b = a        # b 和 a 指向内存中同一个 999
b = b + 100  # 由于 int 不可变，这里会创建一个新的整数 1099 并让 b 指向它
print(a)     # 输出：999，a 的值没有改变
print(b)     # 输出：1099
```

对于可变对象，赋值操作可能导致“意外联动”：

```python
c = [1, 2, 3]
d = c          # d 和 c 指向内存中同一个列表对象
d.append(123)  # 列表是可变对象，这个操作直接修改了内存中的那个列表
print(c)       # 输出：[1, 2, 3, 123]，c 也“被改变”了！
print(d)       # 输出：[1, 2, 3, 123]
```

![](img/735df45110a70483e3ba2ca5fc5e0ff2_15.png)

**关键点**：`d = c` 这个操作，对于可变对象来说，并没有创建一个新的列表，而只是让变量 `d` 也指向了变量 `c` 所指向的**同一个列表对象**。因此，通过 `d` 修改列表，`c` 看到的内容自然也会变化。

## 总结

本节课中我们一起学习了Python中对象的可变性与不可变性。

1.  我们从一个K线缓存类的Bug入手，发现了由于错误地将列表定义为**类成员**，导致所有实例共享数据的问题。
2.  通过使用 `id()` 函数，我们探查到问题的根源是多个实例的成员指向了内存中的**同一个可变对象（列表）**。
3.  我们通过将类成员改为在 `__init__` 中初始化的**实例成员**，解决了数据冲突问题。
4.  最后，我们深入探讨了**可变对象**（如 `list`, `dict`, `set`）和**不可变对象**（如 `int`, `float`, `str`, `tuple`）的核心区别，并理解了赋值操作对它们的不同影响。

![](img/735df45110a70483e3ba2ca5fc5e0ff2_17.png)

理解可变与不可变是掌握Python编程的关键一步，它能帮助你避免许多隐蔽的Bug，尤其是在构建复杂的量化交易系统时。在下一节课中，我们将进一步探讨这个概念在实践中的更多应用和注意事项。