# Python基础课：27：类（三） - 封装、私有与继承 🧱➡️🔒➡️👨‍👦

![](img/4a0ccc5537baf7a8373bae742b88511c_1.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_3.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_5.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_7.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_9.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_11.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_13.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_14.png)

在本节课中，我们将继续深入学习Python类的核心概念。我们将探讨如何从外部为实例添加方法，理解私有属性和私有方法的含义与实现，并初步接触面向对象编程的三大特性之一：继承。这些知识将帮助你更好地组织和管理代码。

![](img/4a0ccc5537baf7a8373bae742b88511c_16.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_18.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_19.png)

---

![](img/4a0ccc5537baf7a8373bae742b88511c_21.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_23.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_25.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_27.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_29.png)

上一节我们通过一个量化的小示例实践了类的使用。本节中，我们来看看如何从类的外部为实例动态添加方法。

![](img/4a0ccc5537baf7a8373bae742b88511c_31.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_33.png)

### 从外部创建实例方法

![](img/4a0ccc5537baf7a8373bae742b88511c_35.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_37.png)

在Python中，你可以为一个已经实例化的对象动态地添加方法。这并非封装的最佳实践，但作为语言特性需要了解。

![](img/4a0ccc5537baf7a8373bae742b88511c_39.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_41.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_43.png)

以下是如何操作的示例：

![](img/4a0ccc5537baf7a8373bae742b88511c_45.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_47.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_49.png)

```python
class Person:
    def __init__(self, name):
        self.name = name

    def speak(self, message):
        print(f"{self.name}说：'{str(message)}'")

![](img/4a0ccc5537baf7a8373bae742b88511c_51.png)

# 定义一个外部函数，它将作为新方法
def sleep_func(self):
    print(f"{self.name}正在睡觉")

![](img/4a0ccc5537baf7a8373bae742b88511c_53.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_55.png)

# 实例化一个对象
p1 = Person("小王")

![](img/4a0ccc5537baf7a8373bae742b88511c_57.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_59.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_61.png)

# 将外部函数绑定为实例的方法（注意：是赋值，不是调用）
p1.sleep = sleep_func

![](img/4a0ccc5537baf7a8373bae742b88511c_63.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_65.png)

# 现在可以调用这个新方法
p1.sleep(p1)  # 需要手动传入self
```
运行上述代码，会输出“小王正在睡觉”。需要注意的是，这种外部绑定的方法在调用时，需要手动将实例本身（`self`）作为第一个参数传入，这与在类内部定义的方法自动绑定`self`不同。

![](img/4a0ccc5537baf7a8373bae742b88511c_67.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_69.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_71.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_73.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_75.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_76.png)

---

![](img/4a0ccc5537baf7a8373bae742b88511c_78.png)

理解了外部添加方法后，我们进入封装的核心部分：私有属性和私有方法。它们用于隐藏类的内部实现细节。

![](img/4a0ccc5537baf7a8373bae742b88511c_80.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_82.png)

### 私有属性与私有方法

![](img/4a0ccc5537baf7a8373bae742b88511c_84.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_86.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_88.png)

私有成员（属性或方法）旨在只能在类的内部被访问和修改，外部无法直接操作，这增强了代码的安全性和封装性。

![](img/4a0ccc5537baf7a8373bae742b88511c_90.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_92.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_94.png)

**定义私有属性**：在属性名前加两个下划线 `__`。
**定义私有方法**：在方法名前加两个下划线 `__`。

![](img/4a0ccc5537baf7a8373bae742b88511c_96.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_98.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_100.png)

