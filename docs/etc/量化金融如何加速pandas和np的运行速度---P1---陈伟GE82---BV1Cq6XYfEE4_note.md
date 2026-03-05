# 量化金融：P1：Pandas与NumPy性能优化核心技巧 🚀

![](img/5b9f34879cc8987361166470b4b2865c_1.png)

在本节课中，我们将学习如何对量化金融、机器学习等领域中最常用的两个数据分析库——Pandas和NumPy——进行有效的性能加速。我们将深入探讨导致代码运行缓慢的核心原因，并提供简单实用的优化策略。

![](img/5b9f34879cc8987361166470b4b2865c_3.png)

## 问题背景与常见误区

![](img/5b9f34879cc8987361166470b4b2865c_5.png)

在量化分析的实际应用中，我们经常需要处理大量的股票行情数据。例如，从接口获取的数据通常以字典形式存储，每只股票包含开盘价、收盘价、最高价、最低价和成交量。为了进行特征分析，我们需要将所有股票的收盘价等数据提取并合并到一个统一的DataFrame中。

![](img/5b9f34879cc8987361166470b4b2865c_7.png)

一个直观的做法是使用`pd.concat()`函数循环拼接数据。然而，这种方法存在严重的性能问题。

![](img/5b9f34879cc8987361166470b4b2865c_9.png)

以下是使用`pd.concat`循环拼接数据的示例代码思路：

![](img/5b9f34879cc8987361166470b4b2865c_11.png)

```python
# 假设 stock_data_dict 是一个包含多只股票数据的字典
combined_df = pd.DataFrame()
for stock_code, data in stock_data_dict.items():
    # 提取单只股票的收盘价序列
    close_series = data['close']
    # 将序列转换为DataFrame，并设置列名为股票代码
    temp_df = close_series.to_frame(name=stock_code)
    # 使用concat进行拼接
    combined_df = pd.concat([combined_df, temp_df], axis=1)
```

运行上述代码时，你会发现处理速度会随着循环次数的增加而显著下降。这是因为`pd.concat`每次都会创建一个全新的DataFrame对象。

## 性能下降的核心原因 🔍

上一节我们介绍了使用`concat`导致性能问题的现象，本节中我们来看看其根本原因。

关键在于理解变量在内存中的“身份”变化。我们可以使用Python内置的`id()`函数来观察。

```python
import pandas as pd
import numpy as np

# 创建两个简单的DataFrame
pd1 = pd.DataFrame({'A': [1, 2]})
pd2 = pd.DataFrame({'B': [3, 4]})

print(f"拼接前 pd1 的 id: {id(pd1)}")

# 使用concat拼接
pd1 = pd.concat([pd1, pd2], axis=1)
print(f"拼接后 pd1 的 id: {id(pd1)}")
```

运行上述代码，你会发现拼接前后的`id(pd1)`值是不同的。这意味着`pd1`在拼接后已经指向了一个全新的内存对象，而不是在原有对象上追加数据。

**核心问题**：`pd.concat`和`np.concatenate`这类操作，会**在内存中创建新的对象并复制所有数据**。随着数据量增大，反复的内存分配和数据复制会消耗大量时间，导致性能急剧下降。

NumPy数组也存在同样的问题：

```python
np1 = np.array([1, 2])
print(f"拼接前 np1 的 id: {id(np1)}")
np1 = np.concatenate([np1, [3, 4]])
print(f"拼接后 np1 的 id: {id(np1)}")
```

## 核心优化策略：避免不必要的数据复制 💡

既然我们找到了性能瓶颈在于内存中数据的反复复制，那么优化策略的核心就是：**在已知最终数据结构的前提下，预先分配好内存，然后进行原地填充或使用高效的数据结构暂存**。

以下是几种有效的优化方法。

### 方法一：预先初始化完整的DataFrame

如果你提前知道最终DataFrame的索引（日期）和列名（股票代码），可以预先创建一个“骨架”，然后直接对每一列进行赋值。

