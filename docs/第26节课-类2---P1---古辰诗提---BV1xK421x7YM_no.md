# Python基础课：26：类（二）🚀

![](img/ce6df8820230e84c9675a0aa94d517b1_1.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_3.png)

在本节课中，我们将继续深入学习Python中的类。我们将重点介绍类方法、静态方法，并通过一个模拟量化交易的实战案例，将之前学过的构造函数、实例方法、实例属性等知识融会贯通，帮助你理解如何在实际项目中组织和使用类。

---

![](img/ce6df8820230e84c9675a0aa94d517b1_5.png)

## 回顾与引言

![](img/ce6df8820230e84c9675a0aa94d517b1_7.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_8.png)

上一节课我们介绍了类的基本概念，包括实例方法、实例属性、类属性以及构造函数 `__init__`。构造函数在类被实例化时自动执行，用于初始化对象。`self` 参数代表实例本身，通过 `self.属性名` 定义的属性是实例属性。

![](img/ce6df8820230e84c9675a0aa94d517b1_10.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_12.png)

本节中，我们将看看类方法和静态方法，并构建一个完整的案例来巩固理解。

---

![](img/ce6df8820230e84c9675a0aa94d517b1_14.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_16.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_18.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_20.png)

## 类方法

![](img/ce6df8820230e84c9675a0aa94d517b1_22.png)

类方法是属于类本身的方法，而不是类的某个特定实例。定义类方法时，需要使用 `@classmethod` 装饰器，并且方法的第一个参数通常命名为 `cls`（代表类本身）。

![](img/ce6df8820230e84c9675a0aa94d517b1_24.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_26.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_28.png)

以下是定义和调用类方法的示例：

![](img/ce6df8820230e84c9675a0aa94d517b1_30.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_32.png)

```python
class Person:
    @classmethod
    def walk(cls):
        print("正在走路")

![](img/ce6df8820230e84c9675a0aa94d517b1_34.png)

# 无需实例化，直接通过类名调用
Person.walk()  # 输出：正在走路
```

类方法最常见的用途是对类属性进行操作（增删改查），因为不建议通过实例来直接修改类属性。

![](img/ce6df8820230e84c9675a0aa94d517b1_36.png)

---

![](img/ce6df8820230e84c9675a0aa94d517b1_38.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_40.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_42.png)

## 静态方法

静态方法也属于类，但它既不接收代表实例的 `self` 参数，也不接收代表类的 `cls` 参数。定义静态方法需要使用 `@staticmethod` 装饰器。

以下是静态方法的示例：

![](img/ce6df8820230e84c9675a0aa94d517b1_44.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_46.png)

```python
class Person:
    @staticmethod
    def jump():
        print("正在跳")

![](img/ce6df8820230e84c9675a0aa94d517b1_48.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_50.png)

# 可以通过类名或实例名调用
Person.jump()  # 输出：正在跳

![](img/ce6df8820230e84c9675a0aa94d517b1_52.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_54.png)

p = Person()
p.jump()       # 输出：正在跳
```

![](img/ce6df8820230e84c9675a0aa94d517b1_56.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_58.png)

静态方法的作用在于**标识该方法可以被这个类所用**。它与在类外部定义一个普通函数几乎没有区别，但放在类内部可以避免命名冲突（即函数遮蔽问题），并使代码结构更清晰。

> **关于函数遮蔽**：如果在同一个作用域内有两个同名函数，后定义的会“遮蔽”先定义的。将函数作为静态方法放在类内部，可以有效管理命名空间。

![](img/ce6df8820230e84c9675a0aa94d517b1_60.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_62.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_64.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_66.png)

---

![](img/ce6df8820230e84c9675a0aa94d517b1_68.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_70.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_71.png)

## 核心方法总结

![](img/ce6df8820230e84c9675a0aa94d517b1_73.png)

以下是三种方法的对比：

![](img/ce6df8820230e84c9675a0aa94d517b1_75.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_77.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_79.png)

