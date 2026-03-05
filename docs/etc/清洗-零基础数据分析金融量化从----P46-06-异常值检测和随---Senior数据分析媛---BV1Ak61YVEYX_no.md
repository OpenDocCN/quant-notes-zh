# 数据分析+金融量化+数据清洗：P46：异常值检测与随机排序抽样 📊

在本节课中，我们将学习数据清洗中的两个重要环节：异常值检测与随机排序抽样。异常值检测帮助我们识别并处理数据中不合理的极端值，而随机排序抽样则能让我们获得更自然、无偏的数据样本，为后续分析打下良好基础。

## 异常值检测 🎯

上一节我们介绍了空值过滤，本节中我们来看看如何识别和处理数据中的异常值。异常值，也称为离群点，是指那些明显偏离数据集整体分布规律的数据点。在数据分析中，异常值可能会扭曲统计结果，影响模型的准确性，因此检测和处理它们是数据清洗的必要步骤。

### 什么是异常值？

异常值通常指连续型数据中，超出数据普遍分布范围的值。对于离散型数据，一般不涉及异常值问题。异常值的判定标准有多种，但有几个通用的过滤方案。

### 异常值的判定方法

以下是两种常见的异常值筛选方式：

![](img/4fa3d376a3cbb4775a83d05d684dde50_1.png)

![](img/4fa3d376a3cbb4775a83d05d684dde50_3.png)

![](img/4fa3d376a3cbb4775a83d05d684dde50_5.png)

1.  **基于标准差的方法**：这是国际通用的异常值认定方法之一。对于一个数据集，如果某个数据点 `X` 的绝对值大于该组数据三倍的标准差，则通常将其认定为异常值。其公式表达为：
    **|X| > 3 * σ**，其中 σ 代表数据集的标准差。

2.  **基于箱线图的方法**：箱线图通过几个百分位数（如四分位数）以及上、下边界来识别异常值。超出上边界或下边界的值通常被认定为离群点。我们将在后续讲解数据可视化的章节中详细介绍此方法。

需要注意的是，**基于三倍标准差的方法通常适用于标准正态分布的数据**。如果数据不是标准正态分布，则需要先通过数据转换（如无量纲化处理、归一化等）将其转换为近似标准正态分布，然后再进行异常值检测。这在机器学习建模中是常见的预处理步骤。

### 异常值检测实战

了解了基本逻辑后，我们来看看如何在代码中实现异常值的检测与过滤。核心步骤分为三步：确认筛选条件、获取异常值索引、删除异常数据。

首先，我们导入必要的库并加载示例数据。

```python
import pandas as pd
import numpy as np

# 加载数据
heroes = pd.read_excel('heroes.xlsx')
# 选取部分列进行分析
data = heroes[['name', 'hp_max', 'mp_max', 'attack_max', 'attack_range', 'main']]
```

我们可以使用 `describe()` 方法快速查看数值型数据的基本统计指标。

```python
print(data.describe())
```

要计算某一列（如血量上限 `hp_max`）的标准差，可以直接使用 `std()` 方法。

```python
std_hp = data['hp_max'].std()
print(f"血量上限的标准差为: {std_hp}")
```

![](img/4fa3d376a3cbb4775a83d05d684dde50_7.png)

![](img/4fa3d376a3cbb4775a83d05d684dde50_9.png)

#### 步骤一：确认异常值条件并生成布尔列表

假设我们以“大于三倍标准差”作为异常值条件。对于非标准正态分布的数据，我们需要先进行标准化转换。这里使用 `StandardScaler` 进行演示。

```python
from sklearn.preprocessing import StandardScaler

# 提取需要检测的列并转换为二维数组格式
hp_data = data[['hp_max']].values
# 初始化标准化器并进行转换
scaler = StandardScaler()
hp_scaled = scaler.fit_transform(hp_data)
# 此时 hp_scaled 中的数据近似服从标准正态分布

# 计算转换后数据的标准差
std_scaled = hp_scaled.std()
# 生成异常值判定条件的布尔列表
condition = np.abs(hp_scaled) > 3 * std_scaled
# 将二维布尔数组转换为一维，以便后续筛选
condition = condition.reshape(-1)
```

#### 步骤二：获取异常值的行索引

利用上一步生成的布尔列表，我们可以找到包含异常值的行索引。

```python
# 使用布尔索引筛选出异常值所在的行
outlier_rows = data.loc[condition]
# 获取这些异常行的索引
outlier_index = outlier_rows.index
print(f"异常值的行索引为: {outlier_index.tolist()}")
```

#### 步骤三：根据行索引删除异常数据

最后，使用 `drop` 函数依据索引删除这些异常行。

```python
# 删除包含异常值的行，inplace=True表示在原数据上修改
data_cleaned = data.drop(labels=outlier_index, inplace=False)
print(f"清洗后数据行数: {len(data_cleaned)}， 原始数据行数: {len(data)}")
```

