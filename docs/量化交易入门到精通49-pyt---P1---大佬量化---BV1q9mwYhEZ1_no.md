# 量化交易入门到精通：49：Python Random模块 🎲

在本节课中，我们将要学习Python标准库中的`random`模块。这是一个用于生成随机数的核心工具。在量化交易领域，随机数的应用非常广泛，例如模拟交易、生成随机策略参数等。掌握`random`模块将帮助我们更好地进行数据模拟和策略开发。

---

## 生成随机数

上一节我们介绍了`random`模块的基本概念，本节中我们来看看如何生成基本的随机数。

`random`模块最基础的功能是生成一个0到1之间的随机浮点数。

```python
import random

# 生成一个0到1之间的随机浮点数
random_float = random.random()
print(random_float)
```

运行上述代码，每次都会得到一个不同的浮点数，例如`0.374448`。

---

## 生成随机整数

除了浮点数，我们经常需要生成指定范围内的随机整数。`random.randint(a, b)`函数可以生成一个包含`a`和`b`在内的随机整数。

以下是生成1到10之间随机整数的示例：

```python
import random

# 生成一个1到10之间的随机整数
random_int = random.randint(1, 10)
print(random_int)
print(type(random_int))
```

---

## 生成指定范围的随机浮点数

如果需要生成指定区间（而不仅仅是0到1）的随机浮点数，可以使用`random.uniform(a, b)`函数。

```python
import random

# 生成一个1.0到10.0之间的随机浮点数
random_float_range = random.uniform(1.0, 10.0)
print(random_float_range)
```

这个功能在模拟虚拟行情数据时非常有用。

---

## 从序列中随机选择元素

在策略测试中，我们可能需要从一组股票中随机挑选。`random.choice(seq)`函数可以从一个非空序列中随机返回一个元素。

以下是其使用方法：

```python
import random

my_list = ['工商银行', '建设银行', '贵州茅台', '比亚迪', '宁德时代']

# 从列表中随机选择一个元素
selected_stock = random.choice(my_list)
print(selected_stock)
```

---

## 带权重的随机选择

有时我们希望某些元素被选中的概率更高。`random.choices(seq, weights)`函数允许我们为序列中的每个元素设置权重。

以下是使用权重进行随机选择的示例：

```python
import random

my_list = ['工商银行', '建设银行', '贵州茅台', '比亚迪', '宁德时代']
# 设置权重，权重越高，被选中的概率越大
weights = [1, 1, 2, 3, 5]

# 根据权重从列表中随机选择一个元素
selected_stock = random.choices(my_list, weights=weights, k=1)[0]
print(selected_stock)
```

在这个例子中，“宁德时代”的权重最高，因此它被选中的概率也最大。

---

## 随机打乱序列顺序

`random.shuffle(seq)`函数可以将一个可变序列（如列表）中的元素顺序随机打乱。这在数据预处理或模拟随机事件顺序时很有用。

以下是其演示：

```python
import random

my_list = ['工商银行', '建设银行', '贵州茅台', '比亚迪', '宁德时代']
print("原始列表:", my_list)

# 随机打乱列表顺序
random.shuffle(my_list)
print("打乱后的列表:", my_list)
```

每次运行，列表的顺序都会发生变化。

---

## 设置随机种子

随机数生成器本质上是“伪随机”的，其序列由一个初始值（种子）决定。设置相同的随机种子可以保证每次运行程序时生成相同的随机数序列，这在需要复现实验结果时至关重要。

使用`random.seed(a)`函数来设置随机种子。

```python
import random

# 设置随机种子为1
random.seed(1)
print("种子为1时的随机数:", random.random())

# 再次设置相同的种子
random.seed(1)
print("再次设置种子为1时的随机数:", random.random())

# 设置不同的种子
random.seed(2)
print("种子为2时的随机数:", random.random())
```

你会发现，当种子相同时，生成的随机数也相同。

![](img/7c4e9b8eb64c6bd92c3a287c880255d0_1.png)

---

![](img/7c4e9b8eb64c6bd92c3a287c880255d0_3.png)

本节课中我们一起学习了Python `random`模块的核心功能。我们掌握了如何生成随机整数和浮点数、如何从序列中随机选择元素（包括带权重的选择）、如何打乱序列顺序，以及如何通过设置随机种子来确保结果的可复现性。这些技能在量化交易的模拟测试、参数优化和数据分析中都非常实用。