*   **实例方法**：最常用。第一个参数是 `self`，代表实例。用于定义对象的行为。
*   **类方法**：使用 `@classmethod` 装饰，第一个参数是 `cls`。用于操作类属性。
*   **静态方法**：使用 `@staticmethod` 装饰，无特殊参数。用于组织与类相关的工具函数。

![](img/ce6df8820230e84c9675a0aa94d517b1_81.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_83.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_85.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_87.png)

对于初学者，掌握实例方法和构造函数 `__init__` 已能满足大部分需求。

![](img/ce6df8820230e84c9675a0aa94d517b1_89.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_91.png)

---

![](img/ce6df8820230e84c9675a0aa94d517b1_93.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_95.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_97.png)

## 实战案例：模拟量化交易策略 🧮

![](img/ce6df8820230e84c9675a0aa94d517b1_99.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_101.png)

为了将知识串联起来，我们构建一个简化的量化交易策略模拟类。这个类会模拟：
1.  接收模拟的市场数据（tick）。
2.  根据数据判断交易条件。
3.  执行模拟的“开仓”或“平仓”操作。

![](img/ce6df8820230e84c9675a0aa94d517b1_103.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_105.png)

### 1. 类结构与构造函数

![](img/ce6df8820230e84c9675a0aa94d517b1_107.png)

我们首先定义类 `DataCalculate`，并在 `__init__` 中初始化一些属性。

![](img/ce6df8820230e84c9675a0aa94d517b1_109.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_111.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_113.png)

```python
from random import randint
from time import sleep

![](img/ce6df8820230e84c9675a0aa94d517b1_115.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_117.png)

class DataCalculate:
    def __init__(self, loop_times=10000):
        self.numbers = loop_times  # 模拟运行次数
        self.position = 0          # 当前持仓，0表示空仓
```

![](img/ce6df8820230e84c9675a0aa94d517b1_119.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_121.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_123.png)

### 2. 模拟获取市场数据

![](img/ce6df8820230e84c9675a0aa94d517b1_125.png)

我们创建一个实例方法 `get_tick`，用随机数模拟每秒接收到的市场报价。

![](img/ce6df8820230e84c9675a0aa94d517b1_127.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_129.png)

```python
    def get_tick(self):
        """模拟获取一次市场报价（tick）"""
        number = randint(0, 100)  # 生成一个0-100的随机数代表价格
        return number
```

![](img/ce6df8820230e84c9675a0aa94d517b1_131.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_133.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_135.png)

### 3. 策略判断逻辑

![](img/ce6df8820230e84c9675a0aa94d517b1_137.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_139.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_141.png)

创建 `calculate` 方法，根据获取到的“价格”判断是否满足交易条件。这里用简单的数学条件模拟复杂的策略逻辑（例如均线交叉）。

![](img/ce6df8820230e84c9675a0aa94d517b1_143.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_145.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_147.png)

```python
    def calculate(self, number):
        """根据tick数据判断交易信号"""
        # 条件1：模拟第一个指标条件
        if number % 2 == 0:
            print(f"Tick数据 {number}: 满足第一个条件")
            # 条件2：模拟第二个确认条件
            if number % 3 == 0:
                print(f"Tick数据 {number}: 满足第二个条件，可以开仓")
                return 'open'
            else:
                return 'wait'
        # 平仓条件模拟
        elif number % 5 == 0:
            print(f"Tick数据 {number}: 满足平仓条件")
            return 'close'
        else:
            print(f"Tick数据 {number}: 不满足条件，等待下一个tick")
            return 'wait'
```

### 4. 执行交易操作

![](img/ce6df8820230e84c9675a0aa94d517b1_149.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_151.png)

定义 `open_position`（开仓）和 `close_position`（平仓）方法，它们会根据信号修改持仓状态 `self.position`。

![](img/ce6df8820230e84c9675a0aa94d517b1_153.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_155.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_157.png)

