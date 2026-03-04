# 数据分析+金融量化+数据清洗：P47：07 分组表交叉表透视表 📊

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_1.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_3.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_5.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_7.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_9.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_10.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_11.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_12.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_14.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_16.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_18.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_20.png)

在本节课中，我们将要学习数据分析中一个非常核心的操作：**分组处理**。我们会从基础的分组概念讲起，逐步深入到如何使用Pandas进行分组、聚合，以及如何利用交叉表和透视表进行更高级的数据汇总分析。这些技能是进行数据探索和金融量化分析的基础。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_22.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_24.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_26.png)

## 什么是分组处理？ 🤔

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_28.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_30.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_32.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_34.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_36.png)

上一节我们介绍了数据清洗的基本操作，本节中我们来看看如何对数据进行分类汇总。分组处理，顾名思义，就是根据某个或某些条件将数据分成不同的组，然后对每个组内的数据进行计算（如求和、平均值、最大值等）。这与数据库中的`GROUP BY`操作原理相同。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_38.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_40.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_42.png)

例如，我们有一个包含游戏英雄信息的数据集`data`，其中包含`attack_range`（攻击范围，分为“近战”和“远程”）和`hp_max`（最大生命值）等字段。如果我们想分别计算近战英雄和远程英雄的平均最大生命值，就需要用到分组处理。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_44.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_46.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_48.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_50.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_52.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_54.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_56.png)

## 基础分组与聚合 🛠️

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_58.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_60.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_62.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_63.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_65.png)

在Pandas中，我们使用`groupby()`方法进行分组。分组后通常会伴随聚合操作，以计算每个组的统计指标。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_67.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_68.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_70.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_72.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_74.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_76.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_78.png)

以下是实现分组聚合的基本语法：
```python
# 根据‘attack_range’列进行分组
group_obj = data.groupby(‘attack_range‘)
# 查看分组情况
print(group_obj.groups)
# 对分组后的‘hp_max’列求平均值
avg_hp = group_obj[‘hp_max‘].mean()
```

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_79.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_81.png)

我们可以通过`agg()`函数进行更灵活的聚合操作，例如同时计算不同列的不同统计量：
```python
# 自定义聚合：计算‘hp_max’的平均值和‘mp_max’的最大值
custom_agg = data.groupby(‘attack_range‘).agg({‘hp_max‘: ‘mean‘, ‘mp_max‘: ‘max‘})
```

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_83.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_85.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_87.png)

## 练习：按职业分组计算 📝

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_89.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_91.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_93.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_95.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_97.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_99.png)

现在，让我们通过一个练习来巩固所学知识。假设我们想根据英雄的`role`（职业）进行分组，并计算每个职业的平均最大生命值`hp_max`和最大攻击力`attack_max`。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_101.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_103.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_105.png)

以下是实现方法：
```python
result = data.groupby(‘role‘).agg({‘hp_max‘: ‘mean‘, ‘attack_max‘: ‘max‘})
print(result)
```

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_107.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_109.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_111.png)

## 多层级分组 🔗

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_113.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_115.png)

分组条件可以是多个，这被称为多层级分组。例如，我们可以先按`attack_range`分组，再在每个组内按`role`进行细分。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_117.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_119.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_121.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_123.png)

实现多层级分组只需在`groupby()`中传入一个列名列表：
```python
multi_group = data.groupby([‘attack_range‘, ‘role‘])[‘hp_max‘].mean()
print(multi_group)
```
这将生成一个具有多层索引的Series，清晰地展示了“近战-坦克”、“远程-法师”等组合下的平均生命值。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_125.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_127.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_129.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_131.png)

## 高级聚合：使用自定义函数 ⚙️

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_133.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_134.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_135.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_136.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_138.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_140.png)

当内置的聚合函数（如`mean`, `max`）无法满足需求时，我们可以使用`apply()`或`transform()`方法传入自定义函数。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_142.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_144.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_146.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_148.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_150.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_152.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_154.png)

