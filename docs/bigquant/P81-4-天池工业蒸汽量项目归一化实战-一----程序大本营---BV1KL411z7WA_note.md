# 机器学习实战：P81：归一化在天池工业蒸汽量项目中的应用（一）

![](img/5f39f39ef310493e3649f5accc403c4f_1.png)

![](img/5f39f39ef310493e3649f5accc403c4f_3.png)

在本节课中，我们将学习数据预处理中一个非常重要的技术——归一化。我们将以天池工业蒸汽量预测项目的数据集为例，通过对比归一化前后模型预测效果的变化，直观地理解归一化的作用与必要性。

## 数据加载与初步观察

首先，我们需要加载项目数据并进行初步查看。以下是加载数据的核心代码：

```python
import pandas as pd
import numpy as np

# 加载训练数据
df = pd.read_csv('./zhengqi_train.txt', sep='\t')
# 提取特征（X）和目标值（y）
X_train = df.iloc[:, :-1]
y_train = df['target']

![](img/5f39f39ef310493e3649f5accc403c4f_5.png)

![](img/5f39f39ef310493e3649f5accc403c4f_7.png)

# 加载测试数据
X_test = pd.read_csv('./zhengqi_test.txt', sep='\t')

# 查看数据前几行
print(X_train.head())
print(y_train.head())
print(X_test.head())
```

执行上述代码后，我们可以观察到原始数据的一个特点：不同特征（列）的数值范围（量纲）差异巨大。例如，`V6` 列的值普遍较大（如0.5， 0.9），而 `V11` 列的值则较小（如-0.307， -0.445）。

![](img/5f39f39ef310493e3649f5accc403c4f_9.png)

上一节我们加载并观察了原始数据，本节中我们来看看如果不进行归一化处理，直接使用这些数据训练模型会怎样。

## 未经归一化的模型训练

我们首先使用未经任何处理的原始数据来训练一个线性回归模型，并保存预测结果。

```python
from sklearn.linear_model import LinearRegression

# 初始化模型
model = LinearRegression()
# 使用原始数据训练模型
model.fit(X_train, y_train)
# 使用训练好的模型对测试集进行预测
y_pred = model.predict(X_test)

![](img/5f39f39ef310493e3649f5accc403c4f_11.png)

![](img/5f39f39ef310493e3649f5accc403c4f_13.png)

# 保存预测结果
np.savetxt('./result1.txt', y_pred)
```

我们将这个结果文件 `result1.txt` 提交到天池竞赛平台，得到的评分是 **3.88096**。这个分数（均方误差）越大代表模型预测效果越差。

![](img/5f39f39ef310493e3649f5accc403c4f_15.png)

为什么直接使用原始数据效果可能不理想呢？这引出了归一化的核心概念。

![](img/5f39f39ef310493e3649f5accc403c4f_17.png)

## 为什么需要归一化？

![](img/5f39f39ef310493e3649f5accc403c4f_19.png)

归一化的主要目的是消除数据特征之间因量纲（单位）不同带来的影响。我们可以通过两个比喻来理解：

![](img/5f39f39ef310493e3649f5accc403c4f_21.png)

1.  **价值与重量**：家里有500克黄金和2吨铁块。虽然铁的“数值”（重量）远大于黄金，但其价值却远低于黄金。在模型中，如果某一特征数值很大，并不能直接代表它对结果的影响（重要性）就大。
2.  **太阳与星星**：白天我们看不到星星，不是因为星星不存在，而是太阳的光芒（量纲大）掩盖了星星的光芒（量纲小）。在机器学习中，如果一个特征的数值范围特别大，它在计算梯度、距离或系数时可能会“主导”整个模型，掩盖其他重要但数值范围小的特征的作用。

![](img/5f39f39ef310493e3649f5accc403c4f_23.png)

![](img/5f39f39ef310493e3649f5accc403c4f_25.png)

因此，不进行归一化，模型可能会错误地赋予数值大的特征过高的权重，导致学习效率低下和预测精度下降。

![](img/5f39f39ef310493e3649f5accc403c4f_27.png)

![](img/5f39f39ef310493e3649f5accc403c4f_29.png)

上一节我们理解了归一化的必要性，接下来我们具体实践两种最常用的归一化方法。

![](img/5f39f39ef310493e3649f5accc403c4f_31.png)

## 方法一：最大最小值归一化

![](img/5f39f39ef310493e3649f5accc403c4f_33.png)

![](img/5f39f39ef310493e3649f5accc403c4f_35.png)

最大最小值归一化（Min-Max Scaling）通过对原始数据进行线性变换，将数据映射到[0, 1]的区间内。其公式为：
**X_norm = (X - X_min) / (X_max - X_min)**

