# 量化交易零基础：第二十一课：Python面向对象编程入门 🐍

![](img/ffeec31b1792f840c5698da4db94b300_1.png)

在本节课中，我们将要学习Python编程中一个非常重要的概念——面向对象编程。与之前学习的顺序执行不同，面向对象是一种更接近人类思维的编程方式，它通过“类”和“对象”来组织代码和数据，是现代编程语言的核心思想。

## 类与对象的概念

上一节我们介绍了顺序执行的编程逻辑。本节中，我们来看看面向对象编程的基础：类与对象。

“类”是一个抽象的概念，它定义了一种新的数据类型，就像我们之前学过的整数、字符串或字典类型一样。一个类可以包含变量（称为**属性**）和函数（称为**方法**）。

“对象”则是类的具体实例。我们可以把“类”想象成“狗”这个物种，而“对象”则是“邻居家的二哈”这条具体的狗。

定义一个类的基本语法如下：
```python
class 类名:
    # 类体（属性和方法的定义）
```

## 类的定义与使用

以下是定义一个简单类并创建其对象的例子。

```python
class Animal:
    name = “动物”  # 这是一个属性

    def eat(self):  # 这是一个方法
        print(self.name + “在吃东西”)

# 创建对象（实例化）
a = Animal()
b = Animal()

b.name = “B”  # 修改对象的属性
a.eat()  # 输出：动物在吃东西
b.eat()  # 输出：B在吃东西

print(a)  # 输出对象在内存中的地址
print(b)  # 输出另一个不同的地址
```

从打印结果可以看出，`a`和`b`是两个独立的对象，它们占用不同的内存空间。类本身是模板，而对象是根据模板创建的具体实体。

## 构造函数

在创建对象时，我们经常需要为属性设置初始值。每次都先创建再修改会很麻烦。这时，我们可以使用构造函数 `__init__`。

![](img/ffeec31b1792f840c5698da4db94b300_3.png)

构造函数是一个特殊的方法，在创建对象时会自动调用，用于初始化对象的属性。

```python
class Person:
    def __init__(self, name, age, score):
        self.name = name
        self.age = age
        self.score = score

    def about(self):
        print(f“{self.name}, {self.age}岁，数学{self.score}分”)

![](img/ffeec31b1792f840c5698da4db94b300_5.png)

# 创建对象时直接传入初始值
p1 = Person(“小明”, 10, 95)
p2 = Person(“小红”, 9, 98)
p1.about()
p2.about()
```

![](img/ffeec31b1792f840c5698da4db94b300_7.png)

![](img/ffeec31b1792f840c5698da4db94b300_9.png)

**注意**：一旦定义了带参数的构造函数，创建对象时就必须传入相应数量的参数，否则会报错。

## 面向对象的三大特性

面向对象编程有三大核心思想：封装、继承和多态。它们是构建复杂、可维护程序的基础。

![](img/ffeec31b1792f840c5698da4db94b300_11.png)

### 1. 封装性 🛡️

![](img/ffeec31b1792f840c5698da4db94b300_13.png)

封装是指将数据（属性）和操作数据的方法包装在类内部，并对外部隐藏实现的细节。我们可以控制哪些属性或方法可以被外部访问。

![](img/ffeec31b1792f840c5698da4db94b300_15.png)

在Python中，以双下划线 `__` 开头的属性被视为私有属性，不能在类外部直接访问或修改。

![](img/ffeec31b1792f840c5698da4db94b300_17.png)

```python
class Encapsulation:
    def __init__(self, name, math_score):
        self.name = name
        self.__math_score = math_score  # 私有属性

    def print_score(self):
        print(f“{self.name}的数学成绩是：{self.__math_score}”)

    def set_score(self, new_score):  # 通过公共方法修改私有属性
        self.__math_score = new_score

![](img/ffeec31b1792f840c5698da4db94b300_19.png)

# 使用
stu = Encapsulation(“张三”, 60)
stu.print_score()  # 输出：张三的数学成绩是：60

![](img/ffeec31b1792f840c5698da4db94b300_21.png)

# stu.__math_score = 80  # 错误！无法直接修改私有属性
stu.set_score(70)  # 通过公共方法修改
stu.print_score()  # 输出：张三的数学成绩是：70
```