```python
    def open_position(self, number, size):
        """模拟开仓操作"""
        if number > 50:
            print(f"价格{number}>50，执行开多仓{size}手")
            self.position += size
        else:
            print(f"价格{number}<=50，执行开空仓{size}手")
            self.position -= size
        print(f"当前持仓为: {self.position}手")

    def close_position(self):
        """模拟平仓操作"""
        print("执行平仓操作")
        self.position = 0
        print(f"当前持仓为: {self.position}手")
```

### 5. 使用Property装饰器计算仓位大小

![](img/ce6df8820230e84c9675a0aa94d517b1_159.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_161.png)

我们引入 `@property` 装饰器，它将一个方法“伪装”成属性。这里用于动态计算每次开仓的手数。

![](img/ce6df8820230e84c9675a0aa94d517b1_163.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_165.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_166.png)

```python
    @property
    def size(self):
        """动态计算本次开仓手数（这里简化为随机）"""
        return randint(1, 10)
```
现在，我们可以像访问属性一样使用 `self.size` 来获取计算好的手数，而不需要加括号调用。

![](img/ce6df8820230e84c9675a0aa94d517b1_168.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_170.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_172.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_173.png)

### 6. 启动策略循环

![](img/ce6df8820230e84c9675a0aa94d517b1_175.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_177.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_179.png)

最后，创建一个 `start` 方法，将所有步骤串联起来，形成完整的策略循环。

![](img/ce6df8820230e84c9675a0aa94d517b1_180.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_182.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_184.png)

```python
    def start(self):
        """启动策略循环"""
        for i in range(self.numbers):
            # 1. 获取数据
            tick_data = self.get_tick()
            # 2. 计算信号
            signal = self.calculate(tick_data)

            # 3. 执行交易
            if signal == 'open':
                self.open_position(tick_data, self.size) # 使用property
            elif signal == 'close' and self.position != 0:
                self.close_position()

            sleep(0.5)  # 模拟等待下一个tick
```

![](img/ce6df8820230e84c9675a0aa94d517b1_186.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_188.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_190.png)

### 7. 运行策略

![](img/ce6df8820230e84c9675a0aa94d517b1_192.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_194.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_196.png)

实例化类并启动策略，观察模拟运行过程。

![](img/ce6df8820230e84c9675a0aa94d517b1_198.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_200.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_202.png)

```python
# 实例化，并传入循环次数（比如100次）
strategy = DataCalculate(100)
# 启动策略
strategy.start()
```

![](img/ce6df8820230e84c9675a0aa94d517b1_204.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_206.png)

---

![](img/ce6df8820230e84c9675a0aa94d517b1_208.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_210.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_212.png)

## 总结

![](img/ce6df8820230e84c9675a0aa94d517b1_214.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_216.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_218.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_220.png)

本节课我们一起深入学习了Python中类的更多特性：

![](img/ce6df8820230e84c9675a0aa94d517b1_222.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_224.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_226.png)

1.  **类方法**：使用 `@classmethod` 装饰，用于操作类属性。
2.  **静态方法**：使用 `@staticmethod` 装饰，用于组织与类逻辑相关但不依赖实例或类状态的函数。
3.  **实战融合**：通过构建一个**模拟量化交易策略类**，我们综合运用了：
    *   构造函数 `__init__` 初始化属性。
    *   实例方法定义对象行为。
    *   在类内部调用其他实例方法（`self.方法名()`）。
    *   使用 `@property` 装饰器创建可计算的“属性”。
    *   将数据获取、逻辑判断、行为执行封装在一个完整的类中。

![](img/ce6df8820230e84c9675a0aa94d517b1_228.png)

![](img/ce6df8820230e84c9675a0aa94d517b1_230.png)

这个案例虽然简单，但清晰地展示了使用类来组织代码、模拟真实业务流程（如量化交易）的基本模式。真正的项目无非是将这些简单的模块不断细化和组合。请务必亲自动手编写和修改这个案例，这是理解面向对象编程的关键。