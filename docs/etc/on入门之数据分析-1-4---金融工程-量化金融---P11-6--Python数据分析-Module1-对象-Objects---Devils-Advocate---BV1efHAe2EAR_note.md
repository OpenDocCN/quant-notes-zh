# 量化交易：Python入门之数据分析【1/4】：对象与类 🏫

![](img/9b149dd1c4929e33a4fe6be702c58f02_0.png)

在本节课中，我们将要学习Python中一个核心概念：**对象（Object）** 和**类（Class）**。它们是面向对象编程的基础，能帮助我们更好地组织和管理复杂的代码，尤其是在构建量化交易模型时非常有用。

![](img/9b149dd1c4929e33a4fe6be702c58f02_2.png)

---

![](img/9b149dd1c4929e33a4fe6be702c58f02_4.png)

## 什么是对象与类？

![](img/9b149dd1c4929e33a4fe6be702c58f02_6.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_8.png)

上一节我们介绍了函数等基础概念，本节中我们来看看如何用“类”来创建更复杂的数据结构。在Python中，**类**就像一个创建对象的蓝图或模板。你可以把类想象成一个学校的通用设计图，而**对象**则是根据这张图纸建造出来的一所所具体的学校。

### 定义一个类

以下是定义一个类的基本语法。我们以“学校”为例：

```python
class School:
    department = "Music"  # 这是一个类属性，所有学校对象都共享

    def set_name(self, new_name):
        self.name = new_name  # self.name 是这个学校对象特有的属性

    def set_location(self, new_location):
        self.location = new_location

    def set_principal(self, new_principal):
        self.principal = new_principal
```

**代码解释**：
*   `class School:`：这行代码定义了一个名为 `School` 的类。
*   `department = "Music"`：这是一个**类属性**。它属于这个类本身，所有由这个类创建的对象（学校）都默认拥有一个音乐系。
*   `def set_name(self, new_name):`：这是一个**方法**（类中的函数）。`self` 是一个特殊参数，代表对象自身。通过 `self.name = new_name`，我们为这个特定的学校对象设置了一个名字属性。

![](img/9b149dd1c4929e33a4fe6be702c58f02_10.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_11.png)

### 创建和使用对象

![](img/9b149dd1c4929e33a4fe6be702c58f02_12.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_13.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_15.png)

定义了类之后，我们就可以用它来创建具体的对象了。

![](img/9b149dd1c4929e33a4fe6be702c58f02_17.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_19.png)

```python
# 创建第一所学校对象
school1 = School()
school1.set_name("Alice Wang")
school1.set_location("Shanghai")
school1.set_principal("Elon Musk")

![](img/9b149dd1c4929e33a4fe6be702c58f02_21.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_23.png)

# 创建第二所学校对象
school2 = School()
school2.set_name("Christine Li")
school2.set_location("Beijing")
school2.set_principal("George Mask")
```

![](img/9b149dd1c4929e33a4fe6be702c58f02_25.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_26.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_28.png)

现在，我们有了两所独立的学校：
*   `school1` 在上海，老师是 Alice Wang，校长是 Elon Musk。
*   `school2` 在北京，老师是 Christine Li，校长是 George Mask。

![](img/9b149dd1c4929e33a4fe6be702c58f02_30.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_31.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_33.png)

它们都共享 `department = "Music"` 这个属性，但拥有各自不同的 `name`、`location` 和 `principal`。

![](img/9b149dd1c4929e33a4fe6be702c58f02_35.png)

### 访问对象的属性

![](img/9b149dd1c4929e33a4fe6be702c58f02_37.png)

创建对象后，我们可以使用点号 `.` 来访问它的属性和方法。

![](img/9b149dd1c4929e33a4fe6be702c58f02_39.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_41.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_42.png)

```python
print(school1.department)   # 输出: Music
print(school1.name)         # 输出: Alice Wang
print(school2.location)     # 输出: Beijing
```

![](img/9b149dd1c4929e33a4fe6be702c58f02_44.png)

---

![](img/9b149dd1c4929e33a4fe6be702c58f02_46.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_48.png)

## 为什么使用类？

![](img/9b149dd1c4929e33a4fe6be702c58f02_50.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_52.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_53.png)

你可能会问，用几个独立的变量也能存储这些信息，为什么要用类呢？以下是使用类的主要优势：

![](img/9b149dd1c4929e33a4fe6be702c58f02_55.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_57.png)

1.  **组织性**：类将相关的数据（属性）和操作（方法）捆绑在一起，使代码结构更清晰。例如，所有关于“学校”的操作都集中在 `School` 类里。
2.  **复用性**：一个类定义好后，可以轻松创建多个具有相同结构但不同数据的对象（如 `school1`, `school2`）。
3.  **模块化**：在大型项目中（如量化交易系统），类可以帮助我们管理复杂的代码。你可以将不同的功能模块（如数据获取、策略分析、风险控制）分别封装成类，使开发和维护更容易。

![](img/9b149dd1c4929e33a4fe6be702c58f02_59.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_60.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_62.png)

想象一下Python内置的 `datetime` 模块，它就是一个包含了许多类（如 `date`, `datetime`, `timedelta`）的模块。我们通过 `from datetime import datetime` 导入后，就可以使用 `datetime.now()` 等方法，这正是类的强大之处。

![](img/9b149dd1c4929e33a4fe6be702c58f02_64.png)

---

## 总结

![](img/9b149dd1c4929e33a4fe6be702c58f02_66.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_67.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_68.png)

本节课中我们一起学习了Python中对象与类的核心概念：
*   **类（Class）** 是创建对象的模板，定义了对象将拥有哪些属性（数据）和方法（函数）。
*   **对象（Object）** 是根据类创建出来的具体实例，每个对象都拥有类中定义的属性和方法，但可以保存不同的数据。
*   使用 `class` 关键字定义类，在类中使用 `self` 关键字来访问和设置对象自身的属性。
*   类的使用提高了代码的组织性、复用性和模块化程度，是构建复杂程序（包括量化交易策略）的重要工具。

![](img/9b149dd1c4929e33a4fe6be702c58f02_70.png)

![](img/9b149dd1c4929e33a4fe6be702c58f02_72.png)

理解对象和类是迈向中级Python编程的关键一步。在接下来的课程中，我们将看到如何利用这些概念来处理和分析金融数据。