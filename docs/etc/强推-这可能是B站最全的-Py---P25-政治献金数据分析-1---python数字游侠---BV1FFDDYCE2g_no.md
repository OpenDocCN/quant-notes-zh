# Python金融量化+业务数据分析：P25：政治献金数据分析~1 📊

![](img/12f821d64911a8f2abcad3bb0e536b64_1.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_3.png)

在本节课中，我们将学习如何使用Python的Pandas库，对2012年美国大选的政治献金数据集进行数据分析。我们将从数据加载开始，逐步完成数据清洗、异常值处理、特征工程、分组统计等多个任务，最终得出一些有趣的结论。

![](img/12f821d64911a8f2abcad3bb0e536b64_5.png)

---

![](img/12f821d64911a8f2abcad3bb0e536b64_7.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_9.png)

## 数据加载与初步观察

![](img/12f821d64911a8f2abcad3bb0e536b64_11.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_13.png)

首先，我们需要加载数据。数据文件位于当前目录的 `data/US_election.txt` 中。

```python
import pandas as pd
df = pd.read_csv('./data/US_election.txt')
```

加载完成后，我们查看一下数据的基本结构和前几行，以了解数据包含哪些列。

![](img/12f821d64911a8f2abcad3bb0e536b64_15.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_17.png)

```python
df.head()
```

![](img/12f821d64911a8f2abcad3bb0e536b64_19.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_21.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_23.png)

数据包含多列，其中我们需要重点关注的几列是：
*   `cand_nm`：候选人姓名
*   `contbr_nm`：捐赠者姓名
*   `contbr_st`：捐赠者所在州
*   `contbr_employer`：捐赠者公司/雇主
*   `contbr_occupation`：捐赠者职业
*   `contb_receipt_amt`：捐赠金额
*   `contb_receipt_dt`：捐赠日期

![](img/12f821d64911a8f2abcad3bb0e536b64_25.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_27.png)

理解这些列的含义是后续分析的基础。例如，捐赠金额和候选人信息可以帮助我们分析各党派的支持情况。

![](img/12f821d64911a8f2abcad3bb0e536b64_29.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_31.png)

---

![](img/12f821d64911a8f2abcad3bb0e536b64_33.png)

## 数据总览与缺失值检查

![](img/12f821d64911a8f2abcad3bb0e536b64_35.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_37.png)

上一节我们加载了数据，本节中我们来看看数据的整体情况，并检查是否存在缺失值。

![](img/12f821d64911a8f2abcad3bb0e536b64_39.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_41.png)

我们可以使用 `info()` 方法来快速查看数据的基本信息，包括每列的非空值数量。

![](img/12f821d64911a8f2abcad3bb0e536b64_43.png)

```python
df.info()
```

![](img/12f821d64911a8f2abcad3bb0e536b64_45.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_47.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_49.png)

从输出结果可以看到，总共有 436041 条记录。通过对比各列的非空值数量与总记录数，我们可以发现 `contbr_city`、`contbr_employer` 等列存在缺失值。

![](img/12f821d64911a8f2abcad3bb0e536b64_51.png)

另一种更直观的方法是使用 `isnull().any()`。

![](img/12f821d64911a8f2abcad3bb0e536b64_53.png)

```python
df.isnull().any()
```

![](img/12f821d64911a8f2abcad3bb0e536b64_55.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_57.png)

以下是存在缺失值的列：
*   `contbr_city`
*   `contbr_st`
*   `contbr_employer`
*   `contbr_occupation`

![](img/12f821d64911a8f2abcad3bb0e536b64_59.png)

---

![](img/12f821d64911a8f2abcad3bb0e536b64_61.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_63.png)

## 缺失值处理

![](img/12f821d64911a8f2abcad3bb0e536b64_65.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_67.png)

发现了缺失值后，我们需要进行处理。根据需求，我们将所有缺失值填充为字符串 “NOT PROVIDE”。

![](img/12f821d64911a8f2abcad3bb0e536b64_69.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_71.png)

我们可以使用 `fillna()` 方法一次性填充所有缺失值。

![](img/12f821d64911a8f2abcad3bb0e536b64_73.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_75.png)

```python
df.fillna(value='NOT PROVIDE', inplace=True)
```

![](img/12f821d64911a8f2abcad3bb0e536b64_77.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_79.png)

填充完成后，我们再次使用 `info()` 检查，确认所有列的非空值数量都已变为 436041，说明缺失值已被成功处理。

![](img/12f821d64911a8f2abcad3bb0e536b64_81.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_83.png)

```python
df.info()
```

![](img/12f821d64911a8f2abcad3bb0e536b64_85.png)