以下是使用 `sklearn` 实现该方法的步骤：

```python
from sklearn.preprocessing import MinMaxScaler

# 初始化归一化器
mm_scaler = MinMaxScaler()
# 拟合训练数据（计算训练集的最小值和最大值）
mm_scaler.fit(X_train)
# 转换训练数据
X_train_mm = mm_scaler.transform(X_train)
# 用相同的参数转换测试数据
X_test_mm = mm_scaler.transform(X_test)

# 使用归一化后的数据重新训练模型
model_mm = LinearRegression()
model_mm.fit(X_train_mm, y_train)
y_pred_mm = model_mm.predict(X_test_mm)

# 保存结果
np.savetxt('./result2.txt', y_pred_mm)
```

**重要提示**：必须使用训练集拟合（`fit`）得到的参数（即 `X_train` 的 `min` 和 `max`）来转换（`transform`）测试集，以确保数据变换的一致性。

![](img/5f39f39ef310493e3649f5accc403c4f_37.png)

![](img/5f39f39ef310493e3649f5accc403c4f_39.png)

## 方法二：Z-Score 标准化

![](img/5f39f39ef310493e3649f5accc403c4f_41.png)

Z-Score 标准化（Standardization）将数据变换为均值为0、标准差为1的正态分布。其公式为：
**X_std = (X - μ) / σ**
其中，μ 是均值，σ 是标准差。

![](img/5f39f39ef310493e3649f5accc403c4f_43.png)

以下是使用 `sklearn` 实现该方法的步骤：

![](img/5f39f39ef310493e3649f5accc403c4f_45.png)

```python
from sklearn.preprocessing import StandardScaler

![](img/5f39f39ef310493e3649f5accc403c4f_47.png)

# 初始化标准化器
std_scaler = StandardScaler()
# 拟合训练数据（计算训练集的均值和标准差）
std_scaler.fit(X_train)
# 转换训练数据
X_train_std = std_scaler.transform(X_train)
# 用相同的参数转换测试数据
X_test_std = std_scaler.transform(X_test)

# 使用标准化后的数据重新训练模型
model_std = LinearRegression()
model_std.fit(X_train_std, y_train)
y_pred_std = model_std.predict(X_test_std)

![](img/5f39f39ef310493e3649f5accc403c4f_49.png)

# 保存结果
np.savetxt('./result3.txt', y_pred_std)
```

我们将 `result2.txt`（Min-Max归一化）和 `result3.txt`（Z-Score标准化）的结果也提交到天池平台。

![](img/5f39f39ef310493e3649f5accc403c4f_51.png)

## 结果分析与问题排查

![](img/5f39f39ef310493e3649f5accc403c4f_53.png)

提交后我们发现一个意外情况：三种方法（原始数据、Min-Max、Z-Score）得到的竞赛评分完全相同，均为 **3.88096**。这显然不符合预期。

![](img/5f39f39ef310493e3649f5accc403c4f_55.png)

通过打印和对比三个结果文件的前若干行预测值，我们发现它们完全一致：

![](img/5f39f39ef310493e3649f5accc403c4f_57.png)

```python
print(‘原始数据预测值前15个：‘, y_pred[:15])
print(‘Min-Max归一化预测值前15个：‘, y_pred_mm[:15])
print(‘Z-Score标准化预测值前15个：‘, y_pred_std[:15])
```

![](img/5f39f39ef310493e3649f5accc403c4f_59.png)

输出结果将显示三组数字完全相同。这说明我们的归一化操作可能并未真正生效，或者生效后对线性回归模型在该特定数据集上的输出没有产生影响。这引出了一个更深层次的问题：对于线性回归等某些模型，单纯的线性归一化是否总是必要？我们将在后续课程中探讨。

![](img/5f39f39ef310493e3649f5accc403c4f_61.png)

![](img/5f39f39ef310493e3649f5accc403c4f_63.png)

## 总结

![](img/5f39f39ef310493e3649f5accc403c4f_65.png)

本节课中我们一起学习了数据预处理中的归一化技术：
1.  我们加载了天池工业蒸汽量数据集，并观察到特征间存在量纲差异。
2.  我们阐述了不进行归一化可能导致的问题，即大数值特征可能不合理地主导模型。
3.  我们实践了两种最常用的归一化方法：**最大最小值归一化**和**Z-Score标准化**，并使用 `sklearn` 库实现了它们。
4.  我们遇到了一个值得深入思考的现象：在本案例中，归一化并未改变线性回归模型的最终预测输出。这提示我们，需要根据模型和数据的特性来理解和应用归一化。

![](img/5f39f39ef310493e3649f5accc403c4f_67.png)

在下一节课中，我们将深入分析这一现象的原因，并探讨在什么情况下归一化是至关重要的。