# Python金融量化：P4：Python基础知识（二） 🔄

![](img/b7fe8acade8640323f4fe3d7356248fc_1.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_3.png)

在本节课中，我们将要学习Python编程中至关重要的“控制流”概念。控制流决定了程序执行的顺序，主要包括条件语句、循环语句以及函数的声明与调用。掌握这些知识是编写自动化、智能化金融分析程序的基础。

## 条件语句：if...elif...else

上一节我们介绍了Python的基本数据类型和操作。本节中我们来看看如何让程序根据不同的条件做出决策，这正是条件语句的作用。

![](img/b7fe8acade8640323f4fe3d7356248fc_5.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_6.png)

Python的条件语句与Excel中的`IF`函数逻辑相似。其基本结构如下：
```python
if 条件1:
    # 如果条件1为真，执行这里的代码
elif 条件2:
    # 如果条件1为假但条件2为真，执行这里的代码
else:
    # 如果以上条件都为假，执行这里的代码
```
以下是使用条件语句时需要注意的关键点：
*   **缩进**：`if`、`elif`、`else`后面的代码块必须缩进（通常用4个空格或一个Tab键）。缩进错误会导致语法错误。
*   **冒号**：每个条件语句（`if`、`elif`、`else`）的末尾都必须有一个英文冒号`:`。

![](img/b7fe8acade8640323f4fe3d7356248fc_8.png)

让我们通过一个判断年龄段的例子来实践：
```python
age = float(input(“请输入你的年龄：”))
if age < 15:
    print(“你是一个孩子。”)
elif age < 25:
    print(“你是一个青年。”)
elif age < 40:
    print(“你是一个中年人。”)
else:
    print(“你是一位老年人。”)
```
在这段代码中，`input()`函数用于获取用户输入，`float()`函数将输入的字符串转换为浮点数以便比较。程序会根据输入的年龄值，打印出对应的描述。

![](img/b7fe8acade8640323f4fe3d7356248fc_10.png)

## 循环语句：for 与 while

条件语句让程序有了分支选择的能力，而循环语句则能让程序重复执行特定的任务，极大地提高了效率。Python中有两种主要的循环：`for`循环和`while`循环。

![](img/b7fe8acade8640323f4fe3d7356248fc_12.png)

### for 循环

![](img/b7fe8acade8640323f4fe3d7356248fc_14.png)

`for`循环通常用于遍历一个序列（如列表、元组、字符串或字典）中的每个元素，或者执行指定次数的重复操作。它是一种**有限的循环**。

以下是`for`循环的基本语法和应用示例：
```python
# 遍历列表
fruits = [“苹果”, “香蕉”, “橙子”]
for fruit in fruits:
    print(fruit)

# 结合 range() 函数执行固定次数循环
for i in range(5): # 循环5次，i的值从0到4
    print(f”这是第 {i+1} 次循环”)

# 遍历字典的键值对
stock_prices = {“贵州茅台”: 840, “中国平安”: 78, “中信证券”: 26}
for stock, price in stock_prices.items():
    print(f”{stock} 的价格是 {price} 元”)
```
`range(n)`函数生成一个从0到n-1的整数序列，常用于控制循环次数。

### while 循环

`while`循环会在条件为真时，持续执行代码块。它是一种**条件循环**，如果条件永远为真，则会形成无限循环，因此使用时需谨慎。

以下是`while`循环的基本语法和一个交互式求和的例子：
```python
# 基本语法
while 条件:
    # 条件为真时执行的代码

# 示例：用户输入数字，输入 ‘done‘ 时结束并计算总和
numbers = []
total = 0
while True: # 条件恒为真，构成一个“无限循环”框架
    user_input = input(“请输入一个数字（输入‘done’结束）：”)
    if user_input.lower() == ‘done’:
        break # break语句用于立即退出当前循环
    try:
        num = float(user_input)
        numbers.append(num)
        total += num
    except ValueError:
        print(“请输入有效的数字！”)

![](img/b7fe8acade8640323f4fe3d7356248fc_16.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_17.png)

print(f“您输入的数字列表是：{numbers}”)
print(f“这些数字的总和是：{total}”)
print(f“这些数字的平均值是：{total / len(numbers)}”)
```
在这个例子中，我们使用`while True`构建了一个循环框架，通过内部的`if`判断和`break`语句来控制循环的终止。

## 函数的声明与调用

