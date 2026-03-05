# Kaggle竞赛Gresearch对数收益率残差预测：P1：赛程说明与数据理解 📊

![](img/89c0a0439b1a040b31b54c16933b0330_1.png)

在本节课中，我们将学习Kaggle平台上由Gresearch举办的加密货币预测竞赛。我们将详细解读竞赛背景、目标、提供的数据集以及一个基础的机器学习基准模型。通过本课，你将理解如何预测加密货币在15分钟后的对数收益率残差，并了解数据处理的基本流程。

---

![](img/89c0a0439b1a040b31b54c16933b0330_3.png)

## 数据集概述与结构

![](img/89c0a0439b1a040b31b54c16933b0330_5.png)

上一节我们介绍了竞赛的基本背景，本节中我们来看看竞赛提供的数据集具体包含哪些内容。

![](img/89c0a0439b1a040b31b54c16933b0330_7.png)

竞赛数据位于 `input/g-research-crypto-forecasting/` 目录下。以下是该目录中的主要文件：

*   **train.csv**: 主要的训练数据集。
*   **supplemental_train.csv**: 补充的训练数据。
*   **example_test.csv**: 示例测试集，用于演示提交格式。
*   **example_sample_submission.csv**: 示例提交文件。
*   **asset_details.csv**: 资产详情文件，列出了所有需要预测的加密货币及其权重。
*   **test.csv**: 真正的测试集，用于最终评估模型。

`train.csv` 是核心训练数据，每一行代表某一特定加密货币在某一分钟的数据快照。其列包括：

![](img/89c0a0439b1a040b31b54c16933b0330_9.png)

![](img/89c0a0439b1a040b31b54c16933b0330_11.png)

*   **timestamp**: 以秒为单位的Unix时间戳。
*   **Asset_ID**: 加密货币的编号（对应 `asset_details.csv`）。
*   **Count**: 该分钟内发生的交易次数。
*   **Open, High, Low, Close**: 该分钟的开盘价、最高价、最低价、收盘价。
*   **Volume**: 该分钟的交易量。
*   **VWAP**: 该分钟的成交量加权平均价。
*   **Target**: 需要预测的目标值，即15分钟后的对数收益率残差。

---

![](img/89c0a0439b1a040b31b54c16933b0330_13.png)

## 目标变量：对数收益率残差

![](img/89c0a0439b1a040b31b54c16933b0330_15.png)

理解了数据结构后，本节我们深入探讨竞赛的核心预测目标——对数收益率残差。

![](img/89c0a0439b1a040b31b54c16933b0330_17.png)

预测目标不是简单的价格或收益率，而是一个经过处理的“残差”。其计算涉及以下步骤：

1.  **计算单个资产的15分钟后对数收益率**：
    公式为：
    `R1 = log( P_{t+16} / P_t )`
    其中，`P_t` 是当前时间 `t` 的价格，`P_{t+16}` 是16分钟后的价格（因为预测的是15分钟后的收益率，数据是1分钟频率，所以下标+16）。

![](img/89c0a0439b1a040b31b54c16933b0330_19.png)

2.  **计算市场加权平均收益率**：
    公式为：
    `M_t = Σ ( Weight_i * R1_i ) / Σ ( Weight_i )`
    这里对市场上所有资产（通常排除比特币和以太坊）在时间 `t` 的收益率 `R1` 按其权重进行加权平均，得到一个“市场基准”收益率。

3.  **计算目标残差**：
    最终预测目标 `Target` 是资产自身的收益率减去其与市场基准相关的部分。
    公式为：
    `Target = R1 - β * M_t`
    其中，`β` 是该资产收益率对市场收益率的滚动回归系数（例如，使用过去3750分钟的数据计算）。

**简单理解**：这个 `Target` 旨在捕捉资产相对于整个市场的“超额收益”或“特异波动”。如果模型能准确预测它，就意味着能判断该资产在未来15分钟是否会跑赢或跑输大盘。

![](img/89c0a0439b1a040b31b54c16933b0330_21.png)

![](img/89c0a0439b1a040b31b54c16933b0330_23.png)

---

![](img/89c0a0439b1a040b31b54c16933b0330_25.png)

![](img/89c0a0439b1a040b31b54c16933b0330_27.png)

![](img/89c0a0439b1a040b31b54c16933b0330_29.png)

## 数据探索与预处理

在构建模型之前，我们必须先理解和清洗数据。本节我们来看看如何进行基本的数据探索与预处理。

