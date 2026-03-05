# Python基础课：第30课：类6 - 元类与单例模式

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_0.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_2.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_4.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_6.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_8.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_10.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_12.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_14.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_16.png)

在本节课中，我们将要学习Python中两个高级但重要的概念：元类和单例模式。我们将了解如何使用`type()`函数动态创建类，探索`__new__`方法的作用，并学习如何通过元类来控制类的创建行为。这些知识将帮助你更深入地理解Python的面向对象机制。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_18.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_20.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_22.png)

## 使用type动态创建类

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_24.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_26.png)

上一节我们介绍了类的常规定义方式，本节中我们来看看如何使用`type()`函数在外部动态地创建一个类。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_28.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_30.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_32.png)

`type()`函数除了查看对象类型，还可以用于创建类。它接受三个参数：类名、父类的元组和包含属性的字典。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_34.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_36.png)

```python
# 使用type创建类
MyClass = type('MyClass', (), {'attribute': 'value'})
print(MyClass.__name__)  # 输出：MyClass
```

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_38.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_40.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_42.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_44.png)

以下是`type()`函数三个参数的详细说明：
*   **第一个参数**：类名，是一个字符串。这个名称可以与接收它的变量名不同。
*   **第二个参数**：父类的元组。如果只有一个父类，需要用逗号表示这是一个元组，例如`(BaseClass,)`。如果不写，则默认继承自`object`。
*   **第三个参数**：一个字典，用于存放类的属性和方法。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_46.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_48.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_50.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_51.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_53.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_55.png)

在字典中定义方法时需要注意，如果方法需要访问实例自身，必须显式地将实例作为第一个参数传入。通常更推荐在类实例化后，再动态地为实例添加方法。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_57.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_59.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_61.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_63.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_65.png)

```python
# 动态添加方法（推荐方式）
def eat(self):
    print(f"{self.name}在吃饭")

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_67.png)

obj = MyClass()
obj.name = "王总"
obj.eat = eat
obj.eat()  # 输出：王总在吃饭
```

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_69.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_71.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_73.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_75.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_77.png)

## Python的元类：type与object

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_79.png)

在深入元类之前，我们需要理解Python中两个最基础的类：`type`和`object`。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_81.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_83.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_84.png)

*   `type`是所有类型的元类，包括它自身。`type(type)`的结果仍然是`type`。
*   `object`是所有类的基类。`type`也是`object`的子类（`type.__bases__`包含`object`）。
*   同时，`object`本身的类型是`type`（`type(object)`的结果是`type`）。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_86.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_88.png)

它们的关系可以简单理解为：`type`是创建类的类（元类），而`object`是被创建的类的顶层父类。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_90.png)

## `__new__`方法与单例模式

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_92.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_93.png)

在实例化一个类时，Python首先调用的是`__new__`方法，然后才是`__init__`方法。`__new__`负责为对象分配内存空间，而`__init__`负责初始化对象。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_95.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_97.png)

`__new__`是一个类方法，它的第一个参数是`cls`（类本身），必须返回一个实例对象。通常我们不需要重写`__new__`，但在实现**单例模式**时非常有用。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_99.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_101.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_103.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_105.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_107.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_108.png)

单例模式确保一个类只有一个实例，并提供一个全局访问点。这在管理数据库连接、交易服务器连接等资源时非常实用。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_110.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_112.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_113.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_115.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_117.png)

以下是实现单例模式的一个示例：

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_119.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_121.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_123.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_125.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_127.png)

```python
class Singleton:
    _instance = None  # 类属性，用于保存唯一实例

    def __new__(cls, *args, **kwargs):
        # 判断类属性中是否已保存了实例
        if not hasattr(cls, '_instance'):
            # 如果没有，则调用父类的__new__创建实例，并保存
            cls._instance = super().__new__(cls)
        # 返回保存的实例
        return cls._instance

    def __init__(self, name):
        self.name = name

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_129.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_131.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_133.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_135.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_137.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_139.png)

# 测试
s1 = Singleton("实例1")
s2 = Singleton("实例2")
print(s1 is s2)  # 输出：True
print(s1.name, s2.name)  # 输出：实例2 实例2 (注意：后初始化的值会覆盖前者)
```

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_141.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_143.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_145.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_147.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_149.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_150.png)

## 自定义元类

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_152.png)

元类是类的类，它控制类的创建行为。当某些需求无法通过普通类继承实现时（例如，强制要求所有子类的类名必须以特定字母开头），就可以使用元类。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_154.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_155.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_157.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_159.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_161.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_163.png)

自定义元类需要继承`type`，并重写它的`__new__`方法。`type.__new__`接收四个参数：`cls`（元类自身）、`name`（类名）、`bases`（父类元组）、`attrs`（属性字典）。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_165.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_167.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_168.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_170.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_171.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_173.png)

下面是一个元类示例，它强制要求使用该元类的类的名称必须以大写字母“P”开头：

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_175.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_177.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_179.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_181.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_183.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_185.png)

```python
# 1. 定义元类
class MyMeta(type):
    def __new__(cls, name, bases, attrs):
        # 在类创建前进行检查
        if not name.startswith('P'):
            raise ValueError(f"类名 '{name}' 不是以 'P' 开头")
        # 调用父类type的__new__来实际创建类
        return super().__new__(cls, name, bases, attrs)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_187.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_188.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_190.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_192.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_194.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_196.png)

# 2. 使用元类
class Person(metaclass=MyMeta):
    def __init__(self, name):
        self.name = name
    def sing(self):
        print(f"{self.name}正在唱歌")

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_198.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_200.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_202.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_204.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_206.png)

# 尝试创建不以P开头的子类会报错
# class Man(Person):  # ValueError: 类名 'Man' 不是以 'P' 开头
#     pass

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_208.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_210.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_212.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_214.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_216.png)

# 创建以P开头的子类则成功
class PMan(Person):
    pass

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_218.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_220.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_222.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_224.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_226.png)

p = PMan("小张")
p.sing()  # 输出：小张正在唱歌
```

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_228.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_230.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_232.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_234.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_236.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_238.png)

其原理是，当Python解释器执行`class Person(metaclass=MyMeta):`这行代码时，会调用`MyMeta.__new__`来创建`Person`类。`PMan`继承`Person`，而`Person`的元类是`MyMeta`，因此创建`PMan`时同样会受到`MyMeta`规则的限制。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_240.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_242.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_244.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_246.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_248.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_250.png)

## 总结

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_252.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_254.png)

本节课中我们一起学习了：
1.  **动态创建类**：使用`type(类名, 父类元组, 属性字典)`可以在代码中动态生成类。
2.  **元类基础**：理解了`type`和`object`这两个Python内置的“元类”及其关系。
3.  **`__new__`方法**：知道了它在实例化时先于`__init__`执行，负责创建实例对象。
4.  **单例模式**：通过重写`__new__`方法，确保一个类在整个程序中只有一个实例。
5.  **自定义元类**：通过继承`type`并重写`__new__`方法，可以拦截类的创建过程，实现类级别的自定义逻辑（如命名规范检查）。

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_256.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_258.png)

![](img/d8909fd1ca34b5fae41c866c8cd2c3ba_260.png)

元类和单例模式属于Python的高级特性，在实际开发中并不常用，但理解它们有助于你更深刻地把握Python的面向对象设计思想，并在阅读复杂框架源码时能够游刃有余。