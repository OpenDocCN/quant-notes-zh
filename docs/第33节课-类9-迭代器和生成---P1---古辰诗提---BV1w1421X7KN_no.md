# Python基础课：33：迭代器和生成器 🧑‍💻

![](img/459b92cdac72d5ce9f943ca1509b66a8_1.png)

在本节课中，我们将要学习Python中两个重要的概念：描述符、迭代器和生成器。上一节我们介绍了几个容易混淆的魔术方法，本节中我们来看看如何通过描述符来管理类属性，以及迭代器和生成器如何帮助我们更高效地处理数据序列。

![](img/459b92cdac72d5ce9f943ca1509b66a8_3.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_5.png)

## 描述符：管理你的属性 🔒

![](img/459b92cdac72d5ce9f943ca1509b66a8_7.png)

描述符是一种强大的工具，用于管理对类属性的访问。它允许你在获取、设置或删除属性时执行自定义的逻辑，常用于数据验证或创建只读属性。

![](img/459b92cdac72d5ce9f943ca1509b66a8_9.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_11.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_13.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_15.png)

一个描述符类需要实现 `__get__`、`__set__` 或 `__delete__` 方法中的一个或多个。

![](img/459b92cdac72d5ce9f943ca1509b66a8_17.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_19.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_21.png)

### 为什么需要描述符？

![](img/459b92cdac72d5ce9f943ca1509b66a8_23.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_24.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_26.png)

考虑一个场景：你有一个`Phone`类，它有`owner`和`price`属性。你希望这些属性不能被随意修改。

![](img/459b92cdac72d5ce9f943ca1509b66a8_28.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_29.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_30.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_32.png)

```python
class Phone:
    owner = '小张'
    price = 2000

![](img/459b92cdac72d5ce9f943ca1509b66a8_34.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_36.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_38.png)

p1 = Phone()
p1.price = 10000  # 这样可以直接修改实例的属性
print(p1.price)  # 输出: 10000
```

![](img/459b92cdac72d5ce9f943ca1509b66a8_40.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_42.png)

如你所见，实例属性可以被直接覆盖。为了控制这种行为，我们可以使用描述符。

![](img/459b92cdac72d5ce9f943ca1509b66a8_44.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_46.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_48.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_50.png)

### 实现一个描述符

![](img/459b92cdac72d5ce9f943ca1509b66a8_52.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_54.png)

以下是创建一个数据描述符的示例：

![](img/459b92cdac72d5ce9f943ca1509b66a8_56.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_58.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_60.png)

```python
class OwnerDescriptor:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __get__(self, instance, owner):
        # 获取属性时，返回格式化字符串
        return f'{self.name}{self.age}'

    def __set__(self, instance, value):
        # 设置属性时，只允许特定值
        if value != '老张':
            return  # 不做任何操作
        self.name = value

![](img/459b92cdac72d5ce9f943ca1509b66a8_62.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_64.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_66.png)

class Phone:
    owner = OwnerDescriptor('小张', 30)  # owner是一个描述符实例
    price = 2000

![](img/459b92cdac72d5ce9f943ca1509b66a8_68.png)

p1 = Phone()
print(p1.owner)  # 输出: 小张30

![](img/459b92cdac72d5ce9f943ca1509b66a8_70.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_71.png)

p1.owner = '小红'  # 赋值无效，因为描述符的__set__方法限制了修改
print(p1.owner)  # 输出: 小张30

![](img/459b92cdac72d5ce9f943ca1509b66a8_73.png)

p1.owner = '老张'  # 赋值成功
print(p1.owner)  # 输出: 老张30
```

![](img/459b92cdac72d5ce9f943ca1509b66a8_75.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_77.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_79.png)

**核心机制**：当访问`p1.owner`时，Python发现`owner`是类属性且是一个描述符对象（实现了`__get__`方法），于是会调用`OwnerDescriptor.__get__()`来获取值，而不是直接返回属性。赋值操作同理，会调用`__set__`方法。

![](img/459b92cdac72d5ce9f943ca1509b66a8_81.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_83.png)

**重要细节**：
*   描述符必须定义为**类属性**才有效。如果定义在`__init__`中成为实例属性，则不会触发描述符协议。
*   只实现`__get__`方法的描述符称为**非数据描述符**；同时实现`__get__`和`__set__`的称为**数据描述符**。数据描述符的优先级高于实例字典。
*   即使通过描述符控制，仍可直接修改类属性本身（如`Phone.owner = ...`），描述符主要用于管理通过**实例**进行的访问。

![](img/459b92cdac72d5ce9f943ca1509b66a8_85.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_87.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_89.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_91.png)

描述符的适用场景包括参数验证、属性类型检查、实现惰性求值或创建类似`@property`但可复用的属性管理器。

![](img/459b92cdac72d5ce9f943ca1509b66a8_92.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_94.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_96.png)

