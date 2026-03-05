# AI云量化：第1关：Python语法基础 🐍

在本节课中，我们将要学习Python编程语言的基础语法，这是编写量化策略代码的第一步。我们将从最基础的变量、数据类型开始，逐步深入到条件判断、循环等核心概念，为后续的量化策略开发打下坚实的基础。

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_0.png)

## 变量与数据类型 📦

在Python中，变量用于存储数据。你可以把变量想象成一个贴有标签的盒子，标签是变量名，盒子里装的是数据。Python中的基本数据类型包括整数、浮点数、字符串和布尔值。

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_1.png)

以下是几种基本数据类型的定义示例：

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_3.png)

```python
# 整数
age = 25

# 浮点数（小数）
price = 19.99

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_4.png)

# 字符串
name = "量化交易"

# 布尔值
is_active = True
```

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_5.png)

## 条件判断 🔀

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_7.png)

程序常常需要根据不同的情况执行不同的操作，这时就需要用到条件判断。在Python中，我们使用 `if`、`elif` 和 `else` 语句来实现。

上一节我们介绍了如何存储数据，本节中我们来看看如何根据数据做出决策。

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_8.png)

以下是一个简单的条件判断示例：

```python
score = 85

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_9.png)

if score >= 90:
    print("优秀")
elif score >= 60:
    print("及格")
else:
    print("不及格")
```

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_10.png)

## 循环结构 🔁

当我们需要重复执行某段代码时，循环结构就派上用场了。Python提供了两种主要的循环：`for` 循环和 `while` 循环。

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_11.png)

理解了条件判断后，让我们学习如何让计算机自动重复执行任务。

以下是两种循环的示例：

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_12.png)

```python
# for循环：遍历一个序列
for i in range(5):  # range(5) 生成 0,1,2,3,4
    print(f"这是第 {i+1} 次循环")

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_13.png)

# while循环：在条件为真时持续执行
count = 0
while count < 3:
    print(f"count 的值为：{count}")
    count = count + 1  # 这行代码也可以写成 count += 1
```

## 函数定义与调用 🛠️

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_15.png)

函数是一段可重复使用的代码块，它接受输入（参数），执行特定任务，并可能返回一个结果。使用函数可以让代码更清晰、更易于维护。

学会了基础的控制流程后，我们将学习如何将代码组织成可复用的模块。

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_16.png)

以下是如何定义和调用一个简单的函数：

```python
# 定义一个计算平方的函数
def calculate_square(number):
    result = number * number  # 或者使用 number ** 2
    return result

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_17.png)

# 调用函数
my_number = 4
squared_value = calculate_square(my_number)
print(f"{my_number} 的平方是 {squared_value}")
```

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_18.png)

## 列表与字典 📋

在量化分析中，我们经常需要处理一组数据。Python的列表和字典是两种非常重要的数据结构，用于存储和操作数据集合。

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_19.png)

为了处理更复杂的数据，我们需要掌握两种强大的容器：列表和字典。

以下是列表和字典的基本用法：

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_20.png)

```python
# 列表：有序的元素集合
stock_list = ['AAPL', 'GOOGL', 'TSLA']
print(f"第一支股票是：{stock_list[0]}")  # 索引从0开始

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_21.png)

# 字典：键值对集合
stock_info = {
    'name': 'Apple Inc.',
    'symbol': 'AAPL',
    'price': 182.63
}
print(f"{stock_info['symbol']} 的价格是 {stock_info['price']}")
```

## 总结 📝

![](img/7a72a9bcd0716cd2c9749cd5bc3d9372_22.png)

本节课中我们一起学习了Python编程的基础语法。我们从变量和数据类型开始，掌握了如何使用条件判断来控制程序流程，以及如何利用循环来重复执行代码。接着，我们学习了如何定义和调用函数来组织代码。最后，我们介绍了列表和字典这两种用于存储数据集合的核心数据结构。

这些知识是编写任何Python程序，包括量化交易策略的基石。请务必通过练习来巩固这些概念。在下一课中，我们将学习如何将这些基础语法应用到具体的量化分析场景中。