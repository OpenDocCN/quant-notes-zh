# Python金融量化：P3：03 Python基础知识（一） 📘

![](img/faf2b34b1435b42756e15ac84d04b3f8_1.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_3.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_5.png)

在本节课中，我们将要学习Python的基础知识，包括变量、简单的数据类型（数字和字符串）、以及三种重要的数据结构：列表、元组和字典。掌握这些内容是进行后续金融量化分析的基础。

![](img/faf2b34b1435b42756e15ac84d04b3f8_7.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_8.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_10.png)

## 安装Python环境 🛠️

上一节我们介绍了课程的整体框架，本节中我们来看看如何搭建Python编程环境。我们不推荐安装Python官方版，因为它在安装第三方模块时可能出现问题。我们建议初学者下载Anaconda软件，它集成了Jupyter Notebook等工具，非常适合学习和使用。

以下是安装Anaconda的步骤：
1.  访问Anaconda官方网站。
2.  根据您的操作系统（如Windows）和位数（64位或32位）选择Python 3.7版本进行下载。
3.  运行安装程序，按照提示进行安装。在安装过程中，建议勾选“Add Anaconda to my PATH environment variable”选项，以便在命令行中直接使用。

![](img/faf2b34b1435b42756e15ac84d04b3f8_12.png)

安装完成后，您可以通过“Anaconda Prompt”终端来安装额外的包，命令格式为：`conda install 包名`，例如 `conda install requests`。

![](img/faf2b34b1435b42756e15ac84d04b3f8_14.png)

## 使用Jupyter Notebook 📓

![](img/faf2b34b1435b42756e15ac84d04b3f8_16.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_17.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_19.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_20.png)

环境安装好后，我们需要一个交互式平台来编写和运行代码。Jupyter Notebook是一个非常好用且轻量化的选择。本课程后续部分也将使用它进行讲解。

![](img/faf2b34b1435b42756e15ac84d04b3f8_22.png)

有两种方式打开Jupyter Notebook：
1.  从Anaconda Navigator客户端中直接点击Jupyter Notebook图标。
2.  在Anaconda Prompt终端中输入命令 `jupyter notebook`。

![](img/faf2b34b1435b42756e15ac84d04b3f8_24.png)

启动后，Jupyter Notebook会在浏览器中打开。它的界面主要包含文件列表（Files）、运行状态（Running）和集群（Clusters）等部分。要新建一个Python文件，可以点击“New”按钮，然后选择“Python 3”。

![](img/faf2b34b1435b42756e15ac84d04b3f8_26.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_28.png)

Jupyter Notebook的工作单元称为“Cell”。有两种模式：
*   **编辑模式**：可以输入代码或文本，边框为绿色。
*   **命令模式**：可以操作Cell本身（如移动、删除），边框为蓝色。按`Esc`键进入命令模式，按`Enter`键进入编辑模式。

![](img/faf2b34b1435b42756e15ac84d04b3f8_30.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_31.png)

为了提高使用体验，我们可以安装一些插件。在Anaconda Prompt中执行以下命令来安装`nb_extensions`：
```bash
conda install -c conda-forge jupyter_contrib_nbextensions
jupyter contrib nbextension install --user
conda install -c conda-forge jupyter_nbextensions_configurator
```
安装完成后，在Jupyter Notebook主页点击“Nbextensions”标签，勾选“Code prettify”（代码美化）、“Table of Contents”（目录）和“Hinterland”（代码提示）等实用功能。

![](img/faf2b34b1435b42756e15ac84d04b3f8_33.png)

要更改Jupyter Notebook的默认工作目录，可以在启动前，先在Anaconda Prompt中使用`cd`命令切换到目标路径，例如：`cd C:\Your\Desired\Path`，然后再启动`jupyter notebook`。

## 变量与简单数据类型 🔤

现在，让我们开始学习Python的核心语法。首先从变量和简单的数据类型开始。

![](img/faf2b34b1435b42756e15ac84d04b3f8_35.png)

### 变量

![](img/faf2b34b1435b42756e15ac84d04b3f8_37.png)

变量可以理解为数据的名字或标签。在Python中，使用等号`=`为变量赋值。
```python
x = 6
y = "hello world"
```
运行变量名（如`x`）可以查看其当前值。使用`print()`函数可以将内容输出到屏幕。
```python
print(x)
print(y)
```
**变量命名规则**：
*   必须以字母或下划线开头，不能以数字开头。
*   不能使用Python的关键字（如`for`, `if`, `and`等）。
*   建议使用有意义的英文名称，以提高代码可读性。

