# 量化交易：Python入门之数据分析【2／4】：2-3. Python数据分析：查询Pandas Series 📊

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_0.png)

在本节课中，我们将要学习如何查询Pandas Series中的数据。我们将介绍使用`.iloc`和`.loc`方法进行精确索引，比较不同查询方式的性能，并了解如何向Series中添加新数据。

---

## 创建与查询Series

首先，我们从一个字典创建一个Pandas Series。这个字典以运动项目作为键（索引），以对应的国家作为值。

```python
import pandas as pd

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_2.png)

sports = {'Tennis': 'USA', 'Baseball': 'Japan', 'Soccer': 'Brazil'}
s = pd.Series(sports)
```

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_4.png)

执行上述代码后，我们得到一个Series `s`，其索引为 `['Tennis', 'Baseball', 'Soccer']`，对应的值为 `['USA', 'Japan', 'Brazil']`。

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_6.png)

上一节我们介绍了如何创建Series，本节中我们来看看如何从中查询特定的值。

以下是两种主要的查询方法：

*   **使用 `.iloc` 按整数位置查询**：`.iloc` 根据元素在Series中的整数位置（从0开始）进行索引。例如，`Brazil` 在第二位（位置索引为1）。
    ```python
    # 查询第二个元素（索引为1）
    value = s.iloc[1]  # 返回 'Brazil'
    ```

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_8.png)

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_10.png)

*   **使用 `.loc` 按索引标签查询**：`.loc` 根据我们定义的索引标签（本例中是运动名称字符串）进行索引。
    ```python
    # 查询索引为 'Soccer' 的元素
    value = s.loc['Soccer']  # 返回 'Brazil'
    # 查询索引为 'Baseball' 的元素
    value = s.loc['Baseball']  # 返回 'Japan'
    ```

**重要提示**：一种古老的查询方式 `s[1]` 或 `s['Soccer']` 虽然可能暂时有效，但在未来的Python版本中已被标记为“弃用”（deprecated）。为了保证代码的长期兼容性，请统一使用 `.iloc` 和 `.loc` 方法。

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_12.png)

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_13.png)

---

## 处理数字索引的Series

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_15.png)

当Series的索引本身就是数字时，使用 `.iloc` 和 `.loc` 可以避免混淆。

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_16.png)

假设我们有以下Series：
```python
s2 = pd.Series([99, 100, 101], index=['USA', 'Japan', 'Brazil'])
```
这个Series的索引是 `['USA', 'Japan', 'Brazil']`，值是 `[99, 100, 101]`。

以下是查询方法对比：

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_18.png)

*   **`.loc` 按标签查询**：传入索引标签。
    ```python
    s2.loc['USA']  # 返回 99
    s2.loc[100]    # 返回 'Japan' (因为100是索引标签之一)
    ```
*   **`.iloc` 按位置查询**：传入整数位置。
    ```python
    s2.iloc[0]  # 返回 99 (第一个元素)
    s2.iloc[1]  # 返回 100 (第二个元素)
    ```

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_20.png)

明确使用 `.loc` 和 `.iloc` 可以清晰地区分你是想按“标签”查找还是按“位置”查找，从而避免潜在的混淆。

---

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_22.png)

## Series的高效计算

在数据分析中，我们经常需要对Series中的所有数值进行计算，例如求和。我们将比较两种方法的效率。

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_24.png)

首先，创建一个简单的数值Series：
```python
s3 = pd.Series([100, 120, 104])
```

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_26.png)

**方法一：使用Python循环**
这是一种直观但效率较低的方法。
```python
total = 0
for item in s3:
    total += item
print(total)  # 输出 324
```

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_28.png)

**方法二：使用NumPy函数**
NumPy是专门为数值计算设计的库，其向量化操作速度极快。
```python
import numpy as np
total_np = np.sum(s3)
print(total_np)  # 输出 324
```

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_30.png)

为了直观展示性能差异，我们用一个包含10，000个随机数的大型Series进行测试：

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_32.png)

```python
import time
# 创建大型Series
large_s = pd.Series(np.random.randint(0, 1000, size=10000))

# 测试循环方法耗时
start = time.time()
summary = 0
for item in large_s:
    summary += item
end = time.time()
print(f"循环耗时: {(end - start) * 1e6:.2f} 微秒")

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_34.png)

# 测试NumPy方法耗时
start = time.time()
summary_np = np.sum(large_s)
end = time.time()
print(f"NumPy耗时: {(end - start) * 1e6:.2f} 微秒")
```

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_36.png)

运行上述代码，你会发现NumPy方法的耗时（约几十微秒）远低于Python循环（约几百微秒）。数据量越大，差距越明显。**结论是：在Pandas/NumPy中进行数值计算时，应优先使用其内置的向量化函数，避免使用Python原生循环。**

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_38.png)

---

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_40.png)

## 修改与添加数据

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_42.png)

最后，我们来看看如何修改已有的Series或向其中添加新数据。这可以通过 `.loc` 方法轻松完成。

假设我们有之前的Series `s2`。如果我们想修改索引 `‘Brazil’` 对应的值，或者添加一个新的索引-值对，可以这样做：

```python
# 修改现有值
s2.loc['Brazil'] = 102
print(s2.loc['Brazil'])  # 输出 102

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_44.png)

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_45.png)

# 添加新数据
s2.loc['England'] = 103
print(s2)
```
执行后，Series `s2` 将包含新添加的索引 `‘England’` 和值 `103`。

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_47.png)

值得注意的是，Pandas Series的索引类型可以混合。即使原来的索引是字符串，我们也可以添加数字作为新的索引，反之亦然。这提供了很大的灵活性，但也需要谨慎使用以保持数据的一致性。
```python
s2.loc[200] = 'Animal'
print(s2)
```

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_49.png)

---

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_50.png)

## 总结

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_52.png)

本节课中我们一起学习了Pandas Series的核心查询与操作方法：

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_54.png)

1.  **精确查询**：使用 `.iloc[位置]` 按整数位置查询，使用 `.loc[标签]` 按索引标签查询。这是推荐的标准做法。
2.  **高效计算**：对Series中的数值进行聚合计算（如求和）时，应优先使用NumPy的向量化函数（如 `np.sum()`），其性能远超Python原生循环。
3.  **数据修改**：可以使用 `.loc[标签] = 新值` 来修改现有数据或添加新的数据行。

![](img/d6b0b6890984dfbb07b02c33c5e63aaf_56.png)

掌握这些基础操作，是后续进行更复杂的数据筛选、清洗和分析的重要前提。