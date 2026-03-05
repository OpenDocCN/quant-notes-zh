# 量化交易零基础：18：Python基础入门

![](img/8e8f6282c5e169cbcac7c69a6b82ab93_1.png)

在本节课中，我们将学习Python编程语言的基础知识。Python是量化交易中常用的脚本语言，因其语法简洁、上手快而广受欢迎。我们将从环境搭建开始，逐步介绍变量、数据类型和基本运算，为后续的量化策略编写打下基础。

## 环境搭建与验证

上一节我们概述了Python在量化交易中的应用，本节中我们来看看如何搭建Python开发环境。

![](img/8e8f6282c5e169cbcac7c69a6b82ab93_3.png)

首先，需要从Python官方网站下载并安装Python。安装过程通常只需按照向导点击“下一步”即可完成。

![](img/8e8f6282c5e169cbcac7c69a6b82ab93_5.png)

安装完成后，需要验证环境是否配置成功。在Windows系统中，可以按下 `Win + R` 键，输入 `cmd` 打开命令提示符，然后输入 `python` 并回车。如果安装成功，将显示Python版本信息并进入交互模式。

在交互模式中，可以输入简单的代码进行测试，例如：
```python
print("hello")
```
如果成功输出“hello”，则说明Python环境已准备就绪。输入 `exit()` 可以退出交互模式。

![](img/8e8f6282c5e169cbcac7c69a6b82ab93_7.png)

## 开发工具选择

验证环境后，我们需要一个代码编辑器或集成开发环境来编写和运行程序。以下是几种常见的选择：

*   **VS Code**：一款免费的轻量级代码编辑器，通过安装Python插件即可获得强大的开发支持。
*   **PyCharm**：一款功能丰富的集成开发环境，其社区版免费，适合Python开发。
*   **Jupyter Notebook**：一个基于网页的交互式计算环境，非常适合教学、数据分析和做笔记。它允许我们将代码、运行结果和说明文字整合在一个文档中。

本教程将使用Jupyter Notebook进行演示，以便更直观地展示代码和结果。

## Python语言简介

Python是一种解释型的脚本语言，诞生于上世纪90年代。与C++、Java等需要编译的强类型语言不同，Python属于弱类型语言，语法灵活，适合快速开发小型工具或自动化脚本。

它的核心优势在于简单易学、开发效率高。虽然运行速度可能不及编译型语言，但在数据分析、人工智能、网络爬虫以及我们关注的量化交易等领域，Python都有极佳的支持和丰富的库。

## 基础运算与变量

现在，让我们开始学习Python的基本语法。首先从最基础的四则运算开始。

在Python中，可以直接输入算式并得到结果，就像一个高级计算器。例如，输入 `1 + 1` 会直接返回结果 `2`。

为了存储和复用数据，我们需要使用**变量**。变量是内存中存储数据的一个命名空间。

在Python中定义变量的语法非常简单：
```python
变量名 = 值
```
这里的等号 `=` 是**赋值**操作符，意思是将右边的“值”存入左边名为“变量名”的内存空间中。

变量名可以包含字母、数字和下划线，但不能以数字开头。

以下是变量与四则运算的例子：
```python
a = 1          # 将整数1赋值给变量a
b = 2.0        # 将浮点数2.0赋值给变量b
c = a % 2      # 计算a除以2的余数，并赋值给c。%是取余运算符。
print(c)       # 输出c的值
```
代码中 `#` 后面的内容是**注释**，用于解释代码，程序不会执行。

## 基本数据类型

Python可以自动推断数据的类型。了解不同类型有助于我们正确操作数据。以下是几种基本数据类型：

![](img/8e8f6282c5e169cbcac7c69a6b82ab93_9.png)

![](img/8e8f6282c5e169cbcac7c69a6b82ab93_11.png)

**1. 整数与浮点数**
整数（int）是不带小数点的数字，浮点数（float）是带小数点的数字。
可以使用 `type()` 函数查看变量的类型。
```python
a = 1
b = 2.0
print(type(a)) # 输出：<class 'int'>
print(type(b)) # 输出：<class 'float'>
```

**2. 布尔类型**
布尔型（bool）只有两个值：`True`（真）和 `False`（假），常用于逻辑判断。
```python
aa = True
print(aa)            # 输出：True
print(type(aa))      # 输出：<class 'bool'>
```