![](img/faf2b34b1435b42756e15ac84d04b3f8_39.png)

### 数据类型

![](img/faf2b34b1435b42756e15ac84d04b3f8_41.png)

Python中基本的数据类型包括数字和字符串。使用`type()`函数可以查看变量的类型。

![](img/faf2b34b1435b42756e15ac84d04b3f8_42.png)

**数字**：主要分为整数（`int`）和浮点数（`float`）。
```python
a = 10       # int
b = 3.14     # float
print(type(a)) # 输出：<class 'int'>
print(type(b)) # 输出：<class 'float'>
```

**字符串**：用单引号`'`或双引号`"`括起来的文本。
```python
s1 = 'hello'
s2 = "world"
print(type(s1)) # 输出：<class 'str'>
```

![](img/faf2b34b1435b42756e15ac84d04b3f8_44.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_45.png)

### 字符串常用操作

![](img/faf2b34b1435b42756e15ac84d04b3f8_47.png)

以下是字符串的一些基本操作：

![](img/faf2b34b1435b42756e15ac84d04b3f8_49.png)

**连接与复制**：
```python
# 连接
greeting = 'Hello' + ' ' + 'World'
print(greeting) # 输出：Hello World

![](img/faf2b34b1435b42756e15ac84d04b3f8_51.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_52.png)

# 复制
repeat = 'Hi' * 3
print(repeat)   # 输出：HiHiHi
```

**获取长度**：使用`len()`函数。
```python
length = len('Python')
print(length) # 输出：6
```

**查找**：使用`in`关键字或`find()`方法。
```python
s = 'hello world'
# 使用 in
print('e' in s)   # 输出：True
print('a' in s)   # 输出：False

![](img/faf2b34b1435b42756e15ac84d04b3f8_54.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_56.png)

# 使用 find
print(s.find('e')) # 输出：1 (索引从0开始)
print(s.find('a')) # 输出：-1 (未找到)
```

![](img/faf2b34b1435b42756e15ac84d04b3f8_58.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_60.png)

**索引与切片**：通过位置获取部分字符串。
```python
s = 'hello world'
print(s[0])     # 输出：h (第一个字符)
print(s[0:5])   # 输出：hello (索引0到4，不包含5)
```

![](img/faf2b34b1435b42756e15ac84d04b3f8_62.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_63.png)

**分割与去除空格**：
```python
# 分割
data = 'A,B,C'
print(data.split(',')) # 输出：['A', 'B', 'C']

# 去除首尾空格
text = '  python  '
print(text.strip())    # 输出：'python'
```

## 数字计算与比较 ➕➖

Python支持基本的数学运算和逻辑比较。

### 基本运算

运算符包括：加`+`、减`-`、乘`*`、除`/`、乘方`**`、取整`//`、取余`%`。
```python
print(10 + 5)   # 15
print(10 - 5)   # 5
print(10 * 5)   # 50
print(10 / 3)   # 3.333...
print(10 ** 2)  # 100
print(10 // 3)  # 3
print(10 % 3)   # 1
```

### 比较与逻辑运算

![](img/faf2b34b1435b42756e15ac84d04b3f8_65.png)

比较运算符用于判断关系，返回布尔值（`True`或`False`）：等于`==`、不等于`!=`、大于`>`、小于`<`、大于等于`>=`、小于等于`<=`。
```python
print(2 == 2)   # True
print(2 != 2)   # False
print(5 > 3)    # True
```
逻辑运算符用于连接多个条件：与`and`、或`or`、非`not`。
```python
print((2 > 1) and (3 < 5)) # True
print((2 > 1) or (3 > 5))  # True
print(not (2 > 1))         # False
```

![](img/faf2b34b1435b42756e15ac84d04b3f8_66.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_68.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_69.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_71.png)

## 列表、元组与字典 📊

![](img/faf2b34b1435b42756e15ac84d04b3f8_72.png)

上一节我们学习了基础类型，本节中我们来看看三种用于存储数据集合的结构。

![](img/faf2b34b1435b42756e15ac84d04b3f8_74.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_75.png)

### 列表 (List)

![](img/faf2b34b1435b42756e15ac84d04b3f8_77.png)

列表用于存储一组**有序**、**可变**的元素，用方括号`[]`表示。

![](img/faf2b34b1435b42756e15ac84d04b3f8_79.png)

