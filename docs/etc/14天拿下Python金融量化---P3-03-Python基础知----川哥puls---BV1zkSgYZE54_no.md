# Python金融量化：P3：03 Python基础知识（一） 📘

![](img/eccb7963ca66462fcf17efdb38e85114_0.png)

![](img/eccb7963ca66462fcf17efdb38e85114_2.png)

![](img/eccb7963ca66462fcf17efdb38e85114_4.png)

![](img/eccb7963ca66462fcf17efdb38e85114_6.png)

![](img/eccb7963ca66462fcf17efdb38e85114_8.png)

![](img/eccb7963ca66462fcf17efdb38e85114_10.png)

在本节课中，我们将要学习Python编程语言的基础知识。这是开启金融量化分析之旅的第一步。我们将从环境搭建开始，逐步学习Python的核心语法、变量、数据类型（如字符串、数字）以及三种重要的数据结构：列表、元组和字典。掌握这些内容是后续进行数据清洗、分析和可视化的基石。

![](img/eccb7963ca66462fcf17efdb38e85114_12.png)

## 环境搭建与Jupyter Notebook配置 💻

上一节我们介绍了课程的整体框架，本节中我们来看看如何搭建Python编程环境。我们推荐使用Anaconda发行版，它集成了Python和许多常用的科学计算库，并提供了Jupyter Notebook这一强大的交互式编程环境。

![](img/eccb7963ca66462fcf17efdb38e85114_14.png)

![](img/eccb7963ca66462fcf17efdb38e85114_16.png)

以下是安装Anaconda的步骤：
1.  访问Anaconda官方网站，根据你的操作系统（如Windows 64位）下载Python 3.7版本的安装程序。
2.  运行下载的安装包，按照提示进行安装。在安装选项中，建议勾选“Add Anaconda to my PATH environment variable”（将Anaconda添加到环境变量），以便在命令行中直接使用。
3.  安装完成后，你可以在开始菜单中找到“Anaconda Prompt”（终端）和“Anaconda Navigator”（图形界面管理器）。

![](img/eccb7963ca66462fcf17efdb38e85114_18.png)

![](img/eccb7963ca66462fcf17efdb38e85114_20.png)

![](img/eccb7963ca66462fcf17efdb38e85114_22.png)

安装好Anaconda后，我们来配置核心的编程环境Jupyter Notebook。Jupyter Notebook是一个基于网页的交互式计算环境，非常适合数据分析和教学。

![](img/eccb7963ca66462fcf17efdb38e85114_24.png)

![](img/eccb7963ca66462fcf17efdb38e85114_26.png)

以下是启动和初步配置Jupyter Notebook的步骤：
1.  打开“Anaconda Prompt”，输入命令 `jupyter notebook` 并回车。这将在默认浏览器中打开Jupyter Notebook的工作目录。
2.  为了获得更好的编码体验，我们安装两个有用的插件。在Anaconda Prompt中依次执行以下命令：
    ```bash
    conda install -c conda-forge jupyter_contrib_nbextensions
    conda install -c conda-forge jupyter_nbextensions_configurator
    conda install yapf
    ```
3.  安装完成后，重新启动Jupyter Notebook。在打开的Notebook界面中，你会看到顶部多了一个“Nbextensions”标签页。点击进入，勾选以下三个扩展：
    *   `Code prettify`: 代码自动格式化。
    *   `Table of Contents`: 为Notebook生成目录。
    *   `Hinterland`: 代码自动补全提示。
4.  若要更改Jupyter Notebook的默认工作目录，可以在启动前，先在Anaconda Prompt中使用 `cd` 命令切换到目标路径，例如 `cd C:\Your\Workspace\Path`，然后再运行 `jupyter notebook`。

![](img/eccb7963ca66462fcf17efdb38e85114_28.png)

![](img/eccb7963ca66462fcf17efdb38e85114_30.png)

现在，点击“New”按钮，选择“Python 3”，即可创建一个新的Notebook文件，开始你的Python编程。

![](img/eccb7963ca66462fcf17efdb38e85114_32.png)