**3. 字符串**
字符串（str）用于表示文本，用单引号 `‘` 或双引号 `“` 括起来。
```python
bb = “哈哈哈”
print(type(bb))      # 输出：<class 'str'>
```
字符串可以使用加号 `+` 进行拼接。
```python
cc = “呵呵”
dd = bb + cc
print(dd)            # 输出：哈哈哈呵呵
```
注意：字符串不能直接与数字或布尔值拼接，需要先转换类型。
```python
# 错误示例：print(‘字符串’ + aa + str(c))
# 正确做法是使用str()函数转换
print(‘字符串’ + str(aa) + ‘ ‘ + str(c))
```
更简洁的方法是使用**格式化字符串**（f-string）：
```python
print(f’字符串{aa} {c}‘)
```

**4. 列表、元组与集合**
这些是用于存储一系列数据的数据结构。
*   **列表（list）**：使用方括号 `[]` 定义，元素有序、可重复、可修改。
    ```python
    students = [‘学生A‘， ‘学生B‘， ‘学生C‘， ‘学生C’]
    print(type(students)) # <class ‘list’>
    ```
*   **元组（tuple）**：使用圆括号 `()` 定义，元素有序、可重复、但**不可修改**。
    ```python
    students = (‘学生A‘， ‘学生B‘， ‘学生C‘， ‘学生C’)
    print(type(students)) # <class ‘tuple’>
    ```
*   **集合（set）**：使用花括号 `{}` 定义，元素**无序、不可重复**。
    ```python
    students = {‘学生A‘， ‘学生B‘， ‘学生C‘， ‘学生C’} # 重复的’学生C‘会被自动去重
    print(type(students)) # <class ‘set’>
    ```

**5. 字典**
字典（dict）用于存储**键值对**数据，使用花括号 `{}` 定义，格式为 `{键1： 值1， 键2： 值2， …}`。
```python
student1 = {‘name‘： ‘学生A‘， ‘age‘： 10}
print(student1)            # 输出：{‘name‘： ‘学生A‘， ‘age‘： 10}
print(type(student1))      # 输出：<class ‘dict’>
```

## 逻辑运算

逻辑运算用于比较和判断，结果是布尔值（True或False）。

**1. 比较运算符**
用于比较两个值的大小或相等关系。
```python
a = 3
b = 5
c = a > b  # 判断a是否大于b，结果False赋值给c
print(c)   # 输出：False
c = a != b # 判断a是否不等于b，结果True赋值给c
print(c)   # 输出：True
```
主要比较运算符包括：`>`（大于）， `<`（小于）， `==`（等于）， `>=`（大于等于）， `<=`（小于等于）， `!=`（不等于）。
**注意**：赋值是一个等号 `=`，判断相等是两个等号 `==`。

**2. 逻辑运算符**
用于组合多个逻辑条件，包括 `and`（与）， `or`（或）， `not`（非）。
*   `and`：两边条件都为True，结果才为True。
*   `or`：两边条件有一个为True，结果即为True。
*   `not`：对条件取反，True变False，False变True。
```python
a = 3
b = 5
condition1 = (a != b)  # True
condition2 = (a > b)   # False

![](img/8e8f6282c5e169cbcac7c69a6b82ab93_13.png)

c = condition1 and condition2 # True and False => False
print(c) # 输出：False

c = condition1 or condition2  # True or False => True
print(c) # 输出：True

c = not (condition1 or condition2) # not True => False
print(c) # 输出：False
```

## 总结

本节课中我们一起学习了Python编程的基础入门知识。我们首先完成了Python环境的安装与验证，并介绍了Jupyter Notebook这一实用的开发工具。接着，我们深入探讨了变量的概念与命名规则，并逐一讲解了整数、浮点数、布尔值、字符串、列表、元组、集合和字典等基本数据类型及其用法。最后，我们学习了用于比较和条件组合的比较运算符与逻辑运算符。

![](img/8e8f6282c5e169cbcac7c69a6b82ab93_15.png)

掌握这些基础知识是编写任何Python程序的第一步，也是后续学习量化交易策略开发的基石。下节课，我们将在此基础上，学习程序的控制流程，例如条件判断和循环。