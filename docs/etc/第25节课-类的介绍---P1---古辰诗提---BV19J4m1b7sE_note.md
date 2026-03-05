# Python基础课：25：类的介绍 🧑‍🏫

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_1.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_3.png)

在本节课中，我们将要学习Python中一个核心概念——**类**。类是面向对象编程的基石，理解它对于编写结构清晰、易于维护的代码至关重要。我们将从面向过程与面向对象的区别讲起，逐步深入到类的定义、属性、方法以及实例化的过程。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_5.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_7.png)

## 面向过程与面向对象

上一节我们介绍了函数，本节中我们来看看编程的两种主要思想：面向过程和面向对象。

**面向过程**是一种线性的编程思想。它关注的是解决问题的步骤，就像一份详细的食谱。例如，建造一栋房子的过程可以分解为：
*   第一步：挖地基。
*   第二步：垒砖墙。
*   第三步：安装窗户。
*   第四步：进行装修。
*   第五步：购买家具。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_9.png)

我们之前编写的代码大多是面向过程的，代码从上到下、从左到右依次执行。这种方式思路清晰，效率高，但在开发大型、复杂的项目时，多人协作会变得困难。

**面向对象**则是一种模块化的编程思想。它关注的是参与解决问题的“对象”以及它们各自的功能。还是以建房子为例，面向对象的方式是：
*   我（项目经理）需要找一个会挖地基的工人（对象A）。
*   再找一个会垒墙的工人（对象B）。
*   再找一个会安装窗户的工人（对象C）。
*   再找一个会装修的工人（对象D）。

我只需要协调这些对象（工人）的工作顺序，而每个对象内部如何完成其特定任务（如何挖地基、如何垒墙）则由他们自己负责。这种方式更利于大型项目的分工与协作。在Python中，字符串、列表、乃至我们自定义的“人”、“汽车”等，都可以被视为对象。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_11.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_13.png)

简单来说，**面向过程关注“怎么做”，面向对象关注“谁来做”**。面向对象编程让我们能够更好地抽象现实世界，构建出由多个具备特定功能的对象组合而成的程序。

## 类的定义与基本结构

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_15.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_17.png)

理解了面向对象的思想后，我们开始学习如何用代码来定义“一类”事物，这就是**类**。类是一个抽象的蓝图，它总结了一类事物的共同特征（属性）和行为（方法）。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_19.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_21.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_23.png)

例如，“人类”具有一些共同特征：两条腿、两只胳膊等；也具有一些共同行为：说话、吃饭、睡觉等。我们可以用类来抽象描述“人类”。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_25.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_27.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_29.png)

以下是定义一个简单类的方式：

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_31.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_33.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_35.png)

```python
class Person:
    pass
```

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_37.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_39.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_41.png)

*   `class` 是定义类的关键字，类似于用 `def` 定义函数。
*   `Person` 是类名。在Python中，类名通常采用**驼峰命名法**（每个单词首字母大写，例如 `PersonDetail`）。
*   `pass` 是一个占位符，表示这个类暂时为空。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_43.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_45.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_47.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_49.png)

一个完整的类可以包含**属性**（变量）和**方法**（函数）。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_51.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_53.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_55.png)

```python
class Person:
    # 类属性 (属于类本身的变量)
    leg_number = 2
    arm_number = 2

    # 实例方法 (第一个参数必须是self)
    def speak(self):
        print("正在说话")

    def eat(self):
        print("正在吃饭")

    def sleep(self):
        print("正在睡觉")
```

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_57.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_59.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_61.png)

*   **类属性**：直接在类中定义的变量，如 `leg_number = 2`。它属于类本身，被所有该类的实例共享。
*   **实例方法**：在类中定义的函数。它的第一个参数必须是 `self`，`self` 代表类的**实例对象**本身。通过 `self`，实例方法可以访问和修改该实例的属性。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_63.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_65.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_67.png)

我们可以通过类名直接访问或修改类属性：

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_69.png)