![](img/eccb7963ca66462fcf17efdb38e85114_34.png)

![](img/eccb7963ca66462fcf17efdb38e85114_36.png)

## 变量与简单数据类型 🔤

上一节我们配置好了编程环境，本节中我们来看看Python编程的基石：变量与简单数据类型。变量是存储信息的容器，数据类型则定义了容器中信息的种类。

![](img/eccb7963ca66462fcf17efdb38e85114_38.png)

### 变量与赋值

![](img/eccb7963ca66462fcf17efdb38e85114_40.png)

![](img/eccb7963ca66462fcf17efdb38e85114_42.png)

在Python中，使用等号 `=` 为变量赋值。这意味着将等号右边的值，关联到左边的变量名上。

![](img/eccb7963ca66462fcf17efdb38e85114_44.png)

![](img/eccb7963ca66462fcf17efdb38e85114_46.png)

```python
x = 6  # 将数字6赋值给变量x
y = ‘hello world‘  # 将字符串‘hello world‘赋值给变量y
```

![](img/eccb7963ca66462fcf17efdb38e85114_48.png)

![](img/eccb7963ca66462fcf17efdb38e85114_50.png)

变量命名需要遵循以下规则：
*   必须以字母或下划线 `_` 开头。
*   不能以数字开头。
*   只能包含字母、数字和下划线。
*   不能使用Python的关键字（如 `False`, `None`, `and`, `if` 等）。
*   建议使用有意义的英文名称，以提高代码可读性（例如用 `stock_code` 而非 `x` 来代表股票代码）。

### 数字类型

![](img/eccb7963ca66462fcf17efdb38e85114_52.png)

![](img/eccb7963ca66462fcf17efdb38e85114_54.png)

Python中主要的数字类型是整数（`int`）和浮点数（`float`）。整数是不带小数点的数字，浮点数是带小数点的数字。

![](img/eccb7963ca66462fcf17efdb38e85114_56.png)

![](img/eccb7963ca66462fcf17efdb38e85114_58.png)

```python
a = 10      # 整数 (int)
b = 3.14    # 浮点数 (float)
```

![](img/eccb7963ca66462fcf17efdb38e85114_60.png)

![](img/eccb7963ca66462fcf17efdb38e85114_62.png)

![](img/eccb7963ca66462fcf17efdb38e85114_64.png)

可以使用 `type()` 函数查看变量的类型。

![](img/eccb7963ca66462fcf17efdb38e85114_66.png)

```python
print(type(a))  # 输出：<class ‘int‘>
print(type(b))  # 输出：<class ‘float‘>
```

![](img/eccb7963ca66462fcf17efdb38e85114_68.png)

### 字符串类型

![](img/eccb7963ca66462fcf17efdb38e85114_70.png)

![](img/eccb7963ca66462fcf17efdb38e85114_72.png)

![](img/eccb7963ca66462fcf17efdb38e85114_74.png)

字符串是由引号（单引号 `‘` 或双引号 `“`）括起来的文本。

![](img/eccb7963ca66462fcf17efdb38e85114_76.png)

![](img/eccb7963ca66462fcf17efdb38e85114_78.png)

![](img/eccb7963ca66462fcf17efdb38e85114_80.png)

![](img/eccb7963ca66462fcf17efdb38e85114_82.png)

```python
s1 = “Hello“
s2 = ‘World‘
```

字符串支持多种操作：
*   **连接**：使用加号 `+` 将字符串拼接。
    ```python
    greeting = s1 + “ “ + s2  # 结果为 ‘Hello World‘
    ```
*   **复制**：使用乘号 `*` 重复字符串。
    ```python
    laugh = “ha“ * 3  # 结果为 ‘hahaha‘
    ```
*   **获取长度**：使用 `len()` 函数。
    ```python
    length = len(greeting)  # 结果为 11
    ```
