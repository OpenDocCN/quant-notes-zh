# 人工智能数据挖掘实战：P55：聚类分析实例 📊

在本节课中，我们将学习如何在Python金融量化分析中应用聚类方法。我们将使用K-Means算法对股票数据进行聚类，以探索数据的内在分组模式，并尝试将其应用于简单的策略分析。

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_1.png)

---

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_2.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_3.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_4.png)

## 第一步：导入工具包 🧰

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_5.png)

与之前课程一致，首先需要导入必要的Python工具包。这些工具包为数据处理和建模提供了基础功能。

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_7.png)

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
from sklearn.cluster import KMeans
```

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_8.png)

---

## 第二步：导入与准备数据 📈

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_10.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_12.png)

上一节我们介绍了工具包，本节中我们来看看数据准备。我们将导入一份股票数据，并从中提取用于建模的特征。

我们选择了两个特征：股票前一天的收益率指标和前两天的收益率指标。这两个指标将作为聚类模型的输入。

```python
# 假设data是已加载的Pandas DataFrame
# 特征工程：创建‘feature_1’（前一天收益率）和‘feature_2’（前两天收益率）
data['feature_1'] = data['returns'].shift(1)
data['feature_2'] = data['returns'].shift(2)
# 删除包含NaN的行
data = data.dropna()
# 选择特征列
features = data[['feature_1', 'feature_2']]
```

---

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_14.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_15.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_16.png)

## 第三步：建立K-Means聚类模型 🤖

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_17.png)

有了准备好的特征数据，接下来我们开始建模。与之前使用的回归方法不同，本次任务我们使用无监督学习中的聚类方法。

在Scikit-learn库中，K-Means是最常用的聚类算法之一。以下是实例化模型的步骤：

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_19.png)

```python
# 实例化K-Means模型，指定要聚成2类（对应涨/跌）
kmeans_model = KMeans(n_clusters=2, random_state=42)
# 使用特征数据拟合模型
kmeans_model.fit(features)
```

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_21.png)

**核心概念**：K-Means算法的目标是最小化所有样本点到其所属簇中心的距离平方和，其目标函数（惯性）可表示为：
`J = Σ Σ ||x_i - μ_k||^2`
其中，`μ_k` 是第k个簇的中心点。

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_23.png)

模型参数`n_clusters=2`表示我们希望将数据点分为两个簇。`random_state`参数用于控制初始中心点选择的随机性，以确保结果可复现。

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_24.png)

---

## 第四步：预测与结果处理 🔮

模型拟合完成后，我们可以用它来预测每个数据点属于哪个簇。

以下是预测并将结果映射到我们期望的标签（1代表涨，-1代表跌）的步骤：

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_26.png)

```python
# 预测每个样本的簇标签（初始为0或1）
cluster_labels = kmeans_model.predict(features)
# 将簇标签映射为1和-1
# 假设我们希望将标签0映射为1，标签1映射为-1
data['predicted_signal'] = np.where(cluster_labels == 0, 1, -1)
```

现在，`data` DataFrame中新增了一列`predicted_signal`，其值为1或-1，代表模型对每个交易日涨跌的聚类判断。

---

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_28.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_29.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_30.png)

## 第五步：可视化聚类结果 📊

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_31.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_32.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_33.png)

为了直观地理解聚类效果，我们可以将数据点在二维空间中画出来，并用不同颜色区分不同的簇。

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_35.png)

以下是绘制散点图的代码：

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_36.png)

```python
plt.figure(figsize=(10, 6))
# 绘制散点图，x轴为feature_1，y轴为feature_2，颜色根据预测信号区分
plt.scatter(data['feature_1'], data['feature_2'],
            c=data['predicted_signal'], cmap='bwr', alpha=0.6)
plt.xlabel(‘前一天收益率 (feature_1)’)
plt.ylabel(‘前两天收益率 (feature_2)’)
plt.title(‘K-Means聚类结果可视化’)
plt.colorbar(label=‘预测信号 (1=涨, -1=跌)’)
plt.grid(True)
plt.show()
```

在生成的图中，蓝色和红色的点分别代表被归为不同簇的数据点。我们可以观察是否存在一定的模式，例如负收益特征的数据点是否更多地被聚到了一起。

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_38.png)

---

## 第六步：策略回测与评估 ⚖️

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_40.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_41.png)

仅仅得到聚类分组还不够，我们需要评估这种基于聚类的方法是否能构成有效的交易策略。

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_42.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_43.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_44.png)

以下是进行简单回测的步骤。我们计算如果按照聚类信号进行交易（信号为1时买入持有，为-1时卖出或空仓），其累计收益如何。

```python
# 计算策略收益：假设信号为1时，获得当日收益率；信号为-1时，获得当日收益率的相反数（模拟做空）
data[‘strategy_returns’] = data[‘predicted_signal’] * data[‘returns’]
# 计算累计收益（需先将收益率从对数形式还原，假设原始returns为对数收益率）
data[‘cumulative_returns’] = (1 + data[‘returns’]).cumprod()
data[‘cumulative_strategy_returns’] = (1 + data[‘strategy_returns’]).cumprod()
# 输出最终累计收益
final_return = data[‘cumulative_returns’].iloc[-1]
final_strategy_return = data[‘cumulative_strategy_returns’].iloc[-1]
print(f“买入持有累计收益： {final_return:.4f}”)
print(f“聚类策略累计收益： {final_strategy_return:.4f}”)
```

**注意**：由于K-Means聚类具有随机性（初始中心点随机选择），每次运行的结果可能略有不同。因此，在实际应用中需要多次运行并评估其稳定性。

---

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_46.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_47.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_48.png)

## 总结 🎯

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_49.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_50.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_51.png)

本节课中我们一起学习了如何在金融数据分析中应用K-Means聚类算法。

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_52.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_53.png)

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_54.png)

我们回顾一下主要步骤：
1.  **导入工具包**：准备了数据分析与建模的环境。
2.  **准备数据**：从原始数据中提取了关键特征。
3.  **建立模型**：使用`KMeans`将数据点划分为两个簇。
4.  **预测处理**：获得聚类标签并将其转换为交易信号。
5.  **结果可视化**：通过散点图直观展示了聚类效果。
6.  **策略回测**：简单评估了基于聚类信号的交易策略表现。

![](img/53bc1fe1ab3b10a18b36f25549ea64e3_56.png)

需要强调的是，聚类是一种无监督学习方法，在缺乏明确标签指导的金融预测中，其效果往往不稳定且难以评估。本节课的目的是让大家熟悉聚类在量化分析中的基本操作流程，在实际复杂场景中，通常需要结合更多有监督学习方法或领域知识。