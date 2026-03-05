# Pandas基础：P1：入门与数据查看 📊

在本节课中，我们将要学习Python中一个非常强大的数据处理和分析库——Pandas。我们将从安装开始，逐步学习如何导入数据、查看数据的基本信息，以及如何选取指定的行和列。

---

![](img/3f264276c362d5a9a2adc13982b847a6_1.png)

## 安装Pandas库

首先，我们需要安装Pandas库。安装过程与安装其他Python库类似。如果你使用Anaconda，可以打开对应环境的终端（Terminal）。

![](img/3f264276c362d5a9a2adc13982b847a6_3.png)

在终端中输入以下代码即可完成安装：

```bash
pip install pandas
```

如果网络状况不佳，可以多尝试几次。安装过程相对简单。

![](img/3f264276c362d5a9a2adc13982b847a6_5.png)

---

![](img/3f264276c362d5a9a2adc13982b847a6_6.png)

## 导入数据与DataFrame

![](img/3f264276c362d5a9a2adc13982b847a6_8.png)

![](img/3f264276c362d5a9a2adc13982b847a6_10.png)

上一节我们介绍了如何安装Pandas，本节中我们来看看如何导入数据并将其转化为Pandas的核心数据结构——DataFrame。

![](img/3f264276c362d5a9a2adc13982b847a6_12.png)

我们使用`read_csv`函数来导入一个CSV格式的数据文件。以下是示例代码：

```python
import pandas as pd
df = pd.read_csv('path/to/your/data.csv')
```

![](img/3f264276c362d5a9a2adc13982b847a6_14.png)

这段代码使用`pd.read_csv`读取了指定路径下的CSV文件，并将其赋值给变量`df`。`df`的全称是DataFrame，它是Pandas中最常用的数据结构，类似于一个Excel表格，有行、列和单元格。

我们本次使用的CSV文件是BTC的5分钟K线数据。文件的第一行是列名，其含义如下：
*   **timestamp**: 时间戳，对应具体的时间点。
*   **open**: 5分钟的开盘价格。
*   **high**: 5分钟的最高价格。
*   **low**: 5分钟的最低价格。
*   **close**: 5分钟的收盘价格。
*   **volume**: 交易量。

![](img/3f264276c362d5a9a2adc13982b847a6_16.png)

![](img/3f264276c362d5a9a2adc13982b847a6_18.png)

如果不理解K线数据，可以搜索相关基础知识进行了解。

![](img/3f264276c362d5a9a2adc13982b847a6_20.png)

我们可以使用`print`函数查看导入的数据：

![](img/3f264276c362d5a9a2adc13982b847a6_22.png)

![](img/3f264276c362d5a9a2adc13982b847a6_24.png)

```python
print(df)
```

![](img/3f264276c362d5a9a2adc13982b847a6_26.png)

---

![](img/3f264276c362d5a9a2adc13982b847a6_28.png)

## 查看数据基本信息

![](img/3f264276c362d5a9a2adc13982b847a6_30.png)

在成功导入数据后，我们需要对数据有一个初步的了解。以下是几个查看数据基本信息的方法。

![](img/3f264276c362d5a9a2adc13982b847a6_31.png)

**1. 转换时间戳格式**
原始的时间戳不便于阅读，我们可以使用`pd.to_datetime`方法将其转换为更友好的日期时间格式。

![](img/3f264276c362d5a9a2adc13982b847a6_33.png)

```python
df['timestamp'] = pd.to_datetime(df['timestamp'])
print(df['timestamp'].head())
```
运行后，时间戳会显示为如“2024-01-29 08:43:20”的格式。

![](img/3f264276c362d5a9a2adc13982b847a6_35.png)

**2. 查看数据形状**
使用`shape`属性可以查看DataFrame有多少行和多少列。

![](img/3f264276c362d5a9a2adc13982b847a6_37.png)

![](img/3f264276c362d5a9a2adc13982b847a6_38.png)

```python
print(df.shape)
```
输出结果是一个元组，例如`(100, 7)`，表示有100行和7列。

![](img/3f264276c362d5a9a2adc13982b847a6_40.png)

