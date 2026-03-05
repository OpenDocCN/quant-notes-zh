# Python金融量化与业务数据分析：P24：pandas高级操作-透视表与交叉表 📊

![](img/b7f33f58209d17ec807b3239942d5aff_1.png)

![](img/b7f33f58209d17ec807b3239942d5aff_3.png)

![](img/b7f33f58209d17ec807b3239942d5aff_5.png)

在本节课中，我们将学习pandas库中的两个高级数据分析工具：透视表和交叉表。它们是进行数据分类汇总和制作报表的强大神器。

![](img/b7f33f58209d17ec807b3239942d5aff_7.png)

![](img/b7f33f58209d17ec807b3239942d5aff_9.png)

## 数据加载回顾 🔄

![](img/b7f33f58209d17ec807b3239942d5aff_11.png)

![](img/b7f33f58209d17ec807b3239942d5aff_13.png)

![](img/b7f33f58209d17ec807b3239942d5aff_15.png)

在讲解透视表和交叉表之前，我们先回顾一下数据加载的几个要点，因为这里有几个细节需要注意。

![](img/b7f33f58209d17ec807b3239942d5aff_17.png)

![](img/b7f33f58209d17ec807b3239942d5aff_19.png)

首先，我们来看一个文本文件 `data/type-.txt`，其内容如下：
```
你好-我好-他也好
张三-李四-王五
A-B-C
```

我们的第一个需求是将这个数据读取到DataFrame中。如果直接使用 `pd.read_csv`，结果可能不符合预期。

![](img/b7f33f58209d17ec807b3239942d5aff_21.png)

![](img/b7f33f58209d17ec807b3239942d5aff_23.png)

以下是读取和处理的步骤：

![](img/b7f33f58209d17ec807b3239942d5aff_25.png)

```python
import pandas as pd

![](img/b7f33f58209d17ec807b3239942d5aff_27.png)

![](img/b7f33f58209d17ec807b3239942d5aff_29.png)

# 初始读取，结果不理想
df_initial = pd.read_csv(‘data/type-.txt’)
print(df_initial.shape)  # 输出形状
print(df_initial)

![](img/b7f33f58209d17ec807b3239942d5aff_31.png)

# 正确读取方式
df_correct = pd.read_csv(‘data/type-.txt’, header=None, sep=‘-’)
print(df_correct.shape)  # 输出 (3, 3)
print(df_correct)
```

![](img/b7f33f58209d17ec807b3239942d5aff_33.png)

![](img/b7f33f58209d17ec807b3239942d5aff_35.png)

**关键参数说明**：
*   `header=None`：指定文件第一行不作为列名，而是作为数据。
*   `sep=‘-’`：指定列之间的分隔符为减号 `-`。

![](img/b7f33f58209d17ec807b3239942d5aff_37.png)

![](img/b7f33f58209d17ec807b3239942d5aff_39.png)

![](img/b7f33f58209d17ec807b3239942d5aff_41.png)

通过这两个参数，我们成功将数据读取为一个3行3列的DataFrame。

![](img/b7f33f58209d17ec807b3239942d5aff_43.png)

## 从数据库加载数据 🗃️

![](img/b7f33f58209d17ec807b3239942d5aff_45.png)

![](img/b7f33f58209d17ec807b3239942d5aff_47.png)

接下来，我们看看如何从数据库中加载数据。这里以SQLite数据库为例，其他数据库（如MySQL）原理类似。

![](img/b7f33f58209d17ec807b3239942d5aff_49.png)

以下是操作步骤：

![](img/b7f33f58209d17ec807b3239942d5aff_51.png)

![](img/b7f33f58209d17ec807b3239942d5aff_53.png)

![](img/b7f33f58209d17ec807b3239942d5aff_55.png)

```python
import sqlite3
import pandas as pd

# 1. 创建数据库连接对象
conn = sqlite3.connect(‘data/weather_2012.sqlite’)

![](img/b7f33f58209d17ec807b3239942d5aff_57.png)

![](img/b7f33f58209d17ec807b3239942d5aff_59.png)

# 2. 从数据库读取数据到DataFrame
sql_query = ‘SELECT * FROM weather_table’
df_from_db = pd.read_sql(sql_query, conn)
print(df_from_db.head())

![](img/b7f33f58209d17ec807b3239942d5aff_61.png)

![](img/b7f33f58209d17ec807b3239942d5aff_63.png)

# 3. 将DataFrame数据写回数据库
df_from_db.to_sql(‘sql_data456’, conn, if_exists=‘replace’, index=False)

![](img/b7f33f58209d17ec807b3239942d5aff_65.png)

![](img/b7f33f58209d17ec807b3239942d5aff_67.png)

![](img/b7f33f58209d17ec807b3239942d5aff_69.png)

# 4. 验证写入是否成功
df_check = pd.read_sql(‘SELECT * FROM sql_data456’, conn)
print(df_check.head())

![](img/b7f33f58209d17ec807b3239942d5aff_71.png)

![](img/b7f33f58209d17ec807b3239942d5aff_73.png)

# 关闭连接
conn.close()
```