## 迭代器与可迭代对象 🔄

![](img/459b92cdac72d5ce9f943ca1509b66a8_98.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_100.png)

在深入生成器之前，必须理解迭代器和可迭代对象，因为生成器是特殊的迭代器。

![](img/459b92cdac72d5ce9f943ca1509b66a8_102.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_104.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_106.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_108.png)

### 基本概念

![](img/459b92cdac72d5ce9f943ca1509b66a8_110.png)

*   **可迭代对象 (Iterable)**：任何你可以用`for`循环遍历的对象，如列表、元组、字典、字符串。它的类中实现了`__iter__()`方法（或`__getitem__()`方法），该方法返回一个迭代器。
*   **迭代器 (Iterator)**：一个同时实现了`__iter__()`和`__next__()`方法的对象。`__next__()`方法负责返回序列中的下一个元素，并在没有更多元素时抛出`StopIteration`异常。

![](img/459b92cdac72d5ce9f943ca1509b66a8_112.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_114.png)

简单比喻：可迭代对象像一个装满水果的篮子（容器），迭代器像是那个从篮子里逐个拿水果给你的手（工具）。`for`循环内部就是先获取迭代器，然后不断调用`next()`。

![](img/459b92cdac72d5ce9f943ca1509b66a8_116.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_118.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_120.png)

### 如何判断？

![](img/459b92cdac72d5ce9f943ca1509b66a8_122.png)

可以使用`collections.abc`模块进行判断：

![](img/459b92cdac72d5ce9f943ca1509b66a8_124.png)

```python
from collections.abc import Iterator, Iterable

![](img/459b92cdac72d5ce9f943ca1509b66a8_126.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_128.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_130.png)

lst = [1, 2, 3]
print(isinstance(lst, Iterable))  # True，列表是可迭代对象
print(isinstance(lst, Iterator))  # False，列表本身不是迭代器

![](img/459b92cdac72d5ce9f943ca1509b66a8_132.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_134.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_136.png)

# 通过iter()函数可以获取可迭代对象的迭代器
lst_iterator = iter(lst)
print(isinstance(lst_iterator, Iterator))  # True
print(next(lst_iterator))  # 1
print(next(lst_iterator))  # 2
print(next(lst_iterator))  # 3
print(next(lst_iterator))  # 抛出 StopIteration 异常
```

![](img/459b92cdac72d5ce9f943ca1509b66a8_137.png)

### 自定义一个迭代器

![](img/459b92cdac72d5ce9f943ca1509b66a8_139.png)

让我们创建一个能迭代几个固定数字的迭代器：

![](img/459b92cdac72d5ce9f943ca1509b66a8_141.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_143.png)

```python
class NumberCounter:
    def __init__(self):
        self.numbers = [100, 1000, 10000]
        self.index = 0  # 当前迭代位置

    def __iter__(self):
        # 返回迭代器对象自身
        return self

    def __next__(self):
        if self.index < len(self.numbers):
            value = self.numbers[self.index]
            self.index += 1
            return value
        else:
            # 没有更多元素时，抛出异常
            raise StopIteration

# 使用
counter = NumberCounter()
print(isinstance(counter, Iterable))  # True
print(isinstance(counter, Iterator))  # True

for num in counter:
    print(num)  # 依次输出 100, 1000, 10000

![](img/459b92cdac72d5ce9f943ca1509b66a8_145.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_146.png)

# 或手动调用next
counter2 = NumberCounter()
print(next(counter2))  # 100
print(next(counter2))  # 1000
```

![](img/459b92cdac72d5ce9f943ca1509b66a8_148.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_150.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_152.png)

**关键点**：一个类要成为迭代器，必须同时实现`__iter__()`和`__next__()`。如果只实现`__iter__()`，它只是一个可迭代对象，不是迭代器。

![](img/459b92cdac72d5ce9f943ca1509b66a8_154.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_155.png)

## 生成器：优雅的迭代器 ✨

![](img/459b92cdac72d5ce9f943ca1509b66a8_157.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_159.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_161.png)

生成器提供了一种更简洁、更Pythonic的方式来创建迭代器。你不需要像上面那样显式定义`__iter__()`和`__next__()`方法，只需使用`yield`关键字。

![](img/459b92cdac72d5ce9f943ca1509b66a8_163.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_165.png)

### 创建生成器函数

![](img/459b92cdac72d5ce9f943ca1509b66a8_167.png)

包含`yield`语句的函数称为生成器函数。调用它时，会返回一个生成器对象，而不会立即执行函数体。

![](img/459b92cdac72d5ce9f943ca1509b66a8_169.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_171.png)