![](img/89c0a0439b1a040b31b54c16933b0330_31.png)

![](img/89c0a0439b1a040b31b54c16933b0330_33.png)

![](img/89c0a0439b1a040b31b54c16933b0330_35.png)

### 数据加载与查看

![](img/89c0a0439b1a040b31b54c16933b0330_37.png)

首先，使用Pandas加载数据并查看资产详情。

![](img/89c0a0439b1a040b31b54c16933b0330_39.png)

![](img/89c0a0439b1a040b31b54c16933b0330_41.png)

```python
import pandas as pd
import numpy as np

![](img/89c0a0439b1a040b31b54c16933b0330_43.png)

![](img/89c0a0439b1a040b31b54c16933b0330_45.png)

![](img/89c0a0439b1a040b31b54c16933b0330_47.png)

# 加载资产详情
asset_details = pd.read_csv('../input/g-research-crypto-forecasting/asset_details.csv')
print(asset_details.head())

![](img/89c0a0439b1a040b31b54c16933b0330_49.png)

# 加载训练数据
train_df = pd.read_csv('../input/g-research-crypto-forecasting/train.csv')
print(train_df.head())
print(train_df.info())
```

![](img/89c0a0439b1a040b31b54c16933b0330_51.png)

### 处理缺失数据与“丢帧”

![](img/89c0a0439b1a040b31b54c16933b0330_53.png)

高频数据常出现整条记录缺失的情况，即“丢帧”。我们需要检测并处理。

![](img/89c0a0439b1a040b31b54c16933b0330_55.png)

![](img/89c0a0439b1a040b31b54c16933b0330_57.png)

以下是检测比特币数据是否丢帧的方法：

![](img/89c0a0439b1a040b31b54c16933b0330_59.png)

![](img/89c0a0439b1a040b31b54c16933b0330_61.png)

```python
# 选取比特币数据 (Asset_ID == 1)
btc = train_df[train_df['Asset_ID']==1].copy()
# 将时间戳设置为索引，便于计算时间差
btc = btc.set_index('timestamp')
# 计算连续时间戳之间的差值（单位：秒）
time_diffs = btc.index[1:] - btc.index[:-1]
# 查看差值分布，正常应为60秒
print(pd.Series(time_diffs).value_counts().head())
```

![](img/89c0a0439b1a040b31b54c16933b0330_63.png)

如果发现120秒、180秒等差值，说明中间有1分钟、2分钟的数据完全缺失。

![](img/89c0a0439b1a040b31b54c16933b0330_65.png)

![](img/89c0a0439b1a040b31b54c16933b0330_67.png)

**处理方式**：可以重新构建一个完整的时间索引（每分钟一个点），并使用前向填充（`ffill`）或插值法来填充缺失的行情数据。

![](img/89c0a0439b1a040b31b54c16933b0330_69.png)

![](img/89c0a0439b1a040b31b54c16933b0330_71.png)

![](img/89c0a0439b1a040b31b54c16933b0330_73.png)

```python
# 创建完整的分钟级时间索引
full_index = pd.RangeIndex(start=btc.index[0], stop=btc.index[-1]+60, step=60)
# 重新索引并填充
btc_reindexed = btc.reindex(full_index)
btc_filled = btc_reindexed.ffill() # 使用前值填充
```

![](img/89c0a0439b1a040b31b54c16933b0330_75.png)

### 资产相关性分析

![](img/89c0a0439b1a040b31b54c16933b0330_77.png)

![](img/89c0a0439b1a040b31b54c16933b0330_79.png)

![](img/89c0a0439b1a040b31b54c16933b0330_81.png)

加密货币价格走势通常高度相关。我们可以计算不同资产收益率之间的皮尔逊相关系数来观察这一点。

![](img/89c0a0439b1a040b31b54c16933b0330_83.png)

![](img/89c0a0439b1a040b31b54c16933b0330_85.png)

```python
# 以收盘价计算简单收益率
returns = train_df.pivot(index='timestamp', columns='Asset_ID', values='Close').pct_change()
# 计算相关系数矩阵
correlation_matrix = returns.corr()
# 可视化相关系数矩阵
import seaborn as sns
import matplotlib.pyplot as plt
plt.figure(figsize=(12,10))
sns.heatmap(correlation_matrix, annot=True, cmap='coolwarm')
plt.title('Cryptocurrency Returns Correlation Matrix')
plt.show()
```

![](img/89c0a0439b1a040b31b54c16933b0330_87.png)

