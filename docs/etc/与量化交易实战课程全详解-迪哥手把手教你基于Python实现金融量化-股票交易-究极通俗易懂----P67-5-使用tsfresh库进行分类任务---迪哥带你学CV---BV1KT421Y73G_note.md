# Python金融分析与量化交易实战课程：P67：使用tsfresh库进行分类任务 📊

在本节课中，我们将学习如何对时间序列数据进行分类任务。与之前预测未来值的回归任务不同，分类任务的目标是根据时间序列的特征，将其划分到不同的类别中，例如区分正常与异常的数据模式。

## 概述

当我们面对多条时间序列数据并希望对其进行分类时，例如区分正常与异常的机器运行状态，核心思路与常规分类任务一致：**先进行特征提取，再基于特征进行分类**。时间序列本身是原始数据，我们需要从中提取有意义的统计特征（如均值、最大值、变化频率等），才能输入给分类模型。

上一节我们介绍了时间序列的回归预测，本节中我们来看看如何为分类任务准备数据。

![](img/dbf9a166c4f935785bc4ffbe6fd58557_1.png)

## 时间序列分类的核心步骤

![](img/dbf9a166c4f935785bc4ffbe6fd58557_3.png)

时间序列分类任务通常遵循以下两步流程：

1.  **特征提取**：从原始时间序列数据中，计算出能够代表其特性的统计特征。
2.  **模型分类**：使用提取出的特征数据，训练一个分类模型（如决策树、随机森林等）。

手动计算这些特征代码繁琐。幸运的是，有现成的Python库可以自动化这个过程。

![](img/dbf9a166c4f935785bc4ffbe6fd58557_5.png)

## 介绍tsfresh库 🛠️

![](img/dbf9a166c4f935785bc4ffbe6fd58557_7.png)

`tsfresh` 是一个专门用于从时间序列中自动提取大量特征的Python库。它可以基于统计特性，从一个时间序列中提取出多达上千种特征，极大简化了特征工程的工作。

![](img/dbf9a166c4f935785bc4ffbe6fd58557_9.png)

![](img/dbf9a166c4f935785bc4ffbe6fd58557_11.png)

以下是`tsfresh`库的核心功能描述：
*   **输入**：原始时间序列数据。
*   **过程**：自动计算大量基于统计的特征。
*   **输出**：一个特征矩阵，其中每一行代表一条时间序列，每一列代表一个提取出的特征（例如 `最小值`、`最大值`、`均值`、`峰值` 等）。

![](img/dbf9a166c4f935785bc4ffbe6fd58557_13.png)

![](img/dbf9a166c4f935785bc4ffbe6fd58557_15.png)

### 安装与官方文档

![](img/dbf9a166c4f935785bc4ffbe6fd58557_17.png)

安装`tsfresh`非常简单，使用pip命令即可：

![](img/dbf9a166c4f935785bc4ffbe6fd58557_19.png)

```bash
pip install tsfresh
```

其官方文档提供了详细的指南和示例。对于初学者，建议从 **Quick Start** 部分开始，在实际需要处理数据时再进行深入查阅。

![](img/dbf9a166c4f935785bc4ffbe6fd58557_21.png)

## 实战演示：机器状态分类

我们将通过一个示例来演示整个流程。假设我们通过6个传感器（A, B, C, D, E, F）监控一台机器，每个传感器产生一条时间序列数据。我们的目标是：根据这些时间序列数据，判断机器是处于 **正常** 状态还是 **异常** 状态。

![](img/dbf9a166c4f935785bc4ffbe6fd58557_23.png)

![](img/dbf9a166c4f935785bc4ffbe6fd58557_25.png)

### 1. 数据加载与探索

首先，我们加载示例数据集并查看其结构。

![](img/dbf9a166c4f935785bc4ffbe6fd58557_27.png)

![](img/dbf9a166c4f935785bc4ffbe6fd58557_29.png)

```python
# 示例代码框架，展示数据加载
from tsfresh.examples import load_robot_execution_failures
df, y = load_robot_execution_failures()
print(df.head())
```