**创建与基本操作**：
```python
# 创建列表
my_list = [1, 2, 3, 'a', 'b']
empty_list = []

# 索引与切片 (与字符串类似)
print(my_list[0])      # 输出：1
print(my_list[1:3])    # 输出：[2, 3]

# 修改元素
my_list[0] = 100
print(my_list)         # 输出：[100, 2, 3, 'a', 'b']

![](img/faf2b34b1435b42756e15ac84d04b3f8_81.png)

# 添加元素
my_list.append('new')
print(my_list)         # 输出：[100, 2, 3, 'a', 'b', 'new']

![](img/faf2b34b1435b42756e15ac84d04b3f8_83.png)

# 删除元素
del my_list[1]         # 删除索引为1的元素
print(my_list)         # 输出：[100, 3, 'a', 'b', 'new']

![](img/faf2b34b1435b42756e15ac84d04b3f8_85.png)

popped = my_list.pop() # 删除并返回最后一个元素
print(popped)          # 输出：'new'
print(my_list)         # 输出：[100, 3, 'a', 'b']
```

![](img/faf2b34b1435b42756e15ac84d04b3f8_87.png)

**列表排序**：
```python
nums = [3, 1, 4, 1, 5]
nums.sort()            # 原地排序，升序
print(nums)            # 输出：[1, 1, 3, 4, 5]

![](img/faf2b34b1435b42756e15ac84d04b3f8_89.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_91.png)

nums.sort(reverse=True) # 原地排序，降序
print(nums)            # 输出：[5, 4, 3, 1, 1]

![](img/faf2b34b1435b42756e15ac84d04b3f8_93.png)

![](img/faf2b34b1435b42756e15ac84d04b3f8_95.png)

# 不改变原列表的排序
nums = [3, 1, 4]
sorted_nums = sorted(nums) # 返回新列表
print(nums)                # 输出：[3, 1, 4] (未变)
print(sorted_nums)         # 输出：[1, 3, 4]
```

### 元组 (Tuple)

元组与列表类似，但它是**不可变**的，用圆括号`()`表示。一旦创建，其元素不能修改。
```python
my_tuple = (1, 2, 3, 'apple')
print(my_tuple[0])     # 输出：1
# my_tuple[0] = 100    # 这行会报错，元组不可修改
```
元组常用于确保数据不被意外改变的场景。

### 字典 (Dictionary)

字典用于存储**键值对（Key-Value）** 映射关系，用花括号`{}`表示，键和值之间用冒号`:`分隔。
```python
# 创建字典
stock_price = {'贵州茅台': 840, '中国平安': 78}
empty_dict = {}

# 访问值
print(stock_price['贵州茅台']) # 输出：840

# 添加或修改键值对
stock_price['招商银行'] = 36   # 添加
stock_price['贵州茅台'] = 850  # 修改

# 获取所有键、值、键值对
print(stock_price.keys())   # 输出：dict_keys(['贵州茅台', '中国平安', '招商银行'])
print(stock_price.values()) # 输出：dict_values([850, 78, 36])
print(stock_price.items())  # 输出：dict_items([('贵州茅台', 850), ...])

# 字典可以嵌套
complex_data = {
    'A001': {'price': 100, 'volume': 1000},
    'A002': [1, 2, 3, 4]
}
```

## Python之禅 🧘

最后，我们通过一个彩蛋来感受Python的设计哲学。在Python中运行`import this`，你会看到《Python之禅》（The Zen of Python），它包含了Python语言设计的19条指导原则，例如：
*   Beautiful is better than ugly.（优美胜于丑陋）
*   Explicit is better than implicit.（明了胜于晦涩）
*   Simple is better than complex.（简洁胜于复杂）
*   Readability counts.（可读性很重要）

这提醒我们，编写清晰、易读的代码是非常重要的。

## 总结 📝

本节课中我们一起学习了Python编程的基础知识：
1.  **环境搭建**：安装并配置了Anaconda和Jupyter Notebook。
2.  **变量与数据类型**：学会了如何定义变量，并了解了数字（`int`, `float`）和字符串（`str`）这两种基本类型及其操作。
3.  **运算与比较**：掌握了Python中的数学运算和逻辑比较。
4.  **数据结构**：深入理解了三种核心数据结构：
    *   **列表（List）**：有序、可变的数据序列。
    *   **元组（Tuple）**：有序、不可变的数据序列。
    *   **字典（Dictionary）**：通过键值对存储数据的映射结构。

![](img/faf2b34b1435b42756e15ac84d04b3f8_97.png)

这些内容是构建更复杂程序的基石。请务必在课后通过手动输入代码进行练习，加深理解。课程讲义中提供了所有演示代码，可供复习参考。