**使用`apply()`方法：**
`apply()`会将每个分组作为一个Series（或DataFrame）传递给自定义函数，函数需要返回一个标量值（即该组的聚合结果）。
```python
def custom_max(x):
    # x 是一个分组（Series）
    return x.max()

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_156.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_158.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_160.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_162.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_164.png)

# 使用apply计算每个分组的最大值
group_max = data.groupby(‘attack_range‘)[‘hp_max‘].apply(custom_max)
```
`apply`返回的结果索引是分组键（如“近战”、“远程”），通常需要与原始数据合并。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_166.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_168.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_170.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_172.png)

**使用`transform()`方法：**
`transform()`同样将每个分组传递给函数，但要求函数返回一个与输入分组**形状相同**的Series。它的作用是**为原始数据的每一行**都返回一个基于其所属分组的计算值。
```python
def custom_max_transform(x):
    # 为分组内的每个元素都返回该组的最大值
    return x.max()

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_174.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_176.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_177.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_179.png)

# 使用transform，结果为每个英雄都标记上其所属分组（近战/远程）的最大生命值
transformed = data.groupby(‘attack_range‘)[‘hp_max‘].transform(custom_max_transform)
data[‘group_max_hp‘] = transformed
```
`transform`的结果可以直接赋值给原DataFrame的新列，因为它与原始数据的行索引是一一对应的。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_180.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_182.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_184.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_186.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_188.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_190.png)

**核心区别总结：**
*   `apply`：用于聚合，返回每个组的**一个**汇总结果。
*   `transform`：用于转换，为原始数据的**每一行**返回一个值，通常用于创建基于分组的新特征。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_192.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_194.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_195.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_197.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_199.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_201.png)

## 交叉表与透视表 📈

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_203.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_205.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_207.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_209.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_211.png)

交叉表和透视表是分组聚合的特殊形式，能够以更直观的矩阵形式展示数据关系。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_213.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_215.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_217.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_219.png)

**交叉表 (`pd.crosstab`):**
主要用于计算分组频率，但也可以进行其他聚合。它指定行索引、列索引和要计算的值。
```python
# 计算各职业(行)在不同攻击范围(列)下的平均最大生命值
cross_tab = pd.crosstab(index=data[‘role‘],
                         columns=data[‘attack_range‘],
                         values=data[‘hp_max‘],
                         aggfunc=‘mean‘)
print(cross_tab)
```

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_221.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_223.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_225.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_227.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_229.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_231.png)

**透视表 (`df.pivot_table`):**
功能与交叉表类似，但语法更接近Excel的数据透视表，是DataFrame的一个方法。
```python
# 实现与上面交叉表相同的功能
pivot_tab = data.pivot_table(values=‘hp_max‘,
                              index=‘role‘,
                              columns=‘attack_range‘,
                              aggfunc=‘mean‘)
print(pivot_tab)
```
透视表还支持`normalize`参数进行数据规范化等高级功能。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_233.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_235.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_237.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_239.png)

**重要提示：** 交叉表和透视表实现的功能，完全可以通过`groupby`配合多层索引与`unstack`操作来实现。它们提供了更便捷的语法来达成特定形式的汇总。

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_241.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_243.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_245.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_247.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_249.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_251.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_253.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_255.png)

## 总结 🎯

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_257.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_259.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_261.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_263.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_265.png)

![](img/e9a62dfaf2dc9bd6b64456fe750e134d_266.png)

本节课中我们一起学习了数据分组处理的核心知识。我们从最基础的`groupby`和`agg`聚合开始，掌握了如何对数据进行分类统计。接着，我们探讨了多层级分组以及如何使用`apply`和`transform`方法进行高级的自定义聚合操作，并理解了两者在返回结果和用途上的关键区别。最后，我们介绍了交叉表`crosstab`和透视表`pivot_table`，它们是进行多维数据汇总和展示的强大工具。掌握这些技能，将为你后续进行更复杂的数据分析和金融量化建模打下坚实的基础。