---

![](img/12f821d64911a8f2abcad3bb0e536b64_87.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_89.png)

## 异常值处理

![](img/12f821d64911a8f2abcad3bb0e536b64_91.png)

处理完缺失值，我们接着处理数据中的异常值。具体来说，我们需要删除捐赠金额小于或等于0的记录，因为这在业务逻辑上是不合理的。

![](img/12f821d64911a8f2abcad3bb0e536b64_93.png)

首先，我们找出 `contb_receipt_amt` 列中值小于等于0的所有行。

![](img/12f821d64911a8f2abcad3bb0e536b64_95.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_97.png)

```python
# 找出异常数据的行索引
abnormal_indexes = df[df['contb_receipt_amt'] <= 0].index
```

![](img/12f821d64911a8f2abcad3bb0e536b64_99.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_101.png)

然后，使用 `drop()` 方法，根据这些行索引从原始数据中删除这些异常行。

![](img/12f821d64911a8f2abcad3bb0e536b64_103.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_105.png)

```python
df.drop(labels=abnormal_indexes, axis=0, inplace=True)
```

![](img/12f821d64911a8f2abcad3bb0e536b64_107.png)

操作完成后，数据中就不再包含捐赠金额非正的记录了。

![](img/12f821d64911a8f2abcad3bb0e536b64_109.png)

---

![](img/12f821d64911a8f2abcad3bb0e536b64_111.png)

## 新增党派信息列

![](img/12f821d64911a8f2abcad3bb0e536b64_113.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_115.png)

原始数据中没有候选人所属党派的信息。我们需要根据已知的映射关系，为每位候选人添加其党派信息。

![](img/12f821d64911a8f2abcad3bb0e536b64_117.png)

首先，我们创建一个字典，将候选人姓名映射到其所属党派。

![](img/12f821d64911a8f2abcad3bb0e536b64_119.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_121.png)

```python
parties = {
    'Bachmann, Michelle': 'Republican',
    'Romney, Mitt': 'Republican',
    'Obama, Barack': 'Democrat',
    # ... 其他候选人映射关系
}
```

![](img/12f821d64911a8f2abcad3bb0e536b64_123.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_125.png)

接着，我们使用 `map()` 方法，根据 `cand_nm` 列和 `parties` 字典，为每一行数据映射出对应的党派，并将结果赋值给一个新的列 `party`。

![](img/12f821d64911a8f2abcad3bb0e536b64_127.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_129.png)

```python
df['party'] = df['cand_nm'].map(parties)
```

![](img/12f821d64911a8f2abcad3bb0e536b64_131.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_133.png)

现在，数据框中就新增了 `party` 列。

---

![](img/12f821d64911a8f2abcad3bb0e536b64_135.png)

## 数据探索与分析

![](img/12f821d64911a8f2abcad3bb0e536b64_137.png)

现在数据已经准备就绪，我们可以开始进行一些探索性分析了。

![](img/12f821d64911a8f2abcad3bb0e536b64_139.png)

### 查看党派构成

首先，我们看看数据中涉及哪些不同的党派。

![](img/12f821d64911a8f2abcad3bb0e536b64_141.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_143.png)

```python
df['party'].unique()
```

![](img/12f821d64911a8f2abcad3bb0e536b64_145.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_147.png)

### 统计各党派出现次数

接着，我们统计每个党派在数据中出现的次数，这可以粗略反映各党派收到的捐赠事件数量。

![](img/12f821d64911a8f2abcad3bb0e536b64_149.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_151.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_153.png)

```python
df['party'].value_counts()
```

### 统计各党派收到捐款总额

![](img/12f821d64911a8f2abcad3bb0e536b64_155.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_157.png)

仅看捐赠次数还不够，我们更关心各党派收到的捐款总额。这需要使用分组聚合操作。

![](img/12f821d64911a8f2abcad3bb0e536b64_159.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_161.png)

```python
# 按党派分组，并对捐赠金额求和
party_total = df.groupby(by='party')['contb_receipt_amt'].sum()
party_total
```

### 按日期和党派统计捐款总额

![](img/12f821d64911a8f2abcad3bb0e536b64_163.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_165.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_167.png)

我们还可以进行更细粒度的分析，例如查看每天各个党派收到的捐款总额。这是一个多条件的分组聚合。

![](img/12f821d64911a8f2abcad3bb0e536b64_169.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_171.png)

```python
# 按捐赠日期和党派进行分组，并对金额求和
daily_party_total = df.groupby(by=['contb_receipt_dt', 'party'])['contb_receipt_amt'].sum()
daily_party_total.head()
```

