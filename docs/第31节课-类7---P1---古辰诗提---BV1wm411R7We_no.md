# Python基础课：31：枚举类与魔术方法

![](img/439b86123eb1b5dbdc3465579ee846de_0.png)

在本节课中，我们将学习两个重要的概念：枚举类（Enum）和魔术方法（Magic Methods）。枚举类用于定义一组有限的常量，使代码更清晰、更安全。魔术方法则是Python中那些以双下划线开头和结尾的特殊方法，它们赋予了类各种特殊行为，例如运算符重载、对象调用等。掌握这些知识，能让你对类的理解更加深入。

## 🧮 枚举类

上一节我们介绍了类的底层创建原理，本节中我们来看看枚举类。枚举类是一种特殊的类，用于定义一组有限的、不可变的常量集合。在Python中，我们可以使用 `enum` 模块来创建枚举类。

以下是枚举类的基本用法：

```python
from enum import Enum

class Exchange(Enum):
    SHFE = "SHFE"
    DCE = "DCE"
    CZCE = "CZCE"
    INE = "INE"
    CFFEX = "CFFEX"
```

枚举类中的成员是常量，不可更改。例如，尝试修改 `Exchange.SHFE` 的值会引发错误。

```python
# 以下操作会报错
# Exchange.SHFE = "SH"
```

枚举类提供了便捷的方法进行值和名称的转换。例如，获取枚举成员的值或名称：

```python
# 获取值
print(Exchange.SHFE.value)  # 输出: SHFE

# 获取名称
print(Exchange.SHFE.name)   # 输出: SHFE

# 通过值获取枚举成员
print(Exchange("SHFE"))     # 输出: Exchange.SHFE
```

在vn.py等量化框架中，枚举类被广泛用于定义方向、状态、产品类型等常量，这比使用字典或列表更加规范和易于维护。

## 🎩 魔术方法

魔术方法，也称为特殊方法或双下划线方法，是Python中用于实现类特定行为的内置方法。我们已经接触过 `__new__` 和 `__init__`，本节我们将学习更多常用的魔术方法。

### `__del__` 方法

`__del__` 方法在对象被销毁（例如，内存被释放）时自动调用。它通常用于执行一些清理工作。

```python
class Person:
    def __init__(self, name):
        self.name = name
        print(f"{self.name} 被创建")

    def __del__(self):
        print(f"{self.name} 被销毁")

# 创建对象
p1 = Person("小明")
# 手动删除对象或程序结束时，__del__ 会被调用
del p1
```

### `__add__` 与 `__radd__` 方法

`__add__` 方法用于重载加法运算符 `+`。当对象位于加号左侧时，会调用此方法。

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __add__(self, other):
        # 假设 other 也是 Person 实例，将年龄相加
        if isinstance(other, Person):
            new_age = self.age + other.age
            return Person("合并", new_age)
        else:
            raise TypeError("不支持的类型")

p1 = Person("张三", 20)
p2 = Person("李四", 30)
p3 = p1 + p2  # 调用 p1.__add__(p2)
print(p3.age)  # 输出: 50
```

当对象位于加号右侧时，需要实现 `__radd__` 方法。

```python
    def __radd__(self, other):
        # 当对象在加号右侧时调用
        return self.__add__(other)
```

### `__iadd__` 方法

`__iadd__` 方法用于重载原地加法运算符 `+=`。

```python
    def __iadd__(self, other):
        if isinstance(other, (int, float)):
            self.age += other
            return self  # 通常返回自身以实现原地修改
        else:
            raise TypeError("不支持的类型")

p1 = Person("张三", 20)
p1 += 10  # 调用 p1.__iadd__(10)
print(p1.age)  # 输出: 30
```

### `__call__` 方法

`__call__` 方法使得类的实例可以像函数一样被调用。

```python
class Person:
    def __init__(self, name):
        self.name = name

    def __call__(self, food):
        print(f"{self.name} 正在吃 {food}")

p1 = Person("小明")
p1("苹果")  # 输出: 小明 正在吃 苹果
```

### `__or__` 方法

`__or__` 方法用于重载按位或运算符 `|`，但常被用来实现逻辑“或”的语义。

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age

    def __or__(self, other):
        # 如果两人年龄之和大于40，返回True
        if isinstance(other, Person):
            return self.age + other.age > 40
        return False

p1 = Person("张三", 20)
p2 = Person("李四", 25)
if p1 | p2:
    print("年龄之和大于40")
else:
    print("年龄之和不大于40")
```

## 📝 总结

![](img/439b86123eb1b5dbdc3465579ee846de_2.png)

本节课中我们一起学习了枚举类和魔术方法。枚举类帮助我们定义一组清晰、安全的常量。魔术方法则赋予了类强大的能力，让我们可以自定义对象在运算、调用时的行为。理解并合理运用这些特性，能够让你写出更优雅、更强大的Python代码。