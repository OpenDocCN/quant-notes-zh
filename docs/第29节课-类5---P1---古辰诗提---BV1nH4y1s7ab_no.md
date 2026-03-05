# Python基础课：第29课：类5 - 多态与高级特性

![](img/4e08dbc767a1187ab7f24159f02a6fbf_1.png)

在本节课中，我们将要学习面向对象编程的第三个核心特性——多态，并了解一些类的高级特性，如抽象基类、`__slots__`属性和使用`type`动态创建类。这些知识将帮助你编写更健壮、更高效的代码。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_3.png)

---

![](img/4e08dbc767a1187ab7f24159f02a6fbf_5.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_7.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_9.png)

## 多态：一个接口，多种实现

![](img/4e08dbc767a1187ab7f24159f02a6fbf_10.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_12.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_14.png)

上一节我们介绍了封装和继承，本节中我们来看看多态。多态可以简单理解为：通过父类的同一个接口，实现不同的功能。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_16.png)

### 多态示例

![](img/4e08dbc767a1187ab7f24159f02a6fbf_18.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_20.png)

以下是一个简单的多态示例。我们首先定义一个父类 `Person`，它有一个 `sing` 方法。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_22.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_24.png)

```python
class Person:
    def sing(self, song_name: str):
        print(f"正在唱歌，唱的是{song_name}")
```

接着，我们创建两个子类 `Man` 和 `Woman`，它们都继承自 `Person` 并重写了 `sing` 方法，以实现不同的行为。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_26.png)

```python
class Man(Person):
    def sing(self, song_name: str):
        print(f"男生正在唱歌，歌曲的名字是{song_name}")

![](img/4e08dbc767a1187ab7f24159f02a6fbf_28.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_30.png)

class Woman(Person):
    def sing(self, song_name: str):
        print(f"女生正在唱歌，唱的歌曲是{song_name}")
```

![](img/4e08dbc767a1187ab7f24159f02a6fbf_32.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_34.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_36.png)

现在，我们定义一个统一的调用接口 `Sing` 类。它接收一个 `Person` 类型的对象，并调用其 `sing` 方法。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_38.png)

```python
class Sing:
    def __init__(self, person: Person):
        self.person = person

    def sing(self, song_name: str):
        self.person.sing(song_name)
```

![](img/4e08dbc767a1187ab7f24159f02a6fbf_40.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_42.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_44.png)

以下是使用示例：

![](img/4e08dbc767a1187ab7f24159f02a6fbf_46.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_47.png)

```python
p_man = Man()
p_woman = Woman()

![](img/4e08dbc767a1187ab7f24159f02a6fbf_49.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_51.png)

sing_man = Sing(p_man)
sing_man.sing("黄河大合唱")

![](img/4e08dbc767a1187ab7f24159f02a6fbf_53.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_55.png)

sing_woman = Sing(p_woman)
sing_woman.sing("茉莉花")
```

![](img/4e08dbc767a1187ab7f24159f02a6fbf_57.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_59.png)

运行这段代码，你会发现，虽然我们调用的都是 `Sing` 类的 `sing` 方法，但由于传入的对象不同（`Man` 或 `Woman`），最终执行的是各自子类中重写的方法。这就是多态：**一个接口（`Sing.sing`），多种实现（`Man.sing` 和 `Woman.sing`）**。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_61.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_63.png)

### 多态的实际应用

![](img/4e08dbc767a1187ab7f24159f02a6fbf_65.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_67.png)

在真实的项目开发中，多态非常有用。例如，在 `vn.py` 量化框架中，数据库接口就使用了多态。框架定义了一个抽象的 `BaseDatabase` 父类，规定了所有数据库必须实现的方法（如保存数据、查询数据）。然后，针对不同的数据库（如 `SQLiteDatabase`、`MySQLDatabase`），创建具体的子类来实现这些方法。这样，上层代码只需要调用统一的 `BaseDatabase` 接口，而无需关心底层具体是哪种数据库，从而实现了代码的解耦和复用。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_69.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_71.png)

---

![](img/4e08dbc767a1187ab7f24159f02a6fbf_73.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_75.png)

## 抽象基类：强制子类实现特定方法

![](img/4e08dbc767a1187ab7f24159f02a6fbf_77.png)

在实现多态时，我们通常希望父类能强制要求其子类必须实现某些关键方法，以避免遗漏。这时就需要用到抽象基类。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_79.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_81.png)

### 使用 `ABC` 和 `@abstractmethod`

![](img/4e08dbc767a1187ab7f24159f02a6fbf_83.png)

Python 通过 `abc` 模块提供了抽象基类的支持。我们需要从 `ABC` 类继承，并使用 `@abstractmethod` 装饰器来标记那些必须由子类实现的方法。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_85.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_87.png)

以下是修改后的 `Person` 类示例：

```python
from abc import ABC, abstractmethod

![](img/4e08dbc767a1187ab7f24159f02a6fbf_89.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_91.png)

class Person(ABC):
    @abstractmethod
    def sing(self, song_name: str):
        pass
```

