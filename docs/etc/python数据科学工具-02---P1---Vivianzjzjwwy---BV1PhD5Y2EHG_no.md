# Python数据科学工具-026：Pandas向量化字符串操作实例 🍳

在本节课中，我们将学习如何利用Pandas的向量化字符串操作来处理一个真实的、结构较为混乱的食谱数据集。我们将从读取数据开始，逐步清理和分析数据，最终构建一个简单的食谱推荐系统，以展示字符串操作在数据清洗中的强大作用。

## 数据来源与问题概述

![](img/a92c8221304153a13cb2a8f09b6d7c19_1.png)

我们使用的数据是一个开放的食谱数据库，它从网络上多个不同来源收集而来。数据格式为JSON，但存在一些结构问题，导致无法直接使用`pd.read_json`读取。我们的目标是解析出每份食谱所使用的食材，以便后续根据食材查找相关食谱。

![](img/a92c8221304153a13cb2a8f09b6d7c19_3.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_5.png)

## 读取并修复JSON数据

![](img/a92c8221304153a13cb2a8f09b6d7c19_7.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_8.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_10.png)

由于原始数据文件并非一个标准的JSON数组，而是每行一个独立的JSON对象，我们需要先将其转换为一个合法的JSON数组格式。

以下是读取和修复数据的步骤：

1.  使用`with open`语句安全地打开文件。
2.  逐行读取文件内容，并去除每行首尾的空白字符。
3.  将所有行用逗号连接，并在首尾添加方括号，组合成一个完整的JSON数组字符串。
4.  使用`pd.read_json`配合`StringIO`来正确读取这个字符串。

![](img/a92c8221304153a13cb2a8f09b6d7c19_12.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_14.png)

```python
import pandas as pd
from io import StringIO

# 读取并修复JSON格式
with open('recipeitems-latest.json', 'r') as f:
    # 提取每一行并去除空白
    data = tuple(line.strip() for line in f)

# 将多行JSON对象组合成一个JSON数组字符串
json_str = '[{}]'.format(','.join(data))

![](img/a92c8221304153a13cb2a8f09b6d7c19_16.png)

# 使用StringIO包装字符串并读取为DataFrame
recipes = pd.read_json(StringIO(json_str))
```

![](img/a92c8221304153a13cb2a8f09b6d7c19_18.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_20.png)

现在，我们成功将数据加载到了名为`recipes`的DataFrame中，它包含约17万条食谱记录和17个字段。

![](img/a92c8221304153a13cb2a8f09b6d7c19_21.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_23.png)

## 初步探索数据

![](img/a92c8221304153a13cb2a8f09b6d7c19_25.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_27.png)

上一节我们成功加载了数据，本节中我们来看看数据的结构和内容。我们主要关注`ingredients`（食材）列。

![](img/a92c8221304153a13cb2a8f09b6d7c19_29.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_31.png)

```python
# 查看数据形状和前几行
print(recipes.shape)
print(recipes.head())

![](img/a92c8221304153a13cb2a8f09b6d7c19_33.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_34.png)

# 查看第一条食谱的详细信息
print(recipes.iloc[0])

![](img/a92c8221304153a13cb2a8f09b6d7c19_36.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_38.png)

# 分析食材字符串的长度分布
ingredient_lengths = recipes.ingredients.str.len()
print(ingredient_lengths.describe())

![](img/a92c8221304153a13cb2a8f09b6d7c19_40.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_42.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_44.png)

# 查找食材描述最长的食谱
max_idx = ingredient_lengths.argmax()
print(recipes.iloc[max_idx]['name'])
```

![](img/a92c8221304153a13cb2a8f09b6d7c19_46.png)

通过初步探索，我们发现`ingredients`列是一个自由文本字符串，格式混乱，这正是我们需要清理的目标。

![](img/a92c8221304153a13cb2a8f09b6d7c19_48.png)

## 使用字符串方法进行数据查询

Pandas的`.str`访问器提供了向量化的字符串操作方法，可以方便地对整个列进行搜索和过滤。

![](img/a92c8221304153a13cb2a8f09b6d7c19_50.png)

以下是几个查询示例：

![](img/a92c8221304153a13cb2a8f09b6d7c19_52.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_54.png)

*   **查找早餐食谱**：在`description`列中搜索包含“breakfast”的记录，不区分大小写。
*   **查找包含特定食材的食谱**：在`ingredients`列中搜索包含“cinnamon”（桂皮）的记录。
*   **处理可能的拼写错误**：搜索可能漏掉字母“n”的“cinnamon”变体。

