# Python数据分析：P64：09 常用内置函数2 🧰

在本节课中，我们将继续学习Python中几个非常实用的内置函数。上一节我们介绍了一些基础的内置函数，本节中我们来看看`enumerate`、`round`、`zip`、`filter`等函数，它们能帮助我们更高效地处理数据。

## 概述
本节课我们将学习`ord`、`enumerate`、`round`、`str`、`type`、`zip`和`filter`这几个内置函数。理解这些函数对于编写简洁、高效的Python代码至关重要。

---

## `ord` 函数
`ord`函数用于获取一个字符对应的ASCII码（十进制数字）。它与`chr`函数功能相反。

**公式/代码描述：**
```python
ord('A')  # 返回 65
```

![](img/9d6231a075689309764fe0f261d6b078_1.png)

---

## `enumerate` 函数
`enumerate`函数用于将一个可迭代对象（如列表）组合为一个索引序列，同时列出数据和数据下标。

以下是`enumerate`函数的基本用法：
```python
for index, value in enumerate(['a', 'b', 'c']):
    print(index, value)
```
执行上述代码会输出索引和对应的值，例如`(0, 'a')`、`(1, 'b')`等。

![](img/9d6231a075689309764fe0f261d6b078_3.png)

---

## `round` 函数
`round`函数用于对浮点数进行四舍五入，可以指定保留的小数位数。

**公式/代码描述：**
```python
round(3.14159)      # 返回 3
round(3.14159, 2)   # 返回 3.14
```

---

## `str` 和 `type` 函数
`str`函数用于将其他数据类型转换为字符串。`type`函数用于查看一个对象的数据类型。

![](img/9d6231a075689309764fe0f261d6b078_5.png)

![](img/9d6231a075689309764fe0f261d6b078_7.png)

**公式/代码描述：**
```python
str(123)           # 返回字符串 '123'
type('hello')      # 返回 <class 'str'>
```
在Python中，一切皆对象。`type`函数返回的`<class '...'>`表示该对象所属的类，这涉及到面向对象编程的概念。例如，字符串属于`str`类，列表属于`list`类，因此它们各自拥有不同的属性和方法。

![](img/9d6231a075689309764fe0f261d6b078_9.png)

---

![](img/9d6231a075689309764fe0f261d6b078_11.png)

## `zip` 函数
`zip`函数像拉链一样，将多个可迭代对象中对应的元素“配对”打包成一个个元组。

![](img/9d6231a075689309764fe0f261d6b078_13.png)

以下是`zip`函数的基本用法：
```python
boys = ['Alex', 'Jack', 'Rain']
girls = ['Black Girl', 'Rachel', 'CC']
for pair in zip(boys, girls):
    print(pair)
```
执行结果类似于`('Alex', 'Black Girl')`。在Python 3中，如果可迭代对象的长度不一致，`zip`会以最短的那个为准，超出部分不显示。

![](img/9d6231a075689309764fe0f261d6b078_15.png)

---

## `filter` 函数
`filter`函数用于过滤序列。它接收一个函数和一个可迭代对象，将后者的每个元素传入函数进行判断，保留返回值为`True`的元素。

![](img/9d6231a075689309764fe0f261d6b078_17.png)

![](img/9d6231a075689309764fe0f261d6b078_19.png)

**公式/代码描述：**
```python
def greater_than_five(x):
    return x > 5

![](img/9d6231a075689309764fe0f261d6b078_21.png)

numbers = list(range(10))
result = filter(greater_than_five, numbers)
print(list(result))  # 输出 [6, 7, 8, 9]
```
`filter`函数将列表`numbers`中的每个元素交给`greater_than_five`函数判断，只保留大于5的元素。

![](img/9d6231a075689309764fe0f261d6b078_23.png)

---

![](img/9d6231a075689309764fe0f261d6b078_25.png)

![](img/9d6231a075689309764fe0f261d6b078_27.png)

## 总结
本节课我们一起学习了多个Python内置函数：用于字符编码转换的`ord`，用于带索引迭代的`enumerate`，用于数值四舍五入的`round`，用于类型转换和查看的`str`与`type`，用于数据配对的`zip`，以及用于数据过滤的`filter`。掌握这些函数能让你在数据处理时更加得心应手。