```python
class Person:
    def __init__(self, name):
        self.name = name
        self.__id = 123456  # 私有属性

    def __private_eat(self, food):  # 私有方法
        print(f"{self.name}正在吃{food}")

    def public_eat(self, food):
        # 在类的内部可以调用私有方法
        self.__private_eat(food)

    def get_id(self):  # 提供访问私有属性的公共接口
        return self.__id

![](img/4a0ccc5537baf7a8373bae742b88511c_102.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_104.png)

# 使用
p1 = Person("小李")
# print(p1.__id)  # 报错：AttributeError
print(p1.get_id())  # 正确：通过公共方法访问
# p1.__private_eat("米饭")  # 报错：AttributeError
p1.public_eat("面条")  # 正确：通过公共方法间接调用私有方法
```

![](img/4a0ccc5537baf7a8373bae742b88511c_106.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_108.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_110.png)

**重要说明**：Python中的“私有”并非绝对安全，而是一种名称改写（Name Mangling）机制。私有成员在底层被重命名为 `_类名__成员名` 的形式。因此，理论上仍可通过 `p1._Person__id` 这种方式访问，但强烈不建议这样做，这破坏了封装原则。

![](img/4a0ccc5537baf7a8373bae742b88511c_112.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_114.png)

---

![](img/4a0ccc5537baf7a8373bae742b88511c_116.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_118.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_119.png)

封装让我们可以保护数据，而继承则允许我们基于已有的类创建新类，实现代码的复用和扩展。

![](img/4a0ccc5537baf7a8373bae742b88511c_121.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_123.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_125.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_127.png)

### 类的继承

![](img/4a0ccc5537baf7a8373bae742b88511c_129.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_131.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_133.png)

继承是面向对象编程的基石。子类（派生类）可以继承父类（基类）的属性和方法，并可以添加自己特有的属性和方法，或重写父类的方法。

![](img/4a0ccc5537baf7a8373bae742b88511c_135.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_137.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_139.png)

以下是继承的基本语法和应用：

![](img/4a0ccc5537baf7a8373bae742b88511c_141.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_143.png)

```python
# 父类
class Animal:
    def __init__(self, name):
        self.name = name

    def eat(self):
        print(f"{self.name}在吃东西")

![](img/4a0ccc5537baf7a8373bae742b88511c_145.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_147.png)

# 子类继承父类
class Cat(Animal):  # 括号内指定要继承的父类
    def meow(self):  # 子类特有的方法
        print(f"{self.name}喵喵叫")

![](img/4a0ccc5537baf7a8373bae742b88511c_149.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_151.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_153.png)

# 使用继承
my_cat = Cat("咪咪")
my_cat.eat()   # 调用继承自父类的方法
my_cat.meow()  # 调用子类自己的方法
```

![](img/4a0ccc5537baf7a8373bae742b88511c_155.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_157.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_159.png)

#### 方法重写

![](img/4a0ccc5537baf7a8373bae742b88511c_161.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_163.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_165.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_167.png)

如果子类需要修改父类某个方法的行为，可以在子类中重新定义一个同名方法，这称为方法重写（Override）。

![](img/4a0ccc5537baf7a8373bae742b88511c_169.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_171.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_173.png)

```python
class Dog(Animal):
    # 重写父类的eat方法
    def eat(self):
        print(f"{self.name}狼吞虎咽地吃狗粮")

![](img/4a0ccc5537baf7a8373bae742b88511c_175.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_177.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_179.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_181.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_183.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_185.png)

my_dog = Dog("旺财")
my_dog.eat()  # 输出：旺财狼吞虎咽地吃狗粮
```
当调用一个方法时，Python会首先在子类中寻找，如果找不到，才会去父类中寻找。

![](img/4a0ccc5537baf7a8373bae742b88511c_187.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_189.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_191.png)

---

![](img/4a0ccc5537baf7a8373bae742b88511c_193.png)

![](img/4a0ccc5537baf7a8373bae742b88511c_194.png)

本节课中我们一起学习了类的几个高级主题：如何从外部为实例添加方法，如何使用私有属性和私有方法来加强封装，以及类继承的基本概念和方法重写。理解这些概念对于构建结构清晰、易于维护的复杂程序至关重要。下一节课，我们将继续深入探讨继承的更多细节，例如如何调用父类方法以及多重继承。