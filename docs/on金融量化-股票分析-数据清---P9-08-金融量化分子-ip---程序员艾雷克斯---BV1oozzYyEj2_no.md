# Python金融量化投资分析与股票交易：P9：08 金融量化分子-IPython魔术命令 🪄

![](img/043cc7bdf351070b8ab9dca6636ad5ea_1.png)

在本节课中，我们将要学习IPython环境中的“魔术命令”。这些命令以百分号开头，提供了许多便捷的高级功能，能极大地提升我们在交互式环境中编写和测试代码的效率。

上一节我们介绍了IPython的基本使用，本节中我们来看看这些强大的魔术命令。

![](img/043cc7bdf351070b8ab9dca6636ad5ea_3.png)

## 运行外部Python脚本

![](img/043cc7bdf351070b8ab9dca6636ad5ea_5.png)

在标准的Python命令行中，若想运行一个Python文件，通常需要退出命令行，再执行`python 文件名.py`。在IPython中，我们无需退出。

以下是使用`%run`命令的方法：
*   `%run hello.py`：此命令可以直接在IPython交互器中运行名为`hello.py`的脚本文件。

## 执行剪贴板中的代码

![](img/043cc7bdf351070b8ab9dca6636ad5ea_7.png)

![](img/043cc7bdf351070b8ab9dca6636ad5ea_9.png)

有时我们可能从编辑器或其他地方复制了一段代码，希望直接在IPython中执行。

![](img/043cc7bdf351070b8ab9dca6636ad5ea_11.png)

![](img/043cc7bdf351070b8ab9dca6636ad5ea_13.png)

以下是使用`%paste`命令的方法：
*   `%paste`：此命令会读取系统剪贴板中的内容，将其作为代码在IPython中执行。它会先打印出代码内容，然后执行。

## 测量代码执行时间

在性能优化时，我们经常需要精确测量某段代码或函数的运行时间。虽然可以使用Python的`time`模块，但对于执行时间极短的代码，测量可能不够精确。

IPython提供了`%timeit`魔术命令来解决这个问题。它会自动多次运行代码，并计算平均执行时间，结果更为可靠。

其基本语法为：
```python
%timeit 要测试的代码
```

例如，测试对一个包含1万个随机数的列表进行排序所需的时间：
```python
import random
lst = [random.random() for _ in range(10000)]
%timeit sorted(lst)
```
`%timeit`会输出类似`146 µs ± 742 ns per loop`的结果，表示平均每次循环耗时146微秒，并给出了误差范围。它通过运行数百万次来获得一个稳定的平均值，这对于评估微小操作的性能非常有帮助。

![](img/043cc7bdf351070b8ab9dca6636ad5ea_15.png)

本节课中我们一起学习了IPython的三个核心魔术命令：`%run`用于运行外部脚本，`%paste`用于执行剪贴板代码，`%timeit`用于精确测量代码执行时间。掌握这些命令能让你的数据分析与量化研究流程更加高效流畅。