```python
# 假设已知所有日期和股票代码
all_dates = [...] # 日期列表
all_codes = ['000001', '000002', ...] # 股票代码列表

# 预先创建一个空的、结构完整的DataFrame
combined_df = pd.DataFrame(index=all_dates, columns=all_codes)

# 循环填充数据，这是高效的原地操作
for code in all_codes:
    close_data = stock_data_dict[code]['close']
    combined_df[code] = close_data # 直接赋值，不会创建新对象
```

![](img/5b9f34879cc8987361166470b4b2865c_13.png)

这种方法避免了循环中的拼接操作，速度会快很多，并且性能稳定。

### 方法二：利用Python高效的内置数据结构

![](img/5b9f34879cc8987361166470b4b2865c_15.png)

在不确定最终数据大小，或需要动态收集数据的场景下，可以先用Python的`list`（列表）或`dict`（字典）收集数据，最后一次性转换为NumPy数组或DataFrame。

**列表（List）的优势**：
```python
my_list = []
print(f"添加数据前 list 的 id: {id(my_list)}")
my_list.append(1)
my_list.append(2)
print(f"添加数据后 list 的 id: {id(my_list)}")
# id保持不变，说明是高效的原址操作
```

**实际应用示例（构建训练数据集）**：
```python
X_train_list = [] # 用于存放特征
y_train_list = [] # 用于存放标签

for data_chunk in large_dataset:
    features, label = process_data(data_chunk)
    X_train_list.append(features)
    y_train_list.append(label)

# 循环结束后，一次性转换为NumPy数组
X_train = np.array(X_train_list)
y_train = np.array(y_train_list)
```

字典（Dict）也具有类似的原址添加特性，适合用于收集键值对数据。

### 方法三：直接赋值而非拼接

对于Pandas，许多操作可以直接通过赋值完成，这比`concat`更高效。

```python
# 低效做法
df = pd.concat([df, new_row.to_frame().T], ignore_index=True)

# 高效做法 (如果new_row是Series)
df.loc[len(df)] = new_row.values
# 或者使用列表追加再转换
```

## 与其他优化技巧的关系

![](img/5b9f34879cc8987361166470b4b2865c_17.png)

网络上常见的Pandas/NumPy加速技巧，例如优化循环、使用向量化操作（`apply`）、高效读取文件等，主要针对的是**计算逻辑和内存使用效率**。

![](img/5b9f34879cc8987361166470b4b2865c_19.png)

这些技巧确实有用，但在我们讨论的**大数据量反复拼接**场景下，其提升效果有限。本课所讲的“避免内存数据复制”是更深层次、效果更显著的性能优化核心。

当你的代码运行缓慢时，应首先检查是否存在大量`concat`或类似操作。如果存在，优先采用本课介绍的预分配或列表收集法进行重构。

## 总结 📝

本节课中我们一起学习了加速Pandas和NumPy的核心思想。

1.  **性能瓶颈**：使用`pd.concat`或`np.concatenate`进行循环拼接，会导致内存中反复创建新对象和复制数据，这是速度变慢的根本原因。
2.  **核心原理**：通过`id()`函数可以验证变量所指内存对象是否发生变化。优化目标是保持内存对象的稳定性。
3.  **优化策略**：
    *   **预先分配**：在已知数据最终形状时，预先初始化完整的DataFrame或数组，然后进行原地填充。
    *   **使用高效中介**：在动态收集数据时，使用Python的`list`或`dict`暂存，最后一次性转换。
    *   **直接操作**：对DataFrame尽量使用直接赋值（`df[col] = data`）或`.loc`赋值，而非拼接。
4.  **关键心法**：时刻思考你的操作**是否会导致底层数据在内存中被大量、反复地复制**。减少这种复制，就是最有效的加速手段。

![](img/5b9f34879cc8987361166470b4b2865c_21.png)

掌握这一核心思路，你将能从根本上改善处理大规模金融数据或机器学习数据时的代码性能。