*   **查找与索引**：使用 `in` 关键字判断子串是否存在，或使用 `find()` 方法查找位置。字符串的索引从0开始。
    ```python
    print(“lo“ in greeting)           # 输出：True
    print(greeting.find(“World“))     # 输出：6
    print(greeting[0])                # 输出：‘H‘ (索引0，第一个字符)
    print(greeting[0:5])              # 输出：‘Hello‘ (切片，取索引0到4)
    ```
*   **分割与去除空格**：使用 `split()` 方法按指定分隔符分割字符串，使用 `strip()` 方法去除首尾空格。
    ```python
    data = “A,B,C“
    items = data.split(“,“)  # 结果为 [‘A‘, ‘B‘, ‘C‘]
    text = “  Python  “
    clean_text = text.strip()  # 结果为 ‘Python‘
    ```

### 数字运算与比较

Python支持基本的算术运算和逻辑比较。

以下是基本的算术运算符：
*   加法：`+`
*   减法：`-`
*   乘法：`*`
*   除法：`/`
*   整除：`//`
*   取余：`%`
*   乘方：`**`

```python
print(10 + 3)   # 13
print(10 - 3)   # 7
print(10 * 3)   # 30
print(10 / 3)   # 3.333...
print(10 // 3)  # 3
print(10 % 3)   # 1
print(10 ** 2)  # 100
```

![](img/eccb7963ca66462fcf17efdb38e85114_84.png)

![](img/eccb7963ca66462fcf17efdb38e85114_86.png)

![](img/eccb7963ca66462fcf17efdb38e85114_88.png)

![](img/eccb7963ca66462fcf17efdb38e85114_90.png)

比较运算返回布尔值（`True` 或 `False`）。

![](img/eccb7963ca66462fcf17efdb38e85114_92.png)

![](img/eccb7963ca66462fcf17efdb38e85114_94.png)

以下是常用的比较与逻辑运算符：
*   等于：`==`
*   不等于：`!=`
*   大于：`>`
*   小于：`<`
*   大于等于：`>=`
*   小于等于：`<=`
*   与：`and`
*   或：`or`
*   非：`not`

![](img/eccb7963ca66462fcf17efdb38e85114_96.png)

```python
print(2 == 3)           # False
print(2 != 3)           # True
print(2 < 3 and 3 > 1)  # True
print(2 < 3 or 3 < 1)   # True
print(not (2 == 3))     # True
```

![](img/eccb7963ca66462fcf17efdb38e85114_98.png)

![](img/eccb7963ca66462fcf17efdb38e85114_100.png)

![](img/eccb7963ca66462fcf17efdb38e85114_102.png)

## 列表、元组与字典 📊

上一节我们学习了变量和简单数据类型，本节中我们来看看Python中三种用于组织数据的核心结构：列表、元组和字典。它们能帮助我们更高效地存储和操作一组数据。

### 列表

![](img/eccb7963ca66462fcf17efdb38e85114_104.png)

![](img/eccb7963ca66462fcf17efdb38e85114_106.png)

列表（`list`）是用方括号 `[]` 括起来的有序元素集合，元素之间用逗号分隔。列表是可变的，意味着可以修改其中的元素。

![](img/eccb7963ca66462fcf17efdb38e85114_108.png)

![](img/eccb7963ca66462fcf17efdb38e85114_110.png)

以下是列表的基本操作：
1.  **创建与访问**：
    ```python
    my_list = [1, 2, 3, ‘a‘, ‘b‘]  # 创建列表
    print(my_list[0])               # 输出第一个元素: 1
    print(my_list[1:3])             # 切片，输出索引1到2的元素: [2, 3]
    ```
2.  **修改元素**：
    ```python
    my_list[0] = 100  # 将第一个元素改为100
    ```
3.  **添加元素**：
    ```python
    my_list.append(‘new‘)      # 在末尾添加元素
    my_list.insert(1, ‘insert‘) # 在索引1处插入元素
    ```
4.  **删除元素**：
    ```python
    del my_list[0]           # 删除索引0的元素
    popped_item = my_list.pop()  # 删除并返回末尾元素
    my_list.remove(‘a‘)      # 删除第一个值为‘a‘的元素
    ```
