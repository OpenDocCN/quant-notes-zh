# 机器学习实战：P100：多项式回归实战天猫双十一销量预测（随机梯度下降模型）📈

![](img/5babde9d17ba0b2c5e8e3b88f4940061_1.png)

![](img/5babde9d17ba0b2c5e8e3b88f4940061_3.png)

![](img/5babde9d17ba0b2c5e8e3b88f4940061_5.png)

在本节课中，我们将学习如何使用随机梯度下降（SGD）模型来拟合多项式回归，并预测天猫双十一的销量。我们将重点关注数据预处理中的归一化操作，以及如何正确应用它来提升模型性能。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_7.png)

![](img/5babde9d17ba0b2c5e8e3b88f4940061_9.png)

上一节我们介绍了使用线性回归进行多项式拟合，本节中我们来看看如何使用随机梯度下降模型来完成同样的任务。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_11.png)

## 模型构建与初步尝试

![](img/5babde9d17ba0b2c5e8e3b88f4940061_13.png)

首先，我们复制之前线性回归的代码，但将模型替换为 `SGDRegressor`，并设置 `fit_intercept=False`。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_15.png)

```python
from sklearn.linear_model import SGDRegressor
model = SGDRegressor(fit_intercept=False)
```

![](img/5babde9d17ba0b2c5e8e3b88f4940061_17.png)

执行后，我们发现模型拟合效果不佳。这是因为输入特征的数据量级差异过大。

## 数据归一化的重要性

观察我们的特征数据 `x_2`，其不同列（如原始值、平方项、立方项）的数值范围差异巨大。在梯度下降中，这会导致不同特征方向的更新速度不一致，从而难以收敛或收敛缓慢。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_19.png)

![](img/5babde9d17ba0b2c5e8e3b88f4940061_21.png)

为了解决这个问题，我们需要对数据进行归一化（标准化）处理。以下是关键步骤：

![](img/5babde9d17ba0b2c5e8e3b88f4940061_23.png)

![](img/5babde9d17ba0b2c5e8e3b88f4940061_25.png)

1.  导入标准化工具。
2.  对训练数据进行拟合和转换。
3.  使用相同的转换器对测试数据进行转换。

```python
from sklearn.preprocessing import StandardScaler
# 初始化标准化器
scaler = StandardScaler()
# 对训练数据进行拟合和转换
x_2_norm = scaler.fit_transform(x_2)
# 对测试数据进行转换（使用训练数据拟合出的参数）
x_test_2_norm = scaler.transform(x_test_2)
```

**核心概念**：标准化公式为 `(x - mean) / std`，即将数据转换为均值为0、标准差为1的分布。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_27.png)

## 调整模型参数

使用归一化后的数据重新训练 `SGDRegressor` 模型，但可能仍会收到“未达到最大迭代次数”的警告。我们可以通过调整参数来改善：

![](img/5babde9d17ba0b2c5e8e3b88f4940061_29.png)

*   **增加最大迭代次数 (`max_iter`)**：例如设置为10000，让模型有更多机会收敛。
*   **调整学习率 (`eta0`)**：适当调大学习率可以加快收敛速度。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_31.png)

调整后，模型能够更好地拟合数据趋势。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_33.png)

![](img/5babde9d17ba0b2c5e8e3b88f4940061_35.png)

## 数据范围一致性的注意事项

在实验过程中，我们发现一个细节问题：训练数据 `x_2` 的范围（例如1到11）与测试数据 `x_test_2` 的范围（例如1到12）不完全一致。这在使用 `fit_transform` 时会导致基准不同。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_37.png)

![](img/5babde9d17ba0b2c5e8e3b88f4940061_39.png)

**解决方案**：
确保用于生成训练和测试特征的数据源（原始X值）范围一致。例如，都使用 `np.arange` 或都使用 `np.linspace`，并注意两者的区间定义（`arange` 是左闭右开，`linspace` 是左闭右闭）。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_41.png)

当数据范围一致时，对测试数据使用 `transform` 或 `fit_transform` 效果相同。若范围不一致，则必须以训练数据为基准，对测试数据只使用 `transform` 方法。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_43.png)

## 最终效果

![](img/5babde9d17ba0b2c5e8e3b88f4940061_45.png)

![](img/5babde9d17ba0b2c5e8e3b88f4940061_47.png)

经过数据归一化和参数调整后，随机梯度下降模型能够很好地拟合天猫双十一销量的历史数据曲线，其效果与线性回归模型相当。

![](img/5babde9d17ba0b2c5e8e3b88f4940061_49.png)

本节课中我们一起学习了如何应用随机梯度下降模型进行多项式回归。关键点在于：**对于像SGD这样对数据尺度敏感的优化算法，必须进行特征归一化处理**。同时，我们也注意到了训练数据与测试数据预处理一致性以及数据生成范围的重要性。通过本实验，我们看到使用不同的模型（线性回归与随机梯度下降）都能有效解决多项式回归问题。