![](img/dbf9a166c4f935785bc4ffbe6fd58557_31.png)

数据框（`df`）通常包含以下列：
*   `id`: 每条时间序列的唯一标识（例如，某次机器运行的ID）。
*   `time`: 时间点。
*   `F_x`, `F_y`, ...: 各个传感器在不同时间点的读数（即时间序列值）。

我们可以通过可视化对比正常（`id=3`）和异常（`id=20`）序列的差异，直观感受分类任务的依据。

### 2. 使用tsfresh提取特征

这是最关键的一步。我们使用`tsfresh`的 `extract_features` 函数来批量提取特征。

```python
# 示例代码框架，展示特征提取
from tsfresh import extract_features
extracted_features = extract_features(df,
                                     column_id="id",      # 按此列分组，每条序列一个id
                                     column_sort="time") # 按此列排序
print(extracted_features.head())
```

![](img/dbf9a166c4f935785bc4ffbe6fd58557_33.png)

![](img/dbf9a166c4f935785bc4ffbe6fd58557_35.png)

执行后，`extracted_features` 将是一个特征矩阵，行数等于唯一 `id` 的数量，列数等于提取出的特征数量（可能成百上千）。

### 3. 特征过滤与分类建模

提取的特征可能很多，其中包含一些与分类目标无关的特征。`tsfresh` 提供了 `select_features` 函数进行特征过滤，可以基于统计测试保留最相关的特征。

![](img/dbf9a166c4f935785bc4ffbe6fd58557_37.png)

以下是完整的特征处理与建模流程：

![](img/dbf9a166c4f935785bc4ffbe6fd58557_39.png)

```python
# 示例代码框架，展示特征过滤与建模
from tsfresh import select_features
from tsfresh.utilities.dataframe_functions import impute
from sklearn.model_selection import train_test_split
from sklearn.tree import DecisionTreeClassifier
from sklearn.metrics import classification_report

![](img/dbf9a166c4f935785bc4ffbe6fd58557_41.png)

# 1. 处理缺失值
impute(extracted_features)

![](img/dbf9a166c4f935785bc4ffbe6fd58557_43.png)

# 2. 过滤特征 (y是之前加载的标签数据)
features_filtered = select_features(extracted_features, y)

![](img/dbf9a166c4f935785bc4ffbe6fd58557_45.png)

# 3. 划分训练集和测试集
X_train, X_test, y_train, y_test = train_test_split(features_filtered, y, test_size=0.3)

![](img/dbf9a166c4f935785bc4ffbe6fd58557_47.png)

![](img/dbf9a166c4f935785bc4ffbe6fd58557_49.png)

# 4. 训练分类模型（这里使用决策树）
clf = DecisionTreeClassifier()
clf.fit(X_train, y_train)

![](img/dbf9a166c4f935785bc4ffbe6fd58557_51.png)

# 5. 评估模型
y_pred = clf.predict(X_test)
print(classification_report(y_test, y_pred))
```

运行上述流程后，我们将得到模型的评估报告（包括精确度、召回率、F1分数等），从而了解基于`tsfresh`提取的特征进行分类的效果。

## 总结

![](img/dbf9a166c4f935785bc4ffbe6fd58557_53.png)

本节课中我们一起学习了如何对时间序列数据进行分类任务。核心要点如下：

![](img/dbf9a166c4f935785bc4ffbe6fd58557_55.png)

1.  **任务转换**：时间序列分类需要先将序列数据转换为特征矩阵。
2.  **工具使用**：`tsfresh` 库能自动化、批量地从时间序列中提取大量统计特征，是处理此类任务的利器。
3.  **标准流程**：流程遵循 **“加载数据 -> 提取特征 -> (可选)过滤特征 -> 训练分类模型 -> 评估”** 的模式。
4.  **后续学习**：`tsfresh` 官方文档和 `notebooks` 目录下的示例是深入学习和解决具体问题的优秀资源。

![](img/dbf9a166c4f935785bc4ffbe6fd58557_57.png)

当你在实际项目中需要对时间序列进行分类时，可以回顾本节课的内容，并参考 `tsfresh` 的官方示例快速上手。