```python
def count_down(num):
    print(f"开始计数，初始值: {num}")
    while num > 0:
        yield num  # 每次执行到这里暂停，返回num的值
        num -= 1
    print("计数结束")

![](img/459b92cdac72d5ce9f943ca1509b66a8_173.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_175.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_177.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_179.png)

# 创建生成器
gen = count_down(5)
print(gen)  # 输出: <generator object count_down at ...>，这是一个生成器对象

![](img/459b92cdac72d5ce9f943ca1509b66a8_181.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_182.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_184.png)

# 使用next()调用
print(next(gen))  # 输出: “开始计数，初始值: 5” 然后输出: 5
print(next(gen))  # 输出: 4
print(next(gen))  # 输出: 3
```

![](img/459b92cdac72d5ce9f943ca1509b66a8_186.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_188.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_190.png)

**`yield`的工作方式**：当函数执行到`yield`时，会暂停并将`yield`后的值返回给调用者。下次调用`next()`时，函数从上次暂停的位置继续执行，直到遇到下一个`yield`或函数结束。

![](img/459b92cdac72d5ce9f943ca1509b66a8_192.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_194.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_195.png)

### 生成器的优势

![](img/459b92cdac72d5ce9f943ca1509b66a8_197.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_199.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_201.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_203.png)

1.  **惰性求值**：数据在需要时才生成，非常适合处理大量数据或无限序列，节省内存。
2.  **代码简洁**：无需手动管理迭代状态（如`index`变量）。
3.  **状态保持**：函数局部变量和执行状态在每次`yield`后都会被自动保存。

![](img/459b92cdac72d5ce9f943ca1509b66a8_205.png)

### 生成器的方法

![](img/459b92cdac72d5ce9f943ca1509b66a8_207.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_209.png)

生成器对象除了`__next__()`，还有几个有用的方法：

![](img/459b92cdac72d5ce9f943ca1509b66a8_211.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_213.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_215.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_217.png)

*   **`.send(value)`**：向生成器内部发送一个值，该值会成为当前`yield`表达式的结果，并继续执行到下一个`yield`。

    ```python
    def adjustable_counter(num):
        while num > 0:
            received = yield num  # yield可以接收外部发送的值
            if received is not None:
                num = received  # 如果接收到新值，则更新num
            num -= 1

    gen = adjustable_counter(10)
    print(next(gen))  # 10
    print(next(gen))  # 9
    print(gen.send(15))  # 向生成器发送15，输出: 14 (因为15-1)
    ```

*   **`.close()`**：终止生成器。

    ```python
    gen = count_down(5)
    print(next(gen))  # 5
    gen.close()
    print(next(gen))  # 抛出 StopIteration 异常
    ```

![](img/459b92cdac72d5ce9f943ca1509b66a8_219.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_221.png)

*   **`.throw(type[, value[, traceback]])`**：在生成器暂停的`yield`处抛出一个指定的异常。

![](img/459b92cdac72d5ce9f943ca1509b66a8_223.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_225.png)

### 生成器表达式

类似于列表推导式，但使用圆括号，它返回一个生成器对象。

```python
# 列表推导式：立即生成所有数据
squares_list = [x**2 for x in range(5)]  # [0, 1, 4, 9, 16]

![](img/459b92cdac72d5ce9f943ca1509b66a8_227.png)

# 生成器表达式：惰性生成数据
squares_gen = (x**2 for x in range(5))
print(next(squares_gen))  # 0
print(list(squares_gen))   # 将剩余元素转化为列表: [1, 4, 9, 16]
```

![](img/459b92cdac72d5ce9f943ca1509b66a8_229.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_231.png)

## 总结 📚

![](img/459b92cdac72d5ce9f943ca1509b66a8_233.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_235.png)

本节课中我们一起学习了Python中三个紧密相关的核心概念：

![](img/459b92cdac72d5ce9f943ca1509b66a8_237.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_239.png)

1.  **描述符**：通过实现`__get__`、`__set__`、`__delete__`方法，可以精细控制类属性的访问、赋值和删除过程，常用于数据验证和创建托管属性。
2.  **迭代器与可迭代对象**：
    *   **可迭代对象**是实现了`__iter__()`方法的容器。
    *   **迭代器**是同时实现了`__iter__()`和`__next__()`方法的对象，负责逐个产出元素。
    *   `for`循环的本质就是先获取可迭代对象的迭代器，然后循环调用`next()`。
3.  **生成器**：一种特殊的迭代器，通过`yield`关键字定义，使得创建迭代器变得异常简单和高效。它支持惰性求值，并能通过`.send()`、`.close()`等方法进行交互和控制。

![](img/459b92cdac72d5ce9f943ca1509b66a8_241.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_243.png)

![](img/459b92cdac72d5ce9f943ca1509b66a8_245.png)

理解这些概念对于编写高效、优雅且符合Python风格的代码至关重要。虽然在一些简单的量化任务中可能不会直接用到生成器，但在处理数据流、文件读取或构建复杂的数据管道时，它们是不可或缺的工具。