当一段代码需要被重复使用时，将其封装成函数是最佳实践。函数可以接收输入（参数），进行处理，并返回结果。

### 如何声明和调用函数

![](img/b7fe8acade8640323f4fe3d7356248fc_19.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_21.png)

函数使用`def`关键字进行声明。以下是定义和调用一个简单函数的步骤：
```python
# 1. 声明一个函数
def greet(name): # ‘name‘ 是形式参数
    “““这是一个问候函数”””
    return f“Hello, {name}!”

![](img/b7fe8acade8640323f4fe3d7356248fc_23.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_24.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_25.png)

# 2. 调用函数
message = greet(“雷锋”) # ‘雷锋‘ 是实际参数
print(message) # 输出：Hello, 雷锋!
```
函数可以没有参数，也可以有多个参数。使用`return`语句可以指定函数的返回值，如果没有`return`，函数默认返回`None`。

![](img/b7fe8acade8640323f4fe3d7356248fc_27.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_29.png)

### 将函数封装为模块

![](img/b7fe8acade8640323f4fe3d7356248fc_31.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_32.png)

我们可以将常用的函数保存在单独的`.py`文件中，形成一个模块，方便在其他程序中导入使用。
1.  创建一个新文件，例如 `my_functions.py`。
2.  在文件中定义你的函数。
    ```python
    # my_functions.py
    def calculate_average(number_list):
        “““计算列表的平均值”””
        return sum(number_list) / len(number_list)
    ```
3.  在另一个Python脚本或Jupyter Notebook中导入并使用它。
    ```python
    # 在主程序中
    import my_functions as mf # 导入模块并起别名
    data = [10, 20, 30, 40]
    avg = mf.calculate_average(data) # 调用模块中的函数
    print(avg) # 输出：25.0
    ```

![](img/b7fe8acade8640323f4fe3d7356248fc_34.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_35.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_37.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_38.png)

### 导入模块的常用方式

![](img/b7fe8acade8640323f4fe3d7356248fc_40.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_42.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_44.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_45.png)

在Python中，导入外部模块有几种常见方式：
*   `import pandas`：导入整个pandas模块，调用时需写全名，如 `pandas.DataFrame()`。
*   `import pandas as pd`：**最推荐的方式**。导入模块并为其设置一个简短的别名`pd`，调用时使用`pd.DataFrame()`，书写简洁。
*   `from pandas import DataFrame`：仅从pandas模块中导入`DataFrame`这一个类，调用时可直接写`DataFrame()`。但不利于代码可读性。
*   `from pandas import *`：导入模块中的所有内容，不推荐使用，可能导致命名冲突。

## Python内置函数简介

Python自带了许多内置函数，无需导入任何模块即可直接使用。这些函数能完成基础而重要的操作。
例如：
*   `len()`：获取对象长度。
*   `type()`：返回对象的类型。
*   `int()`, `float()`, `str()`：进行类型转换。
*   `sum()`, `max()`, `min()`：对可迭代对象进行数学计算。
*   `input()`：获取用户输入。
*   `print()`：输出信息。
*   `help()`：获取函数或模块的帮助文档。例如，`help(print)`会显示`print`函数的详细用法。

要查看所有内置函数，可以查阅[Python官方文档](https://docs.python.org/3/library/functions.html)。

![](img/b7fe8acade8640323f4fe3d7356248fc_47.png)

## 总结

![](img/b7fe8acade8640323f4fe3d7356248fc_49.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_50.png)

![](img/b7fe8acade8640323f4fe3d7356248fc_52.png)

本节课中我们一起学习了Python控制流的核心知识。
我们首先掌握了**条件语句（if/elif/else）**，它让程序能够根据不同条件执行不同的代码分支。
接着，我们深入探讨了两种**循环语句**：**`for`循环**常用于遍历序列或执行指定次数的任务，而**`while`循环**则在条件满足时持续运行，使用时需注意避免无限循环。
然后，我们学习了如何通过`def`关键字**声明和调用函数**，将代码块封装成可重复使用的工具，并了解了如何将函数保存为**模块**以及如何**导入模块**。
最后，我们简要介绍了Python丰富的**内置函数**，它们是编程中的得力助手。
请务必结合课程讲义中的示例代码进行练习，例如尝试编写“猜数字”游戏或完善交互式统计程序，实践是巩固这些概念的最佳途径。