为了更清晰地展示整个过程，我们生成一组包含异常值的模拟数据来演练。

```python
# 生成1000个标准正态分布的随机数作为模拟数据
np.random.seed(42) # 固定随机种子，使结果可复现
sim_data = np.random.randn(1000)
df_sim = pd.DataFrame(sim_data, columns=['value'])

# 直接应用三倍标准差规则
condition_sim = np.abs(df_sim['value']) > 3 * df_sim['value'].std()
print(f"模拟数据中是否存在异常值: {condition_sim.any()}")

# 获取并删除异常值
outlier_index_sim = df_sim.loc[condition_sim].index
df_sim_cleaned = df_sim.drop(outlier_index_sim)
print(f"模拟数据清洗后剩余 {len(df_sim_cleaned)} 条数据。")
```

#### 综合练习：多列异常值过滤

有时我们需要删除那些在**任意列**中包含异常值的整行数据。以下是实现方法：

```python
# 生成一个10000行3列的标准正态分布DataFrame
df_exercise = pd.DataFrame(np.random.randn(10000, 3), columns=['A', 'B', 'C'])

# 步骤1: 判断每个元素是否大于其所在列的三倍标准差
condition_matrix = np.abs(df_exercise) > 3 * df_exercise.std()
# 步骤2: 对每一行进行判断，只要该行有一个True，则整行标记为异常
row_condition = condition_matrix.any(axis=1)
# 步骤3: 获取异常行索引并删除
outlier_idx = df_exercise.loc[row_condition].index
df_exercise_cleaned = df_exercise.drop(outlier_idx)

print(f"原始数据行数: {len(df_exercise)}， 清洗后行数: {len(df_exercise_cleaned)}")
```

## 随机排序与抽样 🔀

在数据分析中，让数据保持一种自然、无序的状态非常重要。有序的数据可能会让我们对整体情况产生误解（例如，只看排序后的前几名会高估平均水平）。此外，当数据集过大时，我们常常需要进行随机抽样来分析数据的基本特征。

Pandas 的 `take` 函数结合 NumPy 的随机函数，可以方便地实现随机排序和抽样。

### 随机排序

随机排序可以打乱数据的行顺序或列顺序，通常用于打乱行顺序以避免潜在的顺序偏差。

```python
# 创建一个示例DataFrame
df = pd.DataFrame(np.random.randint(0, 100, size=(5, 5)), columns=list('ABCDE'))
print("原始数据:")
print(df)

# 使用 np.random.permutation 生成随机行索引序列
random_indices = np.random.permutation(df.index)
print(f"生成的随机行索引: {random_indices}")

# 使用 take 函数按照随机索引重新排列行
df_shuffled = df.take(random_indices)
print("\n随机排序后的数据:")
print(df_shuffled)
```

`take` 函数同样可以用于列方向的重新排列，或者重复选取特定的行/列。

### 随机抽样

当我们需要从大量数据中随机抽取一部分样本进行分析时，可以使用随机抽样。

```python
# 创建一个包含10000行数据的DataFrame
df_large = pd.DataFrame(np.random.randn(10000, 2), columns=['X', 'Y'])

# 设定要抽取的样本数量
sample_size = 5
# 随机生成不重复的样本行索引
random_sample_indices = np.random.choice(df_large.index, size=sample_size, replace=False)
print(f"随机抽取的行索引: {random_sample_indices}")

# 使用 take 函数抽取样本
df_sample = df_large.take(random_sample_indices)
print(f"\n随机抽取的 {sample_size} 条样本数据:")
print(df_sample)
```

通过这种方式，我们可以轻松地从大数据集中获取一个随机的、有代表性的子集进行快速分析或模型训练。

## 总结 📝

本节课中我们一起学习了数据清洗中两个关键的操作。

1.  **异常值检测**：我们理解了异常值（离群点）的概念，重点掌握了基于**三倍标准差（|X| > 3σ）**的国际通用检测方法。我们通过三步流程实现了异常值的过滤：定义条件生成布尔列表、定位异常值索引、使用`drop`函数删除异常行。对于多列数据，我们学会了如何判断任意列包含异常值即删除整行的逻辑。

2.  **随机排序与抽样**：我们认识到让数据保持无序状态的重要性。学习了如何使用 `take` 函数结合 `np.random.permutation` 实现数据的随机重排，以及如何结合 `np.random.choice` 实现从大数据集中的随机抽样，这为后续的统计分析奠定了公平、无偏的数据基础。

![](img/4fa3d376a3cbb4775a83d05d684dde50_11.png)

掌握这些技能，能够使我们的数据集变得更加干净、可靠，从而为后续的金融量化分析与建模提供高质量的数据输入。