![](img/b7f33f58209d17ec807b3239942d5aff_75.png)

**注意事项**：
*   不同数据库（如MySQL）需要使用对应的库（如`pymysql`）来创建连接对象。
*   若要将DataFrame写入MySQL，数据库引擎通常需要是`sqlalchemy`，否则可能写入失败。

## 透视表详解 🎯

![](img/b7f33f58209d17ec807b3239942d5aff_77.png)

![](img/b7f33f58209d17ec807b3239942d5aff_79.png)

![](img/b7f33f58209d17ec807b3239942d5aff_81.png)

透视表是一种可以对数据动态排布并进行分类汇总的技术，是制作报表的强大工具。在pandas中，我们使用 `pivot_table` 方法来实现。

![](img/b7f33f58209d17ec807b3239942d5aff_83.png)

![](img/b7f33f58209d17ec807b3239942d5aff_85.png)

![](img/b7f33f58209d17ec807b3239942d5aff_87.png)

首先，我们加载一组篮球比赛数据作为示例：

![](img/b7f33f58209d17ec807b3239942d5aff_89.png)

![](img/b7f33f58209d17ec807b3239942d5aff_91.png)

```python
df = pd.read_csv(‘basketball_data.csv’)
print(df.head())
print(df.shape)
```

![](img/b7f33f58209d17ec807b3239942d5aff_93.png)

### `pivot_table` 核心参数

![](img/b7f33f58209d17ec807b3239942d5aff_95.png)

![](img/b7f33f58209d17ec807b3239942d5aff_97.png)

`pivot_table` 方法有四个核心参数，理解它们就能熟练使用透视表。

![](img/b7f33f58209d17ec807b3239942d5aff_99.png)

**1. `index`：分类汇总的条件**

![](img/b7f33f58209d17ec807b3239942d5aff_101.png)

![](img/b7f33f58209d17ec807b3239942d5aff_103.png)

`index` 参数用于指定按哪些列进行分组，类似于 `group by` 的条件。

![](img/b7f33f58209d17ec807b3239942d5aff_105.png)

![](img/b7f33f58209d17ec807b3239942d5aff_107.png)

![](img/b7f33f58209d17ec807b3239942d5aff_109.png)

例如，我们想查看“哈登队”对阵不同对手时，在主客场下的各项数据：

![](img/b7f33f58209d17ec807b3239942d5aff_111.png)

![](img/b7f33f58209d17ec807b3239942d5aff_113.png)

```python
# 按‘对手’和‘主客场’分类，查看所有数值列的均值
pivot1 = df.pivot_table(index=[‘对手‘, ’主客场‘])
print(pivot1)
```

![](img/b7f33f58209d17ec807b3239942d5aff_115.png)

![](img/b7f33f58209d17ec807b3239942d5aff_117.png)

![](img/b7f33f58209d17ec807b3239942d5aff_119.png)

**2. `values`：指定要计算的列**

![](img/b7f33f58209d17ec807b3239942d5aff_121.png)

![](img/b7f33f58209d17ec807b3239942d5aff_123.png)

`values` 参数用于筛选需要参与计算的数据列。

![](img/b7f33f58209d17ec807b3239942d5aff_125.png)

![](img/b7f33f58209d17ec807b3239942d5aff_127.png)

例如，我们只想看“哈登队”在主客场和不同胜负情况下的得分、篮板、助攻三项数据：

![](img/b7f33f58209d17ec807b3239942d5aff_129.png)

```python
# 只计算‘得分‘、’篮板‘、’助攻‘的均值
pivot2 = df.pivot_table(index=[‘主客场‘, ’胜负‘], values=[‘得分‘, ’篮板‘, ’助攻‘])
print(pivot2)
```

![](img/b7f33f58209d17ec807b3239942d5aff_131.png)

![](img/b7f33f58209d17ec807b3239942d5aff_133.png)

![](img/b7f33f58209d17ec807b3239942d5aff_135.png)

**3. `aggfunc`：设置聚合函数**

![](img/b7f33f58209d17ec807b3239942d5aff_137.png)

`aggfunc` 参数用于设置数据聚合的方式，默认为求均值 `‘mean’`。

![](img/b7f33f58209d17ec807b3239942d5aff_139.png)

![](img/b7f33f58209d17ec807b3239942d5aff_141.png)

例如，我们想计算“哈登队”在主客场和不同胜负情况下的总得分、总篮板、总助攻：

![](img/b7f33f58209d17ec807b3239942d5aff_143.png)

![](img/b7f33f58209d17ec807b3239942d5aff_145.png)

```python
# 计算‘得分‘、’篮板‘、’助攻‘的总和
pivot3 = df.pivot_table(index=[‘主客场‘, ’胜负‘],
                         values=[‘得分‘, ’篮板‘, ’助攻‘],
                         aggfunc=‘sum’)
print(pivot3)
```