![](img/12f821d64911a8f2abcad3bb0e536b64_173.png)

---

![](img/12f821d64911a8f2abcad3bb0e536b64_175.png)

## 数据格式转换

![](img/12f821d64911a8f2abcad3bb0e536b64_177.png)

原始数据中的日期格式是 “日-月-年” 的英文格式（如 `01-JUN-12`）。我们需要将其转换为中文常用的 “年-月-日” 数字格式（如 `2012-06-01`）。

![](img/12f821d64911a8f2abcad3bb0e536b64_179.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_181.png)

我们可以定义一个转换函数，然后使用 `map()` 方法将其应用到日期列上。

![](img/12f821d64911a8f2abcad3bb0e536b64_183.png)

首先，定义一个月份名称到数字的映射字典。

![](img/12f821d64911a8f2abcad3bb0e536b64_185.png)

```python
month_map = {
    'JAN': '01', 'FEB': '02', 'MAR': '03', 'APR': '04',
    'MAY': '05', 'JUN': '06', 'JUL': '07', 'AUG': '08',
    'SEP': '09', 'OCT': '10', 'NOV': '11', 'DEC': '12'
}
```

![](img/12f821d64911a8f2abcad3bb0e536b64_187.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_189.png)

然后，定义转换函数并使用 `map()`。

![](img/12f821d64911a8f2abcad3bb0e536b64_191.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_193.png)

```python
def transform_date(d):
    day, month, year = d.split('-')
    # 将两位年份转换为四位年份
    full_year = '20' + year
    num_month = month_map[month]
    return f'{full_year}-{num_month}-{day}'

![](img/12f821d64911a8f2abcad3bb0e536b64_195.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_197.png)

df['contb_receipt_dt'] = df['contb_receipt_dt'].map(transform_date)
```

![](img/12f821d64911a8f2abcad3bb0e536b64_199.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_201.png)

转换后，日期列就变成了我们熟悉的格式。

![](img/12f821d64911a8f2abcad3bb0e536b64_203.png)

---

## 深入分析：特定职业的支持倾向

![](img/12f821d64911a8f2abcad3bb0e536b64_205.png)

最后，我们来完成一个具体的分析需求：找出退伍老兵（`contbr_occupation` 为 `RETIRED`）这个群体最支持的候选人是谁。逻辑是，他们给谁捐的钱最多，就最支持谁。

![](img/12f821d64911a8f2abcad3bb0e536b64_207.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_208.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_210.png)

首先，筛选出职业为 “RETIRED” 的所有行数据。

![](img/12f821d64911a8f2abcad3bb0e536b64_212.png)

```python
df_veteran = df[df['contbr_occupation'] == 'RETIRED']
```

![](img/12f821d64911a8f2abcad3bb0e536b64_214.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_216.png)

然后，对这些数据按候选人进行分组，并计算每位候选人从老兵那里收到的捐款总额。

![](img/12f821d64911a8f2abcad3bb0e536b64_218.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_220.png)

```python
veteran_support = df_veteran.groupby(by='cand_nm')['contb_receipt_amt'].sum()
veteran_support
```

捐款总额最高的候选人，就是退伍老兵群体最支持的人。

![](img/12f821d64911a8f2abcad3bb0e536b64_222.png)

![](img/12f821d64911a8f2abcad3bb0e536b64_224.png)

---

![](img/12f821d64911a8f2abcad3bb0e536b64_226.png)

## 总结

![](img/12f821d64911a8f2abcad3bb0e536b64_228.png)

本节课中我们一起学习了如何对一个真实的政治献金数据集进行完整的分析。我们实践了以下核心技能：
1.  **数据加载与观察**：使用 `pd.read_csv()` 和 `head()`、`info()`。
2.  **缺失值处理**：使用 `fillna()` 进行填充。
3.  **异常值处理**：通过布尔索引定位并 `drop()` 异常行。
4.  **特征工程**：使用 `map()` 方法根据字典映射创建新列。
5.  **数据探索**：使用 `unique()`、`value_counts()` 了解数据分布。
6.  **数据聚合**：使用 `groupby()` 进行单条件或多条件分组，并结合 `sum()` 等聚合函数进行分析。
7.  **数据转换**：使用 `map()` 配合自定义函数，对某一列数据进行格式转换。
8.  **针对性分析**：结合布尔筛选和分组聚合，回答具体的业务问题。

![](img/12f821d64911a8f2abcad3bb0e536b64_230.png)

通过这个项目，我们综合运用了Pandas中数据清洗、转换和分析的核心功能，为后续更复杂的数据分析任务打下了坚实基础。