```python
print(Person.leg_number)  # 输出: 2
Person.leg_number = 3
print(Person.leg_number)  # 输出: 3
```

但是，不能直接用类名调用实例方法（会报错），因为缺少 `self` 参数。实例方法需要通过类的**实例**来调用。

## 实例化与self参数

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_71.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_73.png)

类只是一个抽象的模板。要使用它，我们需要根据这个模板创建出具体的“对象”，这个过程叫做**实例化**。这就像根据手机图纸（类）生产出一台台具体的手机（实例）。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_75.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_77.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_79.png)

实例化的语法很简单：

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_81.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_83.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_85.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_87.png)

```python
p1 = Person()  # 创建Person类的一个实例，命名为p1
p2 = Person()  # 创建另一个实例，命名为p2
```

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_89.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_91.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_93.png)

现在，`p1` 和 `p2` 就是两个独立的人（对象）。我们可以用它们来调用实例方法：

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_95.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_97.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_99.png)

```python
p1.speak()  # 输出: 正在说话
p2.eat()    # 输出: 正在吃饭
```

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_101.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_103.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_105.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_107.png)

当 `p1.speak()` 被调用时，Python会自动将 `p1` 作为 `self` 参数传入 `speak` 方法。因此，在方法内部，`self` 就指向了调用它的那个实例（这里是 `p1`）。如果换成 `p2.speak()`，那么 `self` 就指向 `p2`。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_109.png)

**理解 `self` 是理解类的关键**。它确保了每个实例方法都能正确地操作其所属实例的数据。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_111.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_113.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_115.png)

## 实例属性与 `__init__` 方法

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_117.png)

每个实例除了共享类属性，还可以拥有自己独特的属性，这称为**实例属性**。实例属性通常在 `__init__` 方法中定义。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_119.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_121.png)

`__init__` 是一个特殊的实例方法，称为**构造方法**或**初始化方法**。它在实例被创建时**自动执行**，主要作用是为新创建的实例设置初始属性（传参和初始化）。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_123.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_125.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_127.png)

```python
class Person:
    def __init__(self, name, age):
        # 初始化实例属性
        self.name = name  # self.name 是实例属性
        self.age = age    # self.age 是实例属性
        print(f"{self.name}被创建了")

    def speak(self):
        print(f"{self.name}正在说话")

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_129.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_131.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_133.png)

# 实例化时，参数会传递给 __init__ 方法
p1 = Person("小明", 18)  # 输出: 小明被创建了
p2 = Person("小红", 20)  # 输出: 小红被创建了

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_135.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_137.png)

p1.speak()  # 输出: 小明正在说话
p2.speak()  # 输出: 小红正在说话

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_139.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_141.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_143.png)

print(p1.age)  # 输出: 18
print(p2.name) # 输出: 小红
```

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_145.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_147.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_149.png)

*   在 `__init__(self, name, age)` 中，`name` 和 `age` 是传入的参数。
*   `self.name = name` 这行代码创建了一个名为 `name` 的实例属性，并将其值设置为传入的 `name` 参数。`self.age` 同理。
*   这样，每个 `Person` 实例（`p1`, `p2`）都拥有自己独立的 `name` 和 `age`。
*   实例方法（如 `speak`）可以通过 `self.name` 来访问该实例自己的名字。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_151.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_153.png)

**实例属性 vs 类属性**：
*   修改类属性会影响所有实例看到的该属性值（除非实例有自己的同名属性覆盖它）。
*   修改一个实例的属性（如 `p1.age = 25`）只会影响该实例本身。
*   通常，像“姓名”、“年龄”这种因人而异的特征，应该定义为实例属性。像“人类腿的数量”这种通用特征，可以定义为类属性。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_155.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_157.png)

## 类变量与实例变量的作用域

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_159.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_161.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_163.png)

在类中，变量的作用域规则与函数中类似，但多了 `self` 的维度。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_165.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_167.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_169.png)