**3. 查看列名**
使用`columns`属性可以输出每一列的列名。

```python
print(df.columns)
```
可以使用for循环遍历每个列名：
```python
for col in df.columns:
    print(col)
```

![](img/3f264276c362d5a9a2adc13982b847a6_42.png)

**4. 查看行索引**
使用`index`属性可以输出每一行的索引名。

```python
print(df.index)
```
通常输出一个从0开始的等差数列（如0到99）。同样可以用for循环遍历。

![](img/3f264276c362d5a9a2adc13982b847a6_44.png)

**5. 随机查看样本**
如果数据量很大，可以使用`sample`方法随机查看几行数据。

![](img/3f264276c362d5a9a2adc13982b847a6_46.png)

```python
print(df.sample(3))
```
这段代码会随机抽取并显示3行数据。

![](img/3f264276c362d5a9a2adc13982b847a6_48.png)

**6. 获取描述性统计**
`describe`函数可以快速生成数据各列的统计摘要，包括计数、平均值、标准差、最小值、各分位数和最大值。这个函数只对数值型列有效，能让我们对数据分布有直观感受。

![](img/3f264276c362d5a9a2adc13982b847a6_50.png)

```python
print(df.describe())
```

---

![](img/3f264276c362d5a9a2adc13982b847a6_52.png)

## 选取指定的行和列

![](img/3f264276c362d5a9a2adc13982b847a6_54.png)

了解数据概览后，我们常常需要提取其中的特定部分进行分析。以下是选取数据的方法。

![](img/3f264276c362d5a9a2adc13982b847a6_56.png)

**1. 根据列名选取**
可以通过列名直接选取单列或多列数据。
*   选取单列（例如`open`列）：
    ```python
    df['open']
    ```
*   选取多列（例如`timestamp`和`close`列）：
    ```python
    df[['timestamp', 'close']]
    ```

![](img/3f264276c362d5a9a2adc13982b847a6_58.png)

**2. 使用`loc`和`iloc`方法选取**
`loc`和`iloc`是更强大的数据选取方法，两者区别在于：
*   **`loc`**：基于**标签名**进行选取。
*   **`iloc`**：基于**整数位置索引**进行选取。

![](img/3f264276c362d5a9a2adc13982b847a6_60.png)

通过一个例子可以很好地区分它们。假设我们要选取`open`列第3行的数据（值为41985.0）。
*   使用`loc`方法（标签索引）：
    ```python
    # 选取行标签为2，列标签为‘open’的数据
    print(df.loc[2, 'open'])
    ```
*   使用`iloc`方法（位置索引）：
    ```python
    # 选取第2行（索引从0开始），第1列（‘open’列是第1列）的数据
    print(df.iloc[2, 1])
    ```

![](img/3f264276c362d5a9a2adc13982b847a6_62.png)

![](img/3f264276c362d5a9a2adc13982b847a6_64.png)

我们也可以使用它们来选取一个数据区域。
*   使用`loc`选取所有行，以及从`open`到`low`的列：
    ```python
    df.loc[:, 'open':'low']
    ```
*   使用`iloc`实现相同的选取（所有行，第1列到第3列）：
    ```python
    df.iloc[:, 1:4] # 注意切片是左闭右开区间
    ```
这两种操作在未来的数据处理中会非常常见。

![](img/3f264276c362d5a9a2adc13982b847a6_66.png)

---

![](img/3f264276c362d5a9a2adc13982b847a6_68.png)

## 总结

![](img/3f264276c362d5a9a2adc13982b847a6_70.png)

本节课中我们一起学习了Pandas数据处理的基础入门知识。我们首先完成了Pandas库的安装，然后学习了如何导入CSV数据并理解DataFrame结构。接着，我们掌握了多种查看数据基本信息的方法，包括转换时间格式、查看数据形状、列名、索引、随机抽样以及描述性统计。最后，我们重点学习了如何选取指定的行和列，包括直接按列名选取以及使用基于标签的`loc`和基于位置的`iloc`方法。这些是进行后续数据操作和分析的基石。