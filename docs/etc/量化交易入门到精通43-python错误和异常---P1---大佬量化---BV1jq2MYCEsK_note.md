# Python量化交易入门：43：错误与异常处理 🐍

在本节课中，我们将要学习Python中的错误和异常处理机制。对于编写健壮的程序，尤其是在量化交易领域，有效处理错误和异常至关重要，它能帮助我们避免损失并提高策略的稳定性。

Python主要有两种错误类型：**语法错误**和**异常**。语法错误是指代码不符合Python的语法规则，导致解释器无法解析，就像写错了股票代码。异常则是指代码语法正确，但在运行时发生了错误，就像买入股票后市场出现了意料之外的情况。

![](img/66890e28f141fdb06f845d73e2703c95_1.png)

---

## 基础异常处理：try...except

上一节我们介绍了错误的基本概念，本节中我们来看看最基础的异常捕获结构 `try...except`。这是处理运行时错误最常用的方式。

以下是 `try...except` 的基本语法演示：

```python
try:
    # 尝试执行的代码
    result = 10 / 0
except ZeroDivisionError as e:
    # 当发生特定异常（此处为零除错误）时执行的代码
    print(f"捕获到异常：{e}")
```

在上面的代码中，我们尝试执行 `10 / 0`，这会引发一个 `ZeroDivisionError` 异常。`except` 块会捕获这个异常，并打印出错误信息，从而防止程序崩溃。

---

## 扩展处理：try...except...else...finally

除了基础的 `try...except`，Python还提供了更完整的结构来应对不同场景。

以下是 `try...except...else...finally` 的用法说明：

*   **`try`**: 包含可能引发异常的代码块。
*   **`except`**: 捕获并处理特定的异常。
*   **`else`**: 当 `try` 块中的代码**没有引发任何异常**时，会执行 `else` 块中的代码。这常用于放置那些依赖于 `try` 块成功执行的代码。
*   **`finally`**: **无论是否发生异常**，`finally` 块中的代码**都会被执行**。这通常用于执行清理操作，如关闭文件、释放网络连接等。

```python
def divide_numbers(a, b):
    try:
        result = a / b
    except ZeroDivisionError:
        print("除数不能为零")
        return -1  # 返回一个错误码
    else:
        print(f"计算成功，结果是：{result}")
        return 0   # 返回成功码
    finally:
        print("这段‘finally’代码始终会执行。")

# 测试函数
return_code = divide_numbers(10, 0)
print(f"函数返回值为：{return_code}")
print("---")
return_code = divide_numbers(10, 2)
print(f"函数返回值为：{return_code}")
```

运行上述代码，你可以观察到 `finally` 块在两种情况下都被执行了，而 `else` 块仅在未发生异常时执行。

---

## 主动抛出异常：raise

有时我们需要主动中断程序的正常流程并报告错误，这时可以使用 `raise` 语句。

以下是使用 `raise` 主动抛出异常的示例：

```python
def validate_positive_number(number):
    """验证数字是否为正数"""
    print(f"输入的数字是：{number}")
    if number <= 0:
        # 如果数字不大于0，主动抛出一个 ValueError 异常
        raise ValueError("输入的值必须大于零！")
    else:
        return number

# 测试
try:
    validate_positive_number(-5)
except ValueError as e:
    print(f"捕获到自定义错误：{e}")
```

通过 `raise`，我们可以创建清晰、明确的错误信息，帮助快速定位问题。

---

## 自定义异常

对于复杂的项目，内置的异常类型可能不够用。我们可以创建自己的异常类，使其继承自Python的 `Exception` 基类。

以下是创建自定义异常的步骤：

1.  定义一个新的类，继承自 `Exception`。
2.  通常可以在 `__init__` 方法中定义一些自定义的属性或信息。

```python
class InsufficientFundsError(Exception):
    """自定义异常：资金不足错误"""
    def __init__(self, balance, amount):
        self.balance = balance
        self.amount = amount
        message = f"操作失败。当前余额 {balance}，尝试支取 {amount}。"
        super().__init__(message) # 调用基类构造函数

# 模拟一个交易函数
def execute_trade(balance, trade_amount):
    if trade_amount > balance:
        raise InsufficientFundsError(balance, trade_amount)
    else:
        print(f"交易成功！支取 {trade_amount}。")
        return balance - trade_amount

# 使用自定义异常
try:
    execute_trade(10000, 15000)
except InsufficientFundsError as e:
    print(e) # 这里会打印出自定义的错误信息
```

![](img/66890e28f141fdb06f845d73e2703c95_3.png)

自定义异常能让你的代码逻辑更清晰，错误类型更具体，极大地提升了代码的可读性和可维护性。

---

![](img/66890e28f141fdb06f845d73e2703c95_5.png)

本节课中我们一起学习了Python错误与异常处理的核心知识。我们了解了语法错误与异常的区别，掌握了使用 `try...except` 捕获异常的基础方法，并深入学习了包含 `else` 和 `finally` 的完整异常处理结构。此外，我们还学会了如何使用 `raise` 主动抛出异常以及如何创建自定义异常类来满足特定需求。

![](img/66890e28f141fdb06f845d73e2703c95_7.png)

熟练掌握这些技巧，可以帮助我们编写出更健壮、更可靠的程序。在量化交易中，有效的异常处理对于风险控制和策略稳定性至关重要。希望本教程能帮助你更好地构建稳固的交易系统。