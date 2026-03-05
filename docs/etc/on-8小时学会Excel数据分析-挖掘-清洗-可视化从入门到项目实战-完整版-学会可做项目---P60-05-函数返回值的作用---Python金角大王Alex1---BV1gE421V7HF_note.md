# Python数据分析实战：P60：05 函数返回值的作用

![](img/13b0d6cd4f5b53b357509b0cb33a70ba_1.png)

在本节课中，我们将要学习Python函数中一个非常重要的概念——**返回值**。我们将了解它的作用、如何使用，以及它如何影响程序的执行流程。

## 概述

函数外部的代码在调用函数时，有时需要获取函数内部的执行结果。例如，一个注册函数执行后，外部代码需要知道注册是成功还是失败。为了实现这个目的，就需要在函数内部使用 `return` 语句将结果返回给调用者。

上一节我们介绍了函数的基本定义和调用，本节中我们来看看如何让函数“带回”信息。

## 返回值的作用

返回值的主要作用是将函数内部的执行结果传递到函数外部，使得调用者可以根据返回的结果来决定后续的操作。

### 返回执行状态

一个常见的应用是返回操作的执行状态（如成功或失败）。请看以下学生注册函数的例子：

```python
def student_registration(name, age, course):
    if age > 22:
        return False  # 年龄超标，注册失败
    else:
        # 这里可以执行写入数据库等操作
        return True   # 注册成功

# 调用函数并获取返回值
registration_status = student_registration("张三", 21, "Python数据分析")
print(f"注册状态: {registration_status}")

# 根据返回值决定后续流程
if registration_status:
    print("注册成功，欢迎入学！")
else:
    print("抱歉，您的年龄不符合要求。")
```

在这个例子中，函数 `student_registration` 根据输入的年龄进行判断。如果年龄大于22岁，则返回 `False`，表示注册失败；否则返回 `True`，表示注册成功。函数外部的代码通过接收这个返回值，就能知道注册是否成功，并执行相应的提示。

### 返回任意数据

返回值不限于布尔值（`True`/`False`），它可以返回任何类型的数据，例如字符串、数字、列表，甚至是多个值的组合。

```python
def get_student_info(name, age):
    welcome_msg = f"欢迎{name}同学！"
    student_id = 10000 + age  # 一个简单的学号生成逻辑
    return welcome_msg, student_id  # 返回一个包含两个元素的元组

![](img/13b0d6cd4f5b53b357509b0cb33a70ba_3.png)

# 调用函数
info = get_student_info("李四", 20)
print(f"返回的信息类型是: {type(info)}")
print(f"返回的信息是: {info}")
# 可以通过索引或解包来使用返回的多个值
message, id_num = info
print(message)
print(f"你的学号是: {id_num}")
```

当使用逗号分隔多个值返回时，Python会自动将它们打包成一个**元组（tuple）**。调用者接收到这个元组后，可以方便地取出其中的各个值进行使用。

### 不指定返回值

如果函数中没有写 `return` 语句，或者 `return` 后面没有跟任何值，函数会默认返回一个特殊值 `None`。

```python
def function_without_return():
    print("这个函数没有return语句。")

result = function_without_return()
print(f"函数的返回值是: {result}")
print(f"返回值是否等于None: {result is None}")
```

`None` 在Python中表示“空”或“无”，它常用于表示函数没有明确返回有意义的结果。

## return语句的另一个特点：结束函数执行

`return` 语句除了返回值，还有一个关键特性：**一旦执行到 `return`，函数会立即结束，其后的所有代码都不会再执行**。

```python
def test_return():
    print("这句话会执行。")
    return "函数到此结束"
    print("这句话永远不会执行。")  # 这行代码被忽略了

result = test_return()
print(result)
```

运行上面的代码，你会发现第二句 `print` 永远不会被执行。因为程序遇到 `return "函数到此结束"` 后，函数 `test_return` 就终止了，返回值被传递出去，函数内 `return` 之后的代码被跳过。

这个特性非常有用，它可以让我们在满足某些条件时提前退出函数，避免执行不必要的代码。

以下是关于 `return` 语句要点的总结：
1.  **返回结果**：使用 `return` 将数据传递到函数外部。
2.  **默认返回None**：函数没有 `return` 或 `return` 无值，则返回 `None`。
3.  **可返回多个值**：以元组形式返回。
4.  **终止函数**：`return` 会立即结束当前函数的执行。

## 总结

本节课中我们一起学习了Python函数**返回值**的核心概念。我们了解到，返回值是函数与外部代码沟通的桥梁，它允许函数将执行结果（无论是状态、数据还是多个信息）传递出去。同时，`return` 语句也充当了函数执行的“终止符”。理解并熟练运用返回值，是编写模块化、可交互代码的关键一步。

![](img/13b0d6cd4f5b53b357509b0cb33a70ba_5.png)

请大家尝试修改之前的函数练习，为它们添加合适的返回值，并在外部根据返回值进行不同的操作，以巩固对这个重要概念的理解。