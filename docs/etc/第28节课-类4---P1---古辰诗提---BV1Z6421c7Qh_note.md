# Python基础：第28课：类4 - 继承详解 🧬

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_1.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_3.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_5.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_7.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_9.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_10.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_12.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_14.png)

在本节课中，我们将深入学习面向对象编程中一个非常重要的概念——继承。我们将探讨如何让一个类（子类）获取另一个类（父类）的属性和方法，以及单继承、多继承、方法查找顺序和`super()`函数的使用。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_16.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_18.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_20.png)

---

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_22.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_23.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_24.png)

## 继承的基本概念

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_26.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_28.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_30.png)

继承是面向对象编程的核心特性之一。它允许我们定义一个类（子类）来继承另一个类（父类）的属性和方法，从而实现代码的复用和扩展。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_32.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_34.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_36.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_38.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_39.png)

在Python中，继承的语法非常简单。定义类时，在类名后的括号中指定要继承的父类即可。如果一个类没有显式地指定父类，那么它默认继承自`object`类。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_41.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_43.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_45.png)

```python
class Animal:
    def eat(self):
        print("正在吃饭")

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_47.png)

class Person(Animal):  # Person类继承自Animal类
    pass

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_49.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_51.png)

# 实例化Person类
p1 = Person()
p1.eat()  # 输出：正在吃饭
```

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_53.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_55.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_57.png)

在上面的例子中，`Person`类继承了`Animal`类的`eat`方法，因此其实例`p1`可以直接调用`eat`方法。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_59.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_61.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_63.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_65.png)

---

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_67.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_69.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_71.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_73.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_75.png)

## 查看类的父类与子类

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_77.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_79.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_81.png)

在编程过程中，有时我们需要查看一个类的继承关系。Python提供了内置属性来帮助我们。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_83.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_85.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_87.png)

*   **查看父类**：使用`__bases__`属性。它返回一个包含所有直接父类的元组。
*   **查看子类**：使用`__subclasses__()`方法。它返回一个包含所有直接子类的列表。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_89.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_91.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_93.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_95.png)

以下是具体用法：

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_97.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_99.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_101.png)

```python
print(Person.__bases__)        # 输出：(<class '__main__.Animal'>,)
print(Animal.__subclasses__()) # 输出：[<class '__main__.Person'>]
```

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_103.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_105.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_107.png)

需要注意的是，`__bases__`是类属性，而`__subclasses__()`是一个需要调用的方法。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_109.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_111.png)

---

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_113.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_115.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_117.png)

## 多继承与方法查找顺序

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_119.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_121.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_123.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_125.png)

Python支持多继承，即一个类可以同时继承多个父类。这通过在类定义时，在括号内用逗号分隔多个父类来实现。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_127.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_129.png)

```python
class Animal:
    def sleep(self):
        print("动物正在睡觉")

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_131.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_132.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_134.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_136.png)

class Person:
    def sleep(self):
        print("人类正在睡觉")

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_138.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_140.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_142.png)

class Student(Person, Animal):  # Student类同时继承Person和Animal
    pass

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_144.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_146.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_148.png)

s1 = Student()
s1.sleep()  # 输出：人类正在睡觉
```

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_150.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_152.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_154.png)

当子类调用一个方法时，Python解释器会按照特定的顺序在继承链中查找该方法。这个顺序被称为**方法解析顺序（MRO）**。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_156.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_158.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_160.png)

在上面的例子中，`Student`类继承了`Person`和`Animal`。当调用`s1.sleep()`时，解释器会：
1.  先在`Student`类自身中查找`sleep`方法。
2.  如果没有找到，则按照继承列表中从左到右的顺序查找，即先在`Person`类中查找。
3.  在`Person`类中找到了`sleep`方法，因此执行该方法，输出“人类正在睡觉”。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_162.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_163.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_165.png)

如果我们将继承顺序改为`class Student(Animal, Person)`，那么`s1.sleep()`将输出“动物正在睡觉”。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_167.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_169.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_171.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_173.png)

我们可以通过`类名.__mro__`属性或`类名.mro()`方法来查看一个类的MRO。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_175.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_177.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_179.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_181.png)

```python
print(Student.__mro__)
# 输出：(<class '__main__.Student'>, <class '__main__.Person'>, <class '__main__.Animal'>, <class 'object'>)
```

---

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_183.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_185.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_187.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_189.png)

## 构造函数 `__init__` 与 `super()` 函数

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_191.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_193.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_195.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_197.png)

在继承中，子类经常需要扩展或修改父类的行为，尤其是在初始化对象时。这时就需要用到构造函数`__init__`和`super()`函数。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_199.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_201.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_203.png)

### 构造函数的重写与调用

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_205.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_207.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_209.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_211.png)

当子类定义了`__init__`方法时，它会覆盖父类的`__init__`方法。如果子类还需要执行父类的初始化逻辑，就必须显式地调用父类的`__init__`方法。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_213.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_215.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_217.png)