分析发现，大多数加密货币之间存在较强的正相关性，但比特币和以太坊有时表现出一定的独立性。这解释了为什么竞赛目标中，为其他资产设计了减去市场基准的残差项。

![](img/89c0a0439b1a040b31b54c16933b0330_89.png)

![](img/89c0a0439b1a040b31b54c16933b0330_91.png)

![](img/89c0a0439b1a040b31b54c16933b0330_93.png)

---

![](img/89c0a0439b1a040b31b54c16933b0330_95.png)

## 构建一个简单的基准模型

在深入复杂模型前，建立一个简单的基准模型至关重要。本节我们将使用线性回归创建一个基准。

![](img/89c0a0439b1a040b31b54c16933b0330_97.png)

### 特征工程

首先，我们从原始的OHLCV数据中构造一些基本技术特征。

![](img/89c0a0439b1a040b31b54c16933b0330_99.png)

![](img/89c0a0439b1a040b31b54c16933b0330_101.png)

```python
def add_features(df):
    df = df.copy()
    # 计算上影线和下影线
    df['Upper_Shadow'] = df['High'] - np.maximum(df['Close'], df['Open'])
    df['Lower_Shadow'] = np.minimum(df['Close'], df['Open']) - df['Low']
    # 计算VWAP的简单移动平均
    df['VWAP_SMA_5'] = df['VWAP'].rolling(window=5).mean()
    # 计算价格变化
    df['Price_Change'] = df['Close'] - df['Open']
    return df

![](img/89c0a0439b1a040b31b54c16933b0330_103.png)

# 应用特征工程函数
featured_df = add_features(train_df)
```

![](img/89c0a0439b1a040b31b54c16933b0330_105.png)

![](img/89c0a0439b1a040b31b54c16933b0330_107.png)

### 模型训练与评估

![](img/89c0a0439b1a040b31b54c16933b0330_109.png)

![](img/89c0a0439b1a040b31b54c16933b0330_111.png)

我们为比特币单独训练一个线性回归模型。首先需要划分训练期和验证期。

![](img/89c0a0439b1a040b31b54c16933b0330_113.png)

```python
from sklearn.linear_model import LinearRegression
from sklearn.preprocessing import StandardScaler
from sklearn.metrics import mean_squared_error

# 1. 准备比特币数据
btc_data = featured_df[featured_df['Asset_ID']==1].dropna()
# 2. 定义特征(X)和目标(y)
feature_columns = ['Count', 'Volume', 'VWAP', 'Upper_Shadow', 'Lower_Shadow', 'VWAP_SMA_5', 'Price_Change']
X = btc_data[feature_columns]
y = btc_data['Target']
# 3. 按时间划分训练集和验证集 (例如用前80%时间训练，后20%验证)
split_idx = int(len(btc_data) * 0.8)
X_train, X_val = X.iloc[:split_idx], X.iloc[split_idx:]
y_train, y_val = y.iloc[:split_idx], y.iloc[split_idx:]
# 4. 标准化特征
scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_val_scaled = scaler.transform(X_val)
# 5. 训练线性回归模型
lr_model = LinearRegression()
lr_model.fit(X_train_scaled, y_train)
# 6. 预测并评估
y_pred = lr_model.predict(X_val_scaled)
mse = mean_squared_error(y_val, y_pred)
print(f"Linear Regression Baseline MSE for Bitcoin: {mse:.6f}")
```

![](img/89c0a0439b1a040b31b54c16933b0330_115.png)

![](img/89c0a0439b1a040b31b54c16933b0330_117.png)

这个简单的线性回归模型为我们提供了一个性能基准。在后续课程中，我们将探索更复杂的模型（如LightGBM、XGBoost）以及更精细的特征工程和集成方法，以提升预测精度，向竞赛金牌发起冲击。

![](img/89c0a0439b1a040b31b54c16933b0330_119.png)

![](img/89c0a0439b1a040b31b54c16933b0330_121.png)

---

![](img/89c0a0439b1a040b31b54c16933b0330_123.png)

![](img/89c0a0439b1a040b31b54c16933b0330_125.png)

![](img/89c0a0439b1a040b31b54c16933b0330_127.png)

本节课中我们一起学习了Gresearch加密货币预测竞赛的基本框架。我们了解了数据集的结构、核心预测目标“对数收益率残差”的数学定义，并完成了数据探索、预处理以及构建线性回归基准模型的完整流程。这为后续使用更高级的机器学习模型解决该问题打下了坚实的基础。