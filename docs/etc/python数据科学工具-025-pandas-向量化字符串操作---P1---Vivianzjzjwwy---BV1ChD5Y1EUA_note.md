# Python数据科学工具：025：Pandas向量化字符串操作 📚

在本节课中，我们将要学习Pandas库中的向量化字符串操作。Python本身在处理字符串数据方面比较方便，而Pandas在此基础上提供了一整套完整的向量化字符串操作（vectorized string operation）。在处理现实世界的数据时，经常需要清理数据，这种操作是必不可少的。我们将首先了解这些操作，然后通过一个从互联网上搜集的、数据相当混乱的食谱数据集进行部分清理。

![](img/cc43c25c2c90e384ccb96c10508ca084_1.png)

## 向量化操作的概念 🔄

上一节我们介绍了向量化操作的基本概念。前面我们已经看到，像NumPy和Pandas这样的工具能够将数学运算进一步通用化。这种通用化意味着我们可以快速地在数组的所有成员上执行相同的运算。

例如，我们有一个数组 `arr = np.array([2, 3, 5, 7, 11, 13])`。原本的算术运算只能在标量（简单数据类型）上进行。现在，用整个数组乘以2，实际上是在数组的每一个成员上都执行了乘法运算。

![](img/cc43c25c2c90e384ccb96c10508ca084_3.png)

这种操作我们前面已经讲过，它叫做向量化操作，在这里是通过广播（broadcasting）来实现的。有了这种操作后，在处理数组数据时，程序可以写得很简单。我们不需要考虑数组的大小或形状，只需要考虑想做什么运算。

但是，对于字符串数组，NumPy没有这样的能力，它无法简单地做这些事情。这时就需要回到原来的循环，或者使用列表推导式。

![](img/cc43c25c2c90e384ccb96c10508ca084_5.png)

![](img/cc43c25c2c90e384ccb96c10508ca084_7.png)

例如，有这样一个数据 `data = ['peter', 'paul', 'mary']`。我想对它进行清理，让字符串更整齐，这时只能用列表推导式：`[s.capitalize() for s in data]`。

![](img/cc43c25c2c90e384ccb96c10508ca084_9.png)

当然，有时用列表推导式或循环就可以。但如果数据中有缺失值，这种方法就行不通了。

![](img/cc43c25c2c90e384ccb96c10508ca084_11.png)

例如，数据中有一个缺失值：`data = ['peter', 'paul', None, 'mary']`。如果还是用列表推导式 `[s.capitalize() for s in data]`，就会出错，因为 `NoneType` 对象没有 `capitalize` 方法。

## Pandas的解决方案 🛠️

![](img/cc43c25c2c90e384ccb96c10508ca084_13.png)

Pandas提供了向量化字符串操作的功能，并且能够正确处理缺失数据的情况。它是通过Series或Index对象的 `.str` 属性来实现的。

我们看一个例子。首先，从前面这个有缺失数据的 `data` 里创建一个Series对象：`names = pd.Series(data)`。这个Series中，`None` 是缺失值。

![](img/cc43c25c2c90e384ccb96c10508ca084_15.png)

![](img/cc43c25c2c90e384ccb96c10508ca084_17.png)

现在，就可以使用它的 `.str` 属性。有了这个属性后，就可以调用类似原生字符串的 `capitalize` 方法：`names.str.capitalize()`。这时，所有字符串都被正确处理了，缺失值被保留为 `NaN`。

你还可以查看 `names.str.` 后按Tab键，会发现几乎所有原生字符串的方法都在这个 `.str` 属性下。

## 可用的字符串方法列表 📋

![](img/cc43c25c2c90e384ccb96c10508ca084_19.png)

以下是Pandas `.str` 属性下映射的部分方法。这些函数看起来跟Python原生字符串函数一模一样，但实际上是通过Series对象的 `.str` 属性映射过去的。

![](img/cc43c25c2c90e384ccb96c10508ca084_21.png)

需要注意的是，这些方法的返回值类型不同，这与原生字符串方法一致：
*   `lower()` 返回一个字符串Series。
*   `len()` 返回一个整数Series。
*   `startswith()` 返回一个布尔Series。
*   `split()` 返回一个列表Series。

```python
# 示例
names = pd.Series(['peter', 'paul', None, 'mary'])
print(names.str.lower())   # 返回字符串Series
print(names.str.len())     # 返回整数Series
print(names.str.startswith('p')) # 返回布尔Series
print(names.str.split('e')) # 返回列表Series
```

## 正则表达式方法 🧩

还有一些方法接受正则表达式，它们可以检查每个字符串成员的内容。这些函数的使用方法与Python内置的 `re` 模块相同。