![](img/4e08dbc767a1187ab7f24159f02a6fbf_93.png)

现在，任何继承自 `Person` 的子类，如果没有实现 `sing` 方法，在尝试实例化时就会报错。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_95.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_96.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_98.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_100.png)

```python
class Man(Person):
    # 如果这里不实现 sing 方法，实例化时会报错：TypeError: Can't instantiate abstract class Man with abstract method sing
    def sing(self, song_name: str):
        print(f"男生正在唱歌，歌曲的名字是{song_name}")
```

这种机制确保了代码的健壮性，特别是在大型项目中，可以防止开发者忘记实现关键功能。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_102.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_104.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_106.png)

---

![](img/4e08dbc767a1187ab7f24159f02a6fbf_108.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_110.png)

## `__slots__`：限制类的属性和方法

![](img/4e08dbc767a1187ab7f24159f02a6fbf_112.png)

有时，我们希望限制一个类的实例可以动态添加哪些属性和方法，以提高内存利用率和执行效率。这时可以使用 `__slots__` 属性。

### `__slots__` 的使用

![](img/4e08dbc767a1187ab7f24159f02a6fbf_114.png)

`__slots__` 是一个类属性，它是一个元组，用于指定该类实例允许拥有的属性名。

```python
class Person:
    __slots__ = ('name', 'age', 'sleep')

    def __init__(self, name):
        self.name = name

p = Person("小张")
p.age = 18  # 允许，因为 'age' 在 __slots__ 中
p.gender = 'male'  # 报错：AttributeError: 'Person' object has no attribute 'gender'
```

![](img/4e08dbc767a1187ab7f24159f02a6fbf_116.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_118.png)

**需要注意的是：**
*   `__slots__` 只对当前类的实例生效，对其子类无效。
*   它主要作用是**限制实例属性的动态添加**，对于类中已定义的方法和属性没有影响。
*   使用 `__slots__` 可以节省内存，因为实例不再使用 `__dict__` 字典来存储属性，而是使用更紧凑的数据结构。这在创建大量实例对象时效果显著。

---

![](img/4e08dbc767a1187ab7f24159f02a6fbf_120.png)

## 使用 `type` 动态创建类

![](img/4e08dbc767a1187ab7f24159f02a6fbf_122.png)

我们通常使用 `type()` 函数来查看对象的类型。但 `type` 还有另一个强大的功能：**动态创建类**。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_124.png)

### `type` 的第二种用法

`type` 函数可以接收三个参数来创建一个新的类：
1.  `name`：类的名称（字符串）。
2.  `bases`：继承的父类（元组）。
3.  `dict`：类的属性和方法（字典）。

以下是动态创建类的示例：

![](img/4e08dbc767a1187ab7f24159f02a6fbf_126.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_128.png)

```python
# 1. 先定义一个方法
def eat(self):
    print(f"{self.name} 在吃饭")

# 2. 使用 type 动态创建 Man 类，它继承自 object，并拥有 name、age 属性和 eat 方法
Man = type('Man', (object,), {'name': '王总', 'age': 18, 'eat': eat})

![](img/4e08dbc767a1187ab7f24159f02a6fbf_130.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_132.png)

# 3. 实例化并使用
man1 = Man()
print(man1.name)  # 输出：王总
print(man1.age)   # 输出：18
man1.eat()        # 输出：王总 在吃饭
```

![](img/4e08dbc767a1187ab7f24159f02a6fbf_134.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_136.png)

这种动态创建类的方式在需要根据运行时条件（如配置文件、用户输入）来生成不同类结构的场景下非常有用，它提供了极大的灵活性。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_138.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_140.png)

---

![](img/4e08dbc767a1187ab7f24159f02a6fbf_142.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_144.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_146.png)

![](img/4e08dbc767a1187ab7f24159f02a6fbf_148.png)

## 总结

![](img/4e08dbc767a1187ab7f24159f02a6fbf_150.png)

本节课中我们一起学习了面向对象编程的多个高级主题：

1.  **多态**：理解了如何通过父类的统一接口，让不同的子类对象表现出不同的行为，这是实现代码灵活性和可扩展性的关键。
2.  **抽象基类**：学会了使用 `ABC` 和 `@abstractmethod` 来定义抽象父类，强制子类实现特定方法，从而构建更严谨的类层次结构。
3.  **`__slots__`**：了解了如何使用 `__slots__` 来限制实例属性的动态添加，这有助于提升程序性能并防止意外的属性赋值。
4.  **动态创建类**：掌握了 `type` 函数的第二种用法，即动态地创建类，这为元编程和框架设计提供了强大的工具。

![](img/4e08dbc767a1187ab7f24159f02a6fbf_152.png)

这些概念是构建复杂、健壮Python应用程序的基石。在后续学习 `vn.py` 等框架时，你会频繁地看到这些技术的实际应用。