**旧式写法（Python 2风格，Python 3中仍可用但不推荐）**：
```python
class Person:
    def __init__(self, name):
        self.name = name

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_219.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_221.png)

class Student(Person):
    def __init__(self, name, student_id):
        Person.__init__(self, name)  # 显式调用父类的__init__方法
        self.student_id = student_id
```

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_223.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_225.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_227.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_229.png)

**新式写法（Python 3推荐）**：
使用`super()`函数可以更优雅、更安全地调用父类的方法。`super()`返回一个代理对象，它会根据MRO自动找到下一个要调用的父类方法。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_231.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_233.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_235.png)

```python
class Person:
    def __init__(self, name):
        self.name = name
        print(f"执行Person的构造函数，name={name}")

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_237.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_239.png)

class Student(Person):
    def __init__(self, name, student_id):
        super().__init__(name)  # 等同于 super(Student, self).__init__(name)
        self.student_id = student_id
        print(f"执行Student的构造函数，student_id={student_id}")

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_241.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_243.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_245.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_247.png)

s1 = Student("小明", "S001")
# 输出：
# 执行Person的构造函数，name=小明
# 执行Student的构造函数，student_id=S001
```

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_249.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_251.png)

### `super()` 在多继承中的行为

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_253.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_255.png)

`super()`函数的行为在多继承中尤为重要。它并不是简单地调用“父类”，而是按照MRO链条，调用当前类的“下一个”类的方法。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_257.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_259.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_261.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_263.png)

考虑以下多层多继承的例子：

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_265.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_267.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_269.png)

```python
class Animal:
    def __init__(self):
        print("执行Animal的构造函数")

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_271.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_273.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_275.png)

class Person(Animal):
    def __init__(self, name):
        print(f"执行Person的构造函数，name={name}")
        super().__init__()  # 这里super()会找到Animal

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_277.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_279.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_281.png)

class Student(Person):
    def __init__(self, name, student_id):
        print(f"执行Student的构造函数，student_id={student_id}")
        super().__init__(name)  # 这里super()会找到Person

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_283.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_285.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_287.png)

s1 = Student("小红", "S002")
# 输出：
# 执行Student的构造函数，student_id=S002
# 执行Person的构造函数，name=小红
# 执行Animal的构造函数
```

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_289.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_291.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_293.png)

`super()`确保了初始化方法按照MRO顺序被依次调用，这对于正确初始化所有父类中的属性至关重要。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_295.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_297.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_299.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_301.png)

---

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_303.png)

## 关于 `if __name__ == "__main__":` 的补充说明

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_305.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_307.png)

在课程中提到了`if __name__ == "__main__":`这行代码。这是一个在Python模块中常见的写法，用于判断当前模块是作为主程序运行，还是被导入到其他模块中。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_309.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_311.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_313.png)

*   `__name__`是一个内置变量。当一个模块被直接运行时，其`__name__`值被设置为`"__main__"`。
*   当一个模块被导入到其他模块时，其`__name__`值是其模块名（文件名）。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_315.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_317.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_319.png)

这个判断的主要作用是：**将模块中的测试代码或执行代码与可被导入的功能代码分离**。这样，当模块被导入时，测试代码不会自动执行，只有在直接运行该模块时，测试代码才会运行。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_321.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_323.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_325.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_327.png)

```python
# 文件：my_module.py
def add(a, b):
    return a + b

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_329.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_331.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_333.png)

# 测试代码
if __name__ == "__main__":
    # 只有当直接运行 my_module.py 时，下面的代码才会执行
    print(add(3, 5))  # 输出：8
    print("模块测试完成")
```

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_335.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_337.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_339.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_341.png)

---

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_343.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_345.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_347.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_349.png)

## 总结

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_351.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_353.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_355.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_357.png)

本节课我们一起深入学习了Python中的继承机制。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_359.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_361.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_363.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_365.png)

我们首先了解了继承的基本概念和语法，知道了如何让子类复用父类的功能。接着，我们学习了如何查看类的父类（`__bases__`）和子类（`__subclasses__()`）。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_367.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_369.png)

然后，我们探讨了Python强大的多继承特性，并理解了**方法解析顺序（MRO）** 决定了属性和方法的查找路径。这是理解多继承代码行为的关键。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_371.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_372.png)

最后，我们重点讲解了在继承体系中如何正确地初始化和扩展类，核心是**构造函数`__init__`** 和 **`super()`函数**。`super()`能够根据MRO自动、安全地调用父类方法，是在新式类中处理继承关系的推荐方式。我们还通过一个补充说明，了解了`if __name__ == "__main__":`的用途，它有助于我们编写更清晰、更模块化的代码。

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_374.png)

![](img/300c5bb36b2bb05c856e49ab5ba78e7a_376.png)

理解这些概念，尤其是MRO和`super()`的工作原理，将帮助你更好地设计和理解复杂的面向对象程序。