![](img/a92c8221304153a13cb2a8f09b6d7c19_56.png)

```python
# 查找描述中包含‘breakfast’的食谱（不区分大小写）
breakfast_recipes = recipes.description.str.contains('breakfast', case=False)
print(breakfast_recipes.sum())

![](img/a92c8221304153a13cb2a8f09b6d7c19_58.png)

# 查找食材中包含‘cinnamon’的食谱（不区分大小写）
cinnamon_recipes = recipes.ingredients.str.contains('cinnamon', case=False)
print(cinnamon_recipes.sum())

# 查找可能拼写错误的‘cinnamon’（例如‘cinamon’）
cinnamon_misspelled = recipes.ingredients.str.contains('cinamon', case=False)
print(cinnamon_misspelled.sum())
```

![](img/a92c8221304153a13cb2a8f09b6d7c19_60.png)

这些操作让我们能够快速了解数据的概况，并定位到感兴趣的记录子集。

## 构建简单的食谱推荐系统

![](img/a92c8221304153a13cb2a8f09b6d7c19_62.png)

我们的目标是创建一个DataFrame，其行代表食谱，列代表一些常见食材（如盐、胡椒等）。单元格的值表示该食谱是否使用了该食材（True/False）。有了这个矩阵，我们就可以轻松地根据拥有的食材查找食谱。

![](img/a92c8221304153a13cb2a8f09b6d7c19_64.png)

构建该矩阵的步骤如下：

![](img/a92c8221304153a13cb2a8f09b6d7c19_65.png)

1.  定义一个我们关心的食材列表。
2.  针对每种食材，使用`.str.contains()`为所有食谱生成一个布尔序列（Series），表示是否包含该食材。
3.  将这些`{食材: 布尔序列}`对组成一个字典。
4.  使用`pd.DataFrame()`直接将该字典转换为目标DataFrame。

```python
# 定义一组常见食材/香料
spice_list = ['salt', 'pepper', 'oregano', 'sage', 'parsley', 'rosemary', 'tarragon', 'thyme', 'paprika', 'cumin']

# 构建字典：键为食材名，值为该食材在所有食谱中出现情况的布尔序列
spice_df = pd.DataFrame({
    spice: recipes.ingredients.str.contains(spice, case=False)
    for spice in spice_list
})

![](img/a92c8221304153a13cb2a8f09b6d7c19_67.png)

# 查看构建好的DataFrame
print(spice_df.head())
```

现在，`spice_df`就是一个我们需要的食材-食谱关系矩阵。

## 应用推荐系统进行查询

![](img/a92c8221304153a13cb2a8f09b6d7c19_69.png)

有了食材-食谱关系矩阵，查询同时包含多种食材的食谱就变得非常简单。我们只需对相应的列进行逻辑“与”操作即可。

例如，查找同时包含“盐”、“胡椒”和“牛至”的食谱：

![](img/a92c8221304153a13cb2a8f09b6d7c19_71.png)

```python
# 选择同时包含盐、胡椒和牛至的食谱
selection = spice_df.query('salt & pepper & oregano')

![](img/a92c8221304153a13cb2a8f09b6d7c19_73.png)

# 获取这些食谱的索引，并查找对应的食谱名称
recipe_indices = selection.index
matching_recipes = recipes.name.iloc[recipe_indices]

![](img/a92c8221304153a13cb2a8f09b6d7c19_75.png)

print(f"找到 {len(matching_recipes)} 份符合条件的食谱：")
print(matching_recipes)
```

![](img/a92c8221304153a13cb2a8f09b6d7c19_77.png)

通过以上步骤，我们就能根据手头的食材，快速找到可以尝试制作的食谱。

![](img/a92c8221304153a13cb2a8f09b6d7c19_79.png)

![](img/a92c8221304153a13cb2a8f09b6d7c19_81.png)

## 总结

本节课中我们一起学习了如何运用Pandas的向量化字符串操作来处理一个混乱的真实数据集。我们从修复非标准JSON格式开始，逐步探索了数据内容，并利用`.str.contains()`等方法进行数据查询和筛选。最后，我们构建了一个简单的食材-食谱关系矩阵，并实现了基于食材的食谱推荐功能。

![](img/a92c8221304153a13cb2a8f09b6d7c19_83.png)

这个例子清晰地展示了数据清洗在数据科学项目中的重要性，而Pandas提供的强大字符串工具能显著提高这项工作的效率。虽然要构建一个健壮的推荐系统还需要更多工作（例如更精确地解析食材列表），但本节课介绍的方法为处理类似文本数据提供了坚实的基础。