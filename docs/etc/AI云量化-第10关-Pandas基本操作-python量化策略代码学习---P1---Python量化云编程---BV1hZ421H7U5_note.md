# AI云量化：第10关：Pandas基本操作 📊

![](img/9de1a313c38094d31be265aebdacedf9_1.png)

在本节课中，我们将要学习Python数据分析的核心库——Pandas的基本操作。Pandas是处理表格数据（如CSV文件、Excel表格）的强大工具，在量化策略开发中至关重要。我们将通过具体的代码案例来掌握其核心功能。

## 概述

Pandas提供了两种主要的数据结构：**Series**（一维数组）和**DataFrame**（二维表格）。本节课将重点介绍DataFrame的创建、数据查看、选择与基本运算。

![](img/9de1a313c38094d31be265aebdacedf9_3.png)

---

![](img/9de1a313c38094d31be265aebdacedf9_4.png)

## 第一节：DataFrame的创建与查看

DataFrame是Pandas中最常用的数据结构，可以将其理解为一个Excel工作表。

![](img/9de1a313c38094d31be265aebdacedf9_6.png)

### 创建DataFrame
我们可以从Python字典或列表等数据结构创建DataFrame。

以下是创建一个简单DataFrame的代码示例：

![](img/9de1a313c38094d31be265aebdacedf9_8.png)

```python
import pandas as pd

![](img/9de1a313c38094d31be265aebdacedf9_10.png)

# 从字典创建DataFrame
data = {
    ‘股票名称‘: [‘腾讯‘, ‘阿里巴巴‘, ‘茅台‘],
    ‘价格‘: [450, 180, 2100],
    ‘成交量‘: [1000000, 800000, 50000]
}
df = pd.DataFrame(data)
print(df)
```

![](img/9de1a313c38094d31be265aebdacedf9_12.png)

运行上述代码，将输出一个包含三行三列的表格。

![](img/9de1a313c38094d31be265aebdacedf9_14.png)

### 查看数据的基本信息
创建DataFrame后，我们需要快速了解数据的概况。

![](img/9de1a313c38094d31be265aebdacedf9_16.png)

以下是几个常用的查看方法：
*   **`df.head()`**: 查看数据的前5行。
*   **`df.tail()`**: 查看数据的后5行。
*   **`df.shape`**: 获取数据的维度（行数，列数）。
*   **`df.info()`**: 查看数据的索引、数据类型和内存信息。
*   **`df.describe()`**: 生成数据的描述性统计摘要（如计数、均值、标准差等）。

---

## 第二节：数据的选择与索引

![](img/9de1a313c38094d31be265aebdacedf9_18.png)

上一节我们介绍了如何创建和查看DataFrame，本节中我们来看看如何从中选取特定的数据。

![](img/9de1a313c38094d31be265aebdacedf9_20.png)

Pandas提供了灵活的数据选择方式，主要分为按列选择、按行选择和按条件选择。

![](img/9de1a313c38094d31be265aebdacedf9_21.png)

### 按列选择数据
选择单列会返回一个Series，选择多列则返回一个新的DataFrame。

![](img/9de1a313c38094d31be265aebdacedf9_22.png)

```python
# 选择单列 ‘价格‘
price_series = df[‘价格‘]
print(price_series)

![](img/9de1a313c38094d31be265aebdacedf9_23.png)

![](img/9de1a313c38094d31be265aebdacedf9_24.png)

# 选择多列 [‘股票名称‘, ‘价格‘]
sub_df = df[[‘股票名称‘, ‘价格‘]]
print(sub_df)
```

![](img/9de1a313c38094d31be265aebdacedf9_25.png)

### 按行选择数据
我们可以使用`.iloc`（基于整数位置）和`.loc`（基于标签）进行行选择。

![](img/9de1a313c38094d31be265aebdacedf9_26.png)

以下是两种方法的示例：
*   **使用 `.iloc`**：通过行号索引。
    ```python
    # 选择第一行（索引为0）
    first_row = df.iloc[0]
    # 选择前两行
    first_two_rows = df.iloc[0:2]
    ```
*   **使用 `.loc`**：通过索引标签选择。默认情况下，行标签就是行号。
    ```python
    # 选择索引标签为0的行
    row_by_label = df.loc[0]
    ```

![](img/9de1a313c38094d31be265aebdacedf9_27.png)

### 按条件选择数据（布尔索引）
这是非常强大的功能，可以根据数据的值来筛选行。

![](img/9de1a313c38094d31be265aebdacedf9_28.png)

```python
# 筛选出价格大于200的股票
high_price_stocks = df[df[‘价格‘] > 200]
print(high_price_stocks)
```
这里的 `df[‘价格‘] > 200` 会生成一个布尔值Series，Pandas会据此返回值为True的行。

![](img/9de1a313c38094d31be265aebdacedf9_29.png)

---

![](img/9de1a313c38094d31be265aebdacedf9_31.png)

## 第三节：基本数据运算与处理

掌握了数据选择后，我们可以对数据进行简单的运算和处理，为后续分析做准备。

![](img/9de1a313c38094d31be265aebdacedf9_33.png)

### 列的基本运算
我们可以像操作普通数字一样对DataFrame的列进行运算。

![](img/9de1a313c38094d31be265aebdacedf9_34.png)

```python
# 计算成交额（价格 * 成交量）
df[‘成交额‘] = df[‘价格‘] * df[‘成交量‘]
print(df)
```

![](img/9de1a313c38094d31be265aebdacedf9_35.png)

### 处理缺失值
真实数据中常有缺失值，用`NaN`表示。Pandas提供了一些处理方法。

![](img/9de1a313c38094d31be265aebdacedf9_36.png)

![](img/9de1a313c38094d31be265aebdacedf9_37.png)

以下是处理缺失值的常见操作：
*   **`df.isnull()`**: 检查每个元素是否缺失。
*   **`df.dropna()`**: 删除包含缺失值的行或列。
*   **`df.fillna(value)`**: 用指定值填充缺失值。

![](img/9de1a313c38094d31be265aebdacedf9_38.png)

例如，用0填充所有缺失值：
```python
df_filled = df.fillna(0)
```

![](img/9de1a313c38094d31be265aebdacedf9_39.png)

---

![](img/9de1a313c38094d31be265aebdacedf9_40.png)

## 总结

![](img/9de1a313c38094d31be265aebdacedf9_42.png)

本节课中我们一起学习了Pandas库的基本操作。我们首先学会了如何创建和查看DataFrame，然后掌握了按列、按行以及按条件选择数据的关键技巧，最后简单了解了数据运算和缺失值处理。这些是使用Pandas进行数据分析的基石，请务必通过练习巩固。

![](img/9de1a313c38094d31be265aebdacedf9_43.png)

---

![](img/9de1a313c38094d31be265aebdacedf9_44.png)

## 学习支持与资源

本课程配备了丰富的代码案例。学习过程中如有疑问，可利用以下资源：
*   使用右下角的“I云笔记”功能，内含AI大模型辅助解答。
*   练习题附有辅助答案可供参考。
*   课程网站提供了量化基础、证券数学、计算机等更多知识，以及丰富的代码案例库。
*   无需本地安装，可直接在线编写和运行代码。

![](img/9de1a313c38094d31be265aebdacedf9_46.png)

关注课程更新，挑战14天学会Python入门。明天我们将进入第11关：文件读写操作。