*   **类变量**：在类内部、但不在任何方法内部定义的变量。它们属于类，可通过 `类名.变量名` 或 `实例.变量名` 访问（后者可能被实例变量覆盖）。
*   **实例变量**：以 `self.变量名` 形式定义的变量（通常在 `__init__` 中）。它们属于具体的实例，只能通过 `实例.变量名` 访问。
*   **局部变量**：在类的方法内部定义，但没有 `self.` 前缀的变量。它们的作用域仅限于该方法内部，其他方法无法访问。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_171.png)

```python
class Person:
    class_var = “我是类变量”  # 类变量

    def __init__(self, value):
        self.instance_var = value  # 实例变量
        local_var = “我是局部变量” # 局部变量，只能在 __init__ 内使用
        # print(local_var) # 这里可以打印

    def another_method(self):
        # print(local_var) # 这里会报错，访问不到
        print(self.instance_var) # 可以访问实例变量
        print(Person.class_var)  # 可以访问类变量

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_173.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_175.png)

p = Person(“实例的值”)
print(p.instance_var)  # 正确
print(Person.class_var) # 正确
# print(local_var)      # 错误
```

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_177.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_179.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_181.png)

## 魔术方法简介

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_183.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_184.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_186.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_188.png)

在Python类中，以双下划线 `__` 开头和结尾的方法被称为**魔术方法**（或特殊方法）。它们会在特定的时机被Python自动调用，赋予类强大的功能。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_190.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_191.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_193.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_195.png)

我们已经认识了 `__init__`。这里再简单介绍两个：

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_197.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_199.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_201.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_203.png)

*   `__str__`: 当使用 `print(实例)` 或 `str(实例)` 时，会自动调用此方法，返回一个易读的字符串描述。

    ```python
    class Person:
        def __init__(self, name):
            self.name = name
        def __str__(self):
            return f“Person对象，名字是：{self.name}”

    p = Person(“张三”)
    print(p)  # 输出: Person对象，名字是：张三
    ```

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_205.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_207.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_209.png)

*   `__getattr__`: 当试图访问一个不存在的实例属性时，会被调用。可以用来实现动态属性或错误处理。

    ```python
    class Person:
        def __getattr__(self, item):
            print(f“正在访问不存在的属性：{item}”)
            return “默认值”

    p = Person()
    print(p.xxx)  # 输出: 正在访问不存在的属性：xxx \n 默认值
    ```

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_211.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_213.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_215.png)

魔术方法还有很多，如 `__len__`, `__call__` 等，它们让自定义的类对象能够像内置类型（如列表、字符串）一样使用Python的语法和操作。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_217.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_219.png)

## 总结

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_221.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_223.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_225.png)

本节课中我们一起学习了Python类的核心概念：

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_227.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_229.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_231.png)

1.  **编程思想**：理解了**面向过程**（关注步骤）与**面向对象**（关注对象与交互）的区别。
2.  **类的定义**：使用 `class` 关键字定义类，类名采用驼峰命名法。类由**属性**（数据）和**方法**（函数）构成。
3.  **实例化**：通过 `类名()` 创建类的具体对象，称为实例化。
4.  **`self` 参数**：实例方法的第一个参数，代表调用该方法的实例对象本身，是访问实例属性和其他方法的关键。
5.  **`__init__` 方法**：构造方法，在实例化时自动执行，用于初始化实例属性。
6.  **属性类型**：区分了**类属性**（属于类，所有实例共享）和**实例属性**（属于单个实例，在 `__init__` 中用 `self.` 定义）。
7.  **魔术方法**：认识了以 `__` 包围的特殊方法，如 `__init__`, `__str__`，它们由Python在特定场景自动调用。

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_233.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_235.png)

![](img/d5a4fa75835b9c28e2d3f079b9eaf146_236.png)

掌握这些基础是进入面向对象编程世界的第一步。在后续课程中，我们将学习更高级的主题，如继承、多态、类方法、静态方法等，以构建更强大、更灵活的代码结构。