封装性保护了对象内部的数据，只通过我们提供的方法来修改，提高了代码的安全性和可维护性。

![](img/ffeec31b1792f840c5698da4db94b300_23.png)

### 2. 继承性 👨‍👦

![](img/ffeec31b1792f840c5698da4db94b300_25.png)

![](img/ffeec31b1792f840c5698da4db94b300_27.png)

继承允许我们定义一个类（子类）来继承另一个类（父类）的属性和方法。子类可以拥有父类的所有功能，同时还能添加自己特有的功能。

```python
class Animal:  # 父类
    def __init__(self, name):
        self.name = name
        self.__secret = “小秘密”  # 私有属性

    def eat(self):
        print(self.name + “在吃东西”)

![](img/ffeec31b1792f840c5698da4db94b300_29.png)

class Dog(Animal):  # 子类，继承自Animal
    pass  # 暂时不添加新内容

# 使用
my_dog = Dog(“旺财”)
my_dog.eat()  # 可以调用父类的方法
# print(my_dog.__secret)  # 错误！无法继承父类的私有属性
```

**注意**：子类无法直接访问父类中以双下划线开头的私有属性。

### 3. 多态性 🎭

多态是指不同的子类对象调用相同的父类方法，可以产生不同的执行结果。它允许我们使用统一的接口来处理不同类型的对象。

![](img/ffeec31b1792f840c5698da4db94b300_31.png)

```python
class Animal:
    def eat(self):
        print(“吃东西”)
    def walk(self):
        print(“用脚走路”)

![](img/ffeec31b1792f840c5698da4db94b300_33.png)

class Dog(Animal):  # 继承Animal
    def eat(self):  # 重写（覆盖）父类方法
        print(“吃骨头”)
    def walk(self):
        print(“四脚走路”)

![](img/ffeec31b1792f840c5698da4db94b300_35.png)

def animal_action(animal: Animal):  # 指定参数必须是Animal类型或其子类
    animal.eat()
    animal.walk()

![](img/ffeec31b1792f840c5698da4db94b300_37.png)

# 使用
a = Dog()
a.name = “邻居家二哈”
animal_action(a)  # 传递Dog对象

![](img/ffeec31b1792f840c5698da4db94b300_39.png)

b = Animal()
animal_action(b)  # 传递Animal对象
```

运行上述代码，`Dog`对象会输出“吃骨头”和“四脚走路”，而`Animal`对象会输出“吃东西”和“用脚走路”。虽然调用的函数名相同，但具体行为取决于对象的实际类型。

多态性在大型项目开发中极为重要。例如，可以定义一个统一的“工厂”接口，让不同的“手机工厂”、“汽车工厂”子类去实现各自的“制造”方法。业务逻辑代码只需要调用统一的“制造”接口，就能处理各种不同的产品，极大地提高了代码的扩展性和可维护性。

![](img/ffeec31b1792f840c5698da4db94b300_41.png)

![](img/ffeec31b1792f840c5698da4db94b300_43.png)

## 总结

![](img/ffeec31b1792f840c5698da4db94b300_45.png)

本节课中我们一起学习了Python面向对象编程的核心概念。

![](img/ffeec31b1792f840c5698da4db94b300_47.png)

![](img/ffeec31b1792f840c5698da4db94b300_49.png)

*   **类与对象**：类是抽象的模板（如“狗”），对象是具体的实例（如“二哈”）。
*   **构造函数 (`__init__`)**: 用于在创建对象时初始化属性。
*   **三大特性**:
    *   **封装**：隐藏内部实现细节，通过公共方法访问和修改数据。
    *   **继承**：子类继承父类的属性和方法，实现代码复用。
    *   **多态**：同一接口在不同对象上产生不同行为，提高程序灵活性。

![](img/ffeec31b1792f840c5698da4db94b300_51.png)

面向对象是现代编程的基石，虽然初学可能觉得有些抽象，但通过不断练习和实践，你会逐渐体会到它组织代码的强大之处。几乎所有现代高级语言都离不开面向对象的思想，掌握它对于后续的量化交易系统开发至关重要。