**4. `columns`：对值进行列方向的再分类**

![](img/b7f33f58209d17ec807b3239942d5aff_147.png)

![](img/b7f33f58209d17ec807b3239942d5aff_149.png)

`columns` 参数可以在已有分类的基础上，对 `values` 进行更细致的列方向分类。

![](img/b7f33f58209d17ec807b3239942d5aff_151.png)

![](img/b7f33f58209d17ec807b3239942d5aff_153.png)

例如，我们想获取每个对手在主客场的总得分：
*   首先，获取所有队主客场的总得分。
*   然后，在此基础上，按‘对手’进行细分。

![](img/b7f33f58209d17ec807b3239942d5aff_155.png)

![](img/b7f33f58209d17ec807b3239942d5aff_157.png)

```python
# 所有队主客场总得分
pivot4_all = df.pivot_table(index=‘主客场‘, values=‘得分‘, aggfunc=‘sum’)
print(pivot4_all)

# 每个队主客场总得分
pivot4_each = df.pivot_table(index=‘主客场‘,
                              values=‘得分‘,
                              columns=‘对手‘, # 对‘得分’按‘对手’再分类
                              aggfunc=‘sum’,
                              fill_value=0) # 用0填充NaN值
print(pivot4_each)
```

![](img/b7f33f58209d17ec807b3239942d5aff_159.png)

![](img/b7f33f58209d17ec807b3239942d5aff_161.png)

**总结一下**：`index` 是行分类条件，`columns` 是列分类条件，`values` 是要计算的数据，`aggfunc` 是计算方式。它们共同构成了一个多维度的数据汇总视图。

![](img/b7f33f58209d17ec807b3239942d5aff_163.png)

## 交叉表简介 ⚔️

![](img/b7f33f58209d17ec807b3239942d5aff_165.png)

![](img/b7f33f58209d17ec807b3239942d5aff_167.png)

交叉表是透视表的一种特殊形式，专门用于计算分组频率，非常适合分析分类数据之间的关系。我们使用 `crosstab` 方法。

![](img/b7f33f58209d17ec807b3239942d5aff_169.png)

![](img/b7f33f58209d17ec807b3239942d5aff_171.png)

假设我们有一个包含性别、年龄、是否抽烟、身高的数据集 `df_survey`：

![](img/b7f33f58209d17ec807b3239942d5aff_173.png)

![](img/b7f33f58209d17ec807b3239942d5aff_175.png)

```python
print(df_survey.head())
```

![](img/b7f33f58209d17ec807b3239942d5aff_177.png)

![](img/b7f33f58209d17ec807b3239942d5aff_179.png)

以下是交叉表的应用示例：

![](img/b7f33f58209d17ec807b3239942d5aff_181.png)

![](img/b7f33f58209d17ec807b3239942d5aff_183.png)

![](img/b7f33f58209d17ec807b3239942d5aff_185.png)

```python
# 计算各个性别下抽烟与不抽烟的人数
cross1 = pd.crosstab(index=df_survey[‘smoke‘],  # 行索引：是否抽烟
                      columns=df_survey[‘sex‘]) # 列索引：性别
print(cross1)

![](img/b7f33f58209d17ec807b3239942d5aff_187.png)

# 计算各个年龄下抽烟与不抽烟的人数
cross2 = pd.crosstab(index=df_survey[‘smoke‘],
                      columns=df_survey[‘age‘])
print(cross2)
```

![](img/b7f33f58209d17ec807b3239942d5aff_189.png)

`crosstab` 的前两个参数 `index` 和 `columns` 分别指定了构成交叉表的行和列的分类依据，默认计算的是频数。

![](img/b7f33f58209d17ec807b3239942d5aff_191.png)

![](img/b7f33f58209d17ec807b3239942d5aff_193.png)

## 课程总结 📝

![](img/b7f33f58209d17ec807b3239942d5aff_195.png)

![](img/b7f33f58209d17ec807b3239942d5aff_197.png)

本节课我们一起学习了pandas中两个强大的数据汇总工具：
1.  **透视表**：通过 `pivot_table` 方法，使用 `index`, `values`, `aggfunc`, `columns` 四个核心参数，可以实现灵活的多维度数据分类汇总。
2.  **交叉表**：通过 `crosstab` 方法，可以方便地计算分类变量之间的频数分布。

![](img/b7f33f58209d17ec807b3239942d5aff_199.png)

![](img/b7f33f58209d17ec807b3239942d5aff_201.png)

![](img/b7f33f58209d17ec807b3239942d5aff_203.png)

结合之前学习的 `groupby`（分组聚合）和 `map`（映射转换），这些构成了pandas高级数据分析的核心技能。掌握它们，你将能高效地应对各种复杂的数据汇总与洞察任务。

![](img/b7f33f58209d17ec807b3239942d5aff_205.png)

![](img/b7f33f58209d17ec807b3239942d5aff_207.png)

下一节，我们将通过一个实际项目来综合运用这些pandas高级操作。