# 量化交易零基础：第二十二课：程序篇7 Python基础5 🐍

![](img/0b1b9c03450d545f7470a0130a12eab6_1.png)

在本节课中，我们将要学习Python编程中两个非常重要的概念：**异常处理**和**模块/包**。异常处理能让我们的程序在遇到错误时不会直接崩溃，而是优雅地处理问题并继续运行。模块和包则是Python强大功能的基石，它们允许我们复用代码，并轻松使用他人编写好的强大工具。

## 异常处理 🛡️

在编写程序时，难免会遇到各种错误，例如文件找不到、除数为零或变量未定义等。Python作为脚本语言，默认遇到错误就会停止运行。但我们希望程序能更健壮，即使遇到错误也能进行适当处理并继续执行。这就需要用到异常处理。

### 基本语法：try...except

异常处理的核心语法是 `try...except`。我们将可能出错的代码放在 `try` 代码块中，如果发生错误，程序会跳转到对应的 `except` 代码块执行错误处理逻辑。

```python
try:
    # 可能引发异常的代码
    result = 10 / 0
except:
    # 异常发生时的处理代码
    print("发生了一个错误！")
```

在上面的例子中，`10 / 0` 会引发一个“除零错误”。如果没有异常处理，程序会在此处崩溃。但使用了 `try...except` 后，错误被捕获，程序打印了提示信息并继续运行。

### 捕获特定异常

有时我们预知可能发生哪种错误，可以指定捕获特定的异常类型，进行更精确的处理。

```python
try:
    print(b)  # 变量b未定义，会引发NameError
except NameError:
    print("变量未定义错误已被捕获。")
```

这里，我们明确知道 `print(b)` 可能引发 `NameError`（命名错误），因此在 `except` 后指定了该异常类型。当发生 `NameError` 时，才会执行对应的处理代码。

![](img/0b1b9c03450d545f7470a0130a12eab6_3.png)

### 捕获多种异常

![](img/0b1b9c03450d545f7470a0130a12eab6_5.png)

一段代码可能引发多种不同类型的异常。我们可以使用多个 `except` 块来分别处理它们。

以下是处理多种可能异常的示例：
```python
try:
    # 可能引发多种异常的代码
    file = open('不存在的文件.txt')
    data = file.read()
except FileNotFoundError:
    print("文件未找到。")
except PermissionError:
    print("没有文件访问权限。")
except Exception as e:
    # Exception是所有异常类的基类，可以捕获所有未被前面except捕获的异常
    print(f"发生了未知错误：{e}")
```

### finally 子句

![](img/0b1b9c03450d545f7470a0130a12eab6_7.png)

无论是否发生异常，有些操作（如关闭文件、断开网络连接）都必须执行。这时可以使用 `finally` 子句。

```python
try:
    file = open('test.txt', 'w')
    file.write("Hello")
    # 这里假设发生了某种错误
    result = 10 / 0
except ZeroDivisionError:
    print("捕获到除零错误。")
finally:
    # 无论是否出错，都会执行关闭文件的操作
    file.close()
    print("文件已关闭。")
```

![](img/0b1b9c03450d545f7470a0130a12eab6_9.png)

![](img/0b1b9c03450d545f7470a0130a12eab6_11.png)

`finally` 块中的代码**必定会执行**，这保证了资源能被正确释放，是编写健壮程序的关键。

![](img/0b1b9c03450d545f7470a0130a12eab6_13.png)

上一节我们介绍了如何捕获和处理程序运行中的错误，让程序更加健壮。本节中我们来看看Python另一个强大的特性：模块和包。

## 模块与包 📦

Python的强大不仅在于其核心语法，更在于其庞大的**模块（Module）**和**包（Package）**生态系统。模块是一个包含Python代码的`.py`文件，而包则是包含多个模块的目录。通过导入它们，我们可以复用代码，避免重复造轮子。

### 使用内置模块

Python标准库自带了许多实用的内置模块。要使用它们，需要使用 `import` 语句。

例如，使用 `random` 模块生成随机数：
```python
import random
# 生成一个1到100之间的随机整数
random_number = random.randint(1, 100)
print(random_number)
```

我们也可以只从模块中导入需要的特定函数或类，语法是 `from ... import ...`。

![](img/0b1b9c03450d545f7470a0130a12eab6_15.png)

例如，从 `datetime` 模块导入 `datetime` 类来获取当前时间：
```python
from datetime import datetime
# 获取当前时间
now = datetime.now()
# 格式化输出
formatted_time = now.strftime("%Y-%m-%d %H:%M:%S")
print(f"当前时间是：{formatted_time}")
```

### 安装与使用第三方包

除了内置模块，Python社区贡献了海量的第三方包，涵盖了科学计算、网络爬虫、数据分析、人工智能等各个领域。这是Python如此流行的主要原因之一。

要使用第三方包，首先需要安装。Python使用 `pip` 这个包管理工具来安装和管理第三方包。

![](img/0b1b9c03450d545f7470a0130a12eab6_17.png)

以下是常用的pip命令：
*   **安装包**：`pip install 包名`
*   **卸载包**：`pip uninstall 包名`
*   **更新包**：`pip install --upgrade 包名`

![](img/0b1b9c03450d545f7470a0130a12eab6_19.png)

例如，安装著名的AI框架PyTorch：
```bash
pip install torch
```

安装成功后，就可以像使用内置模块一样导入和使用它了：
```python
import torch
# 检查是否有可用的GPU
device = torch.device('cuda' if torch.cuda.is_available() else 'cpu')
print(f"使用设备：{device}")
# 创建一个随机张量（多维数组）
x = torch.rand(2, 3, device=device)
print(x)
```

## 总结 📝

![](img/0b1b9c03450d545f7470a0130a12eab6_21.png)

![](img/0b1b9c03450d545f7470a0130a12eab6_23.png)

本节课中我们一起学习了Python的两个核心进阶概念。

首先，我们掌握了**异常处理**。通过 `try...except` 结构，我们可以预见并妥善处理程序运行中可能出现的错误，使用 `finally` 确保关键操作（如关闭文件）一定执行，从而编写出更加稳定、健壮的程序。

其次，我们深入了解了**模块和包**。这是Python生态强大的秘密武器。我们学会了如何使用 `import` 导入内置模块，以及如何使用 `pip` 安装和管理海量的第三方包，从而轻松利用他人编写好的强大功能，极大地提升了开发效率。

![](img/0b1b9c03450d545f7470a0130a12eab6_25.png)

掌握这两项技能，是编写实用、高效Python程序的关键一步。