它们的映射对应关系如下：
*   `match()`：在每个成员上执行 `re.match()`，返回布尔Series。
*   `extract()`：在每个成员上执行匹配，如果正则表达式中有分组，则返回匹配的组作为字符串列。
*   `findall()`：在每个成员上执行 `re.findall()`。
*   `replace()`：使用正则表达式模式进行替换。
*   `contains()`：在每个成员上执行 `re.search()`，检查是否包含模式，返回布尔Series。
*   `count()`：计算模式出现的次数，返回整数Series。
*   `split()`：使用正则表达式进行分割，返回列表Series。

例如，想从一系列全名中提取每个人的名（first name）。可以使用 `extract()` 方法，并在正则表达式中提供一个分组。

```python
names = pd.Series(['John Doe', 'Jane Smith', 'Bob Johnson'])
# 提取第一个单词（名）
first_names = names.str.extract(r'([A-Za-z]+)')
print(first_names)

# 提取名和姓（两个分组）
full_split = names.str.extract(r'([A-Za-z]+)\s+([A-Za-z]+)')
print(full_split)
```

你也可以做一些更复杂的操作，例如用 `findall()` 寻找以辅音字母开头和结尾的单词。

```python
import re
pattern = re.compile(r'[^aeiouAEIOU].*[^aeiouAEIOU]')
# 测试单个字符串
test_string = 'John'
print(pattern.findall(test_string))

# 在Series上向量化操作
result = names.str.findall(r'[^aeiouAEIOU].*[^aeiouAEIOU]')
print(result)
```

如果不清楚正则表达式的结果，可以先用 `re` 模块测试。Pandas的 `.str` 方法只是在每个成员上执行对应的 `re` 操作，然后将结果作为一个Series返回。在清理和分析数据时，这非常方便。

![](img/cc43c25c2c90e384ccb96c10508ca084_23.png)

## 其他便捷方法 ⚙️

除了映射的方法，`.str` 属性还提供了一些其他便捷功能。

![](img/cc43c25c2c90e384ccb96c10508ca084_25.png)

以下是部分方法：
*   `get()` / `[]`：获取每个字符串的指定位置字符。
*   `slice()` / `[]`：获取每个字符串的切片。
*   `cat()`：连接字符串。
*   `repeat()`：重复字符串。
*   `get_dummies()`：将分类字符串展开为虚拟变量（dummy variables）。

我们先看一下 `get` 和 `slice`。

对于一个字符串，例如 `name = ‘peter’`，可以用 `name[0]` 索引第一个字符，也可以用切片 `name[0:3]`。

Pandas提供的 `.str[0:3]` 或 `.str.slice(0, 3)` 是对Series中每个成员取 `0:3` 的切片。`.str[2]` 或 `.str.get(2)` 是取每个成员的第二个字符。`slice` 和 `get` 是函数形式的同义词，用中括号索引是语法糖。

你可以组合这些方法。例如，先用 `split()` 分开名和姓，得到一个列表Series，然后用 `get(1)` 取出姓。

```python
names = pd.Series(['John Doe', 'Jane Smith'])
last_names = names.str.split().str.get(1)
print(last_names)
```

另一个有用的方法是 `get_dummies`。假设DataFrame中有一列包含用分隔符连接的多个标签（例如 `‘A|C’` 表示具有属性A和C），`get_dummies` 可以很方便地将其拆解成虚拟变量列。

```python
df = pd.DataFrame({'info': ['A|C', 'B|D', 'A|B|C']})
dummies = df['info'].str.get_dummies('|')
print(dummies)
```

这在机器学习和数据预处理中准备特征时非常有用。

## 总结 📝

![](img/cc43c25c2c90e384ccb96c10508ca084_27.png)

本节课我们一起学习了Pandas中的向量化字符串操作。我们了解到，通过Series的 `.str` 属性，可以方便地在整个数据列上应用字符串方法，并自动处理缺失值。我们学习了三类主要操作：
1.  **映射的字符串方法**：如 `lower()`, `len()`, `split()` 等，它们返回不同类型的Series。
2.  **正则表达式方法**：如 `match()`, `extract()`, `replace()` 等，它们基于 `re` 模块，提供了强大的模式匹配和文本提取能力。
3.  **其他便捷工具**：如 `get()`, `slice()`, `get_dummies()` 等，用于字符串索引、切片和编码。

![](img/cc43c25c2c90e384ccb96c10508ca084_29.png)

![](img/cc43c25c2c90e384ccb96c10508ca084_30.png)

有了这些函数作为基本的构建块，你就可以在其上构建复杂的数据清理或整理程序。要了解更多细节和示例，可以查阅Pandas官方文档中 “Working with text data” 部分。