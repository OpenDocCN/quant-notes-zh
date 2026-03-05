# 量化交易零基础：第二十课：Python函数详解 🧮

![](img/aa60b029b21aa3cf23292a733d6b82e4_1.png)

在本节课中，我们将深入学习Python编程中一个极其重要的概念——函数。函数是组织代码、实现代码复用和模块化开发的核心工具。我们将从最简单的函数定义开始，逐步讲解带参数、返回值、默认参数、可变参数等高级用法，并介绍递归和高阶函数的概念。

## 函数的定义与调用

上一节我们介绍了分支与循环，它们是控制程序流程的基础。本节中，我们来看看如何将一系列操作封装成可重复使用的代码块，即函数。

函数通过 `def` 关键字定义，后跟函数名、括号和冒号。函数体内的代码需要缩进。

```python
def say_hello():
    print("Hello")
```

定义函数后，它不会自动执行。需要通过函数名加括号来调用它。

```python
say_hello()  # 输出：Hello
```

## 带返回值的函数

许多函数不仅执行操作，还会进行计算并返回一个结果。使用 `return` 语句可以将结果返回给调用者。

```python
def add():
    a = 1
    b = 2
    return a + b

result = add()
print(result)  # 输出：3
```

函数内部定义的变量（如 `a`, `b`）是局部变量，函数执行完毕后会自动销毁，只有 `return` 返回的值能被外部获取。

## 带参数的函数

函数可以接收外部传入的数据，这些数据称为参数。定义函数时，需要在括号内指定参数名。

```python
def multiply(a, b):
    return a * b

product = multiply(3, 5)
print(product)  # 输出：15
```

调用函数时，传入参数的顺序必须与定义时一致。

## 多返回值与参数忽略

![](img/aa60b029b21aa3cf23292a733d6b82e4_3.png)

Python函数的一个特点是可以返回多个值。调用时可以用多个变量接收。

![](img/aa60b029b21aa3cf23292a733d6b82e4_5.png)

```python
def return_multi():
    a = 1
    b = 2
    c = 3
    return a, b, c

aa, bb, cc = return_multi()
print(aa, bb, cc)  # 输出：1 2 3
```

如果不想接收某个返回值，可以使用下划线 `_` 占位忽略。

![](img/aa60b029b21aa3cf23292a733d6b82e4_7.png)

```python
aa, _, cc = return_multi()
print(aa, cc)  # 输出：1 3
```

## 默认参数与关键字参数

可以为函数参数指定默认值，调用时若不传递该参数，则使用默认值。

```python
def divide(a, b=2):
    return a / b

![](img/aa60b029b21aa3cf23292a733d6b82e4_9.png)

print(divide(4))     # 输出：2.0， b使用默认值2
print(divide(3, 1))  # 输出：3.0
```

调用函数时，可以通过参数名指定值，这称为关键字参数，此时参数的顺序可以改变。

```python
print(divide(b=1, a=3))  # 输出：3.0
```

![](img/aa60b029b21aa3cf23292a733d6b82e4_11.png)

## 递归函数

递归是指函数在其定义中调用自身。递归必须有一个明确的终止条件（递归头），否则会导致无限循环。

以下是计算阶乘的递归函数示例：
```python
def factorial(n):
    if n == 0 or n == 1:  # 递归头
        return 1
    else:
        return n * factorial(n - 1)  # 递归调用自身

print(factorial(4))  # 输出：24 (4*3*2*1)
```

![](img/aa60b029b21aa3cf23292a733d6b82e4_13.png)

## 可变参数

有时我们不确定函数会接收多少个参数。使用 `*args` 可以接收任意数量的位置参数，它们会被打包成一个元组。

```python
def sum_all(*args):
    print(type(args))  # 输出：<class 'tuple'>
    total = 0
    for num in args:
        total += num
    return total

print(sum_all(1, 2, 3))    # 输出：6
print(sum_all(10, 20, 30, 40, 50))  # 输出：150
```

![](img/aa60b029b21aa3cf23292a733d6b82e4_15.png)

## 任意关键字参数

使用 `**kwargs` 可以接收任意数量的关键字参数，它们会被打包成一个字典。

```python
def print_info(**kwargs):
    print(type(kwargs))  # 输出：<class 'dict'>
    for key, value in kwargs.items():
        print(f"{key}: {value}")

print_info(name="张三", age=30, city="北京")
# 输出：
# name: 张三
# age: 30
# city: 北京
```

![](img/aa60b029b21aa3cf23292a733d6b82e4_17.png)

## 高阶函数

![](img/aa60b029b21aa3cf23292a733d6b82e4_19.png)

高阶函数是指将其他函数作为参数或返回值的函数。这体现了Python中“函数是一等公民”的特性。

![](img/aa60b029b21aa3cf23292a733d6b82e4_21.png)

```python
def square(x):
    return x * x

def double(x):
    return x * 2

def apply_func(func, value):
    return func(value)

print(apply_func(square, 3))  # 输出：9 (3的平方)
print(apply_func(double, 3))  # 输出：6 (3的2倍)
```

---

![](img/aa60b029b21aa3cf23292a733d6b82e4_23.png)

本节课中我们一起学习了Python函数的全面知识。我们从最简单的无参函数开始，逐步掌握了如何定义带参数、返回值的函数，并探讨了默认参数、多返回值、递归、可变参数和高阶函数等高级概念。函数是构建复杂程序的基础，理解并熟练运用它们对后续学习量化交易策略的编程实现至关重要。请务必多加练习，巩固这些核心概念。