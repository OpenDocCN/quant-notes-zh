# Python量化金融：第12讲：数据读取基础 📊

在本节课中，我们将学习量化金融分析中最基础也是最重要的一步：如何使用Python读取数据。我们将重点介绍如何从Excel和CSV这两种常见格式的文件中调用数据，为后续的分析工作打下基础。

---

## 课程背景与预告

![](img/56e471583ed6ea1b68d01b06c9e0b2fb_1.png)

北京大学将于8月2日至4日举办第一期“Python与量化金融研修班”。该课程前期讲解Python基础知识，后期由我主讲量化金融应用、模型及财经数据分析。课程为期三天，旨在为学员提供Python量化金融分析的入门引导，帮助大家建立基础，以便未来学习更高级的内容。

---

## 数据读取的核心概念

量化金融分析所使用的数据，通常是股票、期货、期权、汇率等特定财经数据。这些数据通常被整理并存储为Excel表格或CSV文件。因此，掌握Python读取这两种格式文件的方法是关键的第一步。

![](img/56e471583ed6ea1b68d01b06c9e0b2fb_3.png)

### 准备工作：导入库

在读取数据前，我们需要导入必要的Python库。`pandas`库用于数据处理，`numpy`库用于数值计算。

```python
import pandas as pd
import numpy as np
```

---

## 读取Excel文件

上一节我们介绍了数据读取的重要性，本节中我们来看看如何具体操作。以下是用Python读取Excel文件的步骤。

首先，使用`pandas`的`read_excel`函数来加载数据。你需要提供文件的完整路径和名称。

```python
df = pd.read_excel(‘/路径/到/你的文件/数据文件名.xlsx’)
```

读取完成后，可以使用`.head()`方法查看数据的前几行，以确认数据已正确加载。

```python
df.head()
```

执行上述代码后，会显示数据表的前五行。你会看到数据变量以及Python自动生成的行索引（从0开始）。

为了进一步了解数据的结构，我们可以提取特定变量并查看其形状（即观测值的数量）。

以下是具体操作步骤：
1.  将Excel数据读入一个变量（例如`data`）。
2.  使用`.head()`预览数据。
3.  提取两个指定的列（例如`C2`和`C3`）为变量`X`和`Y`。
4.  使用`.shape`属性查看每个变量的观测值数量。

```python
data = pd.read_excel(‘文件路径/数据文件名.xlsx’)
data.head()

X = np.array(data[‘C2’])
Y = np.array(data[‘C3’])

print(X.shape)
print(Y.shape)
```

![](img/56e471583ed6ea1b68d01b06c9e0b2fb_5.png)

输出结果`(13,)`表示变量`X`和`Y`各自包含13个观测值。

---

## 读取CSV文件

学会了读取Excel文件后，我们再来看看另一种常见格式——CSV文件。CSV（逗号分隔值）也是量化金融中常用的数据存储格式。

读取CSV文件的方法与读取Excel类似，主要区别在于使用的函数是`read_csv`。

以下是读取CSV文件的核心步骤：
1.  使用`pd.read_csv()`函数并指定文件路径。
2.  同样使用`.head()`方法预览数据前五行。
3.  提取所需变量并检查其形状，以确认数据维度。

```python
df_csv = pd.read_csv(‘/路径/到/你的文件/数据文件名.csv’)
df_csv.head()

X_csv = np.array(df_csv[‘某列名’])
Y_csv = np.array(df_csv[‘另一列名’])

print(X_csv.shape)
print(Y_csv.shape)
```

---

## 总结与回顾

![](img/56e471583ed6ea1b68d01b06c9e0b2fb_7.png)

本节课中，我们一起学习了Python量化金融分析的数据基础。核心内容包括：
1.  **导入库**：使用`import pandas as pd`和`import numpy as np`。
2.  **读取Excel**：使用`pd.read_excel()`函数，并通过`.head()`和`.shape`来查看数据和其维度。
3.  **读取CSV**：使用`pd.read_csv()`函数，操作流程与读取Excel文件一致。

简而言之，进行Python量化金融分析，就是先将财经数据存储在Excel或CSV文件中，然后用Python调用这些数据，并在Python环境中进行后续的分析、建模或策略研究。而正确读取数据，是整个流程中最基本且至关重要的一步。

希望本讲内容能帮助你打好基础。也欢迎大家关注并参与北京大学8月份举办的“Python与量化金融研修班”，深入学习更多知识。