5.  **排序**：
    ```python
    num_list = [3, 1, 4, 2]
    num_list.sort()                 # 原地排序，变为[1,2,3,4]
    sorted_list = sorted(num_list, reverse=True)  # 返回新排序列表，不改变原列表
    ```
6.  **其他操作**：
    ```python
    list2 = [‘x‘, ‘y‘]
    combined = my_list + list2      # 列表合并
    repeated = list2 * 2            # 列表复制
    length = len(my_list)           # 获取列表长度
    ```

![](img/eccb7963ca66462fcf17efdb38e85114_112.png)

![](img/eccb7963ca66462fcf17efdb38e85114_114.png)

![](img/eccb7963ca66462fcf17efdb38e85114_116.png)

### 元组

![](img/eccb7963ca66462fcf17efdb38e85114_118.png)

![](img/eccb7963ca66462fcf17efdb38e85114_120.png)

![](img/eccb7963ca66462fcf17efdb38e85114_122.png)

元组（`tuple`）是用圆括号 `()` 括起来的有序元素集合。它与列表的关键区别在于**元组是不可变的**，创建后不能修改其内容。这使得元组在某些场景下（如作为字典的键）比列表更安全、更高效。

```python
my_tuple = (1, 2, 3, ‘apple‘)
print(my_tuple[0])  # 输出: 1
# my_tuple[0] = 100  # 这行会报错，因为元组不可变
```

### 字典

字典（`dict`）是用花括号 `{}` 括起来的**键值对**集合。每个键值对中，键（`key`）和值（`value`）用冒号 `:` 分隔。字典是无序的（在Python 3.7+中，插入顺序被保留），但可以通过唯一的键来快速访问对应的值。

以下是字典的基本操作：
1.  **创建与访问**：
    ```python
    stock_dict = {‘贵州茅台‘: 840, ‘中国平安‘: 78}
    print(stock_dict[‘贵州茅台‘])  # 输出键‘贵州茅台‘对应的值: 840
    ```
2.  **添加与修改**：
    ```python
    stock_dict[‘招商银行‘] = 36   # 添加新的键值对
    stock_dict[‘贵州茅台‘] = 850  # 修改已有键对应的值
    ```
3.  **删除**：
    ```python
    del stock_dict[‘中国平安‘]  # 删除键‘中国平安‘及其值
    ```
4.  **常用方法**：
    ```python
    keys = stock_dict.keys()    # 获取所有键
    values = stock_dict.values() # 获取所有值
    items = stock_dict.items()  # 获取所有键值对（每个键值对是一个元组）
    ```
5.  **嵌套**：字典的值可以是任意类型，包括列表、元组甚至另一个字典，这允许我们构建复杂的数据结构。
    ```python
    complex_dict = {
        ‘portfolio1‘: [‘贵州茅台‘, ‘中国平安‘],
        ‘portfolio2‘: {‘股票‘: ‘招商银行‘, ‘价格‘: 36}
    }
    ```

## 总结 🎯

本节课中我们一起学习了Python编程的基础核心内容。

我们首先完成了**Anaconda环境和Jupyter Notebook的搭建与配置**，为学习准备好了强大的交互式编程平台。

接着，我们深入探讨了**变量与简单数据类型**，理解了如何通过变量存储信息，并掌握了数字（整型、浮点型）和字符串的表示方法、基本运算及常用操作。

最后，我们学习了三种重要的**数据结构**：
*   **列表**：可变的有序序列，功能强大灵活，是存储一组数据的首选。
*   **元组**：不可变的有序序列，适用于存储不应被修改的数据集合。
*   **字典**：通过键值对存储数据，提供了基于键的快速数据检索能力，非常适合存储具有映射关系的信息（如股票名称与价格）。

![](img/eccb7963ca66462fcf17efdb38e85114_124.png)

这些基础知识是构建更复杂金融量化程序的砖瓦。请务必在课后通过Jupyter Notebook进行练习，亲手输入代码，加深理解。在接下来的课程中，我们将学习控制流语句，让程序能够根据条件做出判断和循环执行，使我们的代码真正“活”起来。