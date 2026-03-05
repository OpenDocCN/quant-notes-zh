# Python函数参数详解：04：关键参数与非固定参数 🧩

![](img/b6b2fa16c75f5caf5f9e5733cca527ca_1.png)

在本节课中，我们将要学习Python函数中两种重要的参数类型：关键参数和非固定参数。理解它们将帮助你编写更灵活、更强大的函数。

![](img/b6b2fa16c75f5caf5f9e5733cca527ca_3.png)

## 概述

![](img/b6b2fa16c75f5caf5f9e5733cca527ca_5.png)

上一节我们介绍了位置参数和默认参数。本节中，我们来看看如何通过指定参数名来传递值（关键参数），以及如何定义可以接收任意数量参数的函数（非固定参数）。

## 关键参数（指定参数）

正常调用函数时，我们按照参数定义的位置顺序传递值，这被称为**位置参数**。例如：
```python
def register(name, age, course='PY', country='CN'):
    print(name, age, course, country)

![](img/b6b2fa16c75f5caf5f9e5733cca527ca_7.png)

register('王山炮', 22, 'AI', 'US')  # 按位置一一对应
```

但有时我们不想按照固定顺序赋值。这时可以使用**关键参数**（或称指定参数），即在调用时明确指定参数名进行赋值。
```python
register('王山炮', course='PY', age=25)  # 使用关键参数指定 age 和 course
```
通过参数名指定，就不再依赖位置顺序。

**关键规则**：关键参数必须放在所有位置参数之后。位置参数的优先级高于关键参数。如果顺序错误，解释器会产生歧义并报错。
```python
# 错误示例：位置参数在关键参数之后
register(course='PY', '王山炮', 25)  # 这将导致 SyntaxError
```
此外，不能对同一个参数既使用位置参数又使用关键参数进行重复赋值。
```python
# 错误示例：对 age 参数重复赋值
register('王山炮', 22, age=25)  # 报错：got multiple values for argument 'age'
```

![](img/b6b2fa16c75f5caf5f9e5733cca527ca_9.png)

以下是关键参数的使用要点总结：
*   关键参数通过在调用时指定 `参数名=值` 的形式使用。
*   关键参数必须位于所有位置参数之后。
*   同一个参数不能被赋值两次。

![](img/b6b2fa16c75f5caf5f9e5733cca527ca_11.png)

## 非固定参数

![](img/b6b2fa16c75f5caf5f9e5733cca527ca_13.png)

在程序设计初期，我们可能无法预知函数未来需要接收多少个参数。为了提供扩展性，避免频繁修改源代码，Python提供了**非固定参数**。

非固定参数主要有两种：`*args` 和 `**kwargs`。

![](img/b6b2fa16c75f5caf5f9e5733cca527ca_15.png)

![](img/b6b2fa16c75f5caf5f9e5733cca527ca_17.png)

### 1. `*args` 非固定位置参数

在形参前加一个星号 `*`，例如 `*args`，可以将调用时传入的多个位置参数收集到一个**元组**中。
```python
def register(name, *args):
    print(name)
    print(args)  # args 是一个元组

register('Alex', 'male', 'coding', '136xxxxxxx')
# 输出：
# Alex
# ('male', 'coding', '136xxxxxxx')
```
你可以传入任意数量的额外参数，它们都会被 `args` 元组接收。在函数内部，可以像操作元组一样使用 `args`，例如通过索引 `args[0]` 访问第一个额外参数。

### 2. `**kwargs` 非固定关键字参数

在形参前加两个星号 `**`，例如 `**kwargs`，可以将调用时传入的多个关键字参数（关键参数）收集到一个**字典**中。
```python
def register(name, **kwargs):
    print(name)
    print(kwargs)  # kwargs 是一个字典

register('Alex', hometown='Shandong', hobby='reading')
# 输出：
# Alex
# {'hometown': 'Shandong', 'hobby': 'reading'}
```
传入的 `键=值` 对会成为字典 `kwargs` 的键值对。在函数内部，可以使用字典的方法来操作这些参数，例如 `kwargs.get('hometown')`。

### 组合使用

`*args` 和 `**kwargs` 经常组合使用，使函数能够接收任意形式和数量的参数，这在很多Python库和框架中非常常见。
```python
def flexible_func(name, *args, **kwargs):
    print(f"Name: {name}")
    print(f"Extra args: {args}")
    print(f"Extra kwargs: {kwargs}")

flexible_func('Alex', 'arg1', 'arg2', key1='value1', key2='value2')
```

**注意**：`args` 和 `kwargs` 只是约定俗成的变量名，你可以使用其他名字，但使用通用名称能使代码更易被他人理解。

## 练习

请定义一个函数，使其能够处理以下调用，并打印出指定的结果。
```python
# 你的函数定义
def exercise_func(*args, **kwargs):
    # 你的代码 here
    pass

# 调用
exercise_func('Hello', 'World', name='Alex', age=25, city='Beijing')
# 期望打印结果：
# args: ('Hello', 'World')
# kwargs: {'name': 'Alex', 'age': 25, 'city': 'Beijing'}
# Name from kwargs: Alex
```
提示：在函数内部，你需要分别打印 `args` 元组和 `kwargs` 字典，并从 `kwargs` 字典中取出 `name` 对应的值进行打印。可以使用 `kwargs.get('name')` 来安全地获取值。

## 总结

本节课中我们一起学习了Python函数的另外两种参数机制：
1.  **关键参数**：允许在调用函数时通过参数名指定值，提高了代码的可读性和灵活性，但必须遵循“位置参数优先”的规则。
2.  **非固定参数**：
    *   `*args`：将多余的位置参数打包成元组。
    *   `**kwargs`：将多余的关键字参数打包成字典。
    它们为函数提供了强大的扩展能力，使其能够适应未来可能增加的参数需求。

![](img/b6b2fa16c75f5caf5f9e5733cca527ca_19.png)

理解位置参数、默认参数、关键参数和非固定参数的区别与用法，是掌握Python函数定义与调用的关键。无需死记硬背，多练习即可熟练运用。