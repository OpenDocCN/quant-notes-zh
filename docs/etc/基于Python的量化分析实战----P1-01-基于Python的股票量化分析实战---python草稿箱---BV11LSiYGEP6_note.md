# 基于Python的量化分析实战：P1：01 股票量化分析基础流程与数据获取 📈

![](img/f827d602a80105035cf20a6948bc6f03_1.png)

![](img/f827d602a80105035cf20a6948bc6f03_3.png)

![](img/f827d602a80105035cf20a6948bc6f03_5.png)

在本节课中，我们将学习如何使用Python进行股票量化分析的基础流程，包括数据获取、指标计算以及初步的模型搭建。我们将从纯技术的角度出发，了解量化分析的核心步骤。

## 课程资料与安排

以下是本次课程将提供的资料：
*   数据分析环境搭建教程。
*   一套实战过的量化交易策略。
*   一个股票涨跌预测模型。
*   本次课程的所有素材。

## 量化分析的核心流程 🔄

上一节我们介绍了课程安排，本节中我们来看看进行量化交易或股票分析时，需要遵循的核心流程。

量化分析通常包含以下几个步骤：
1.  **数据获取**：获取股票的历史行情等原始数据。
2.  **数据准备**：对原始数据进行清洗、规整，处理不规范或不符合要求的数据。
3.  **建立指标**：基于原始数据计算新的分析指标，例如开盘价、收盘价、移动平均线等。
4.  **设计模型**：构建用于决策的模型，例如用于选择股票的“智能择股”模型，或用于选择买卖时机的“智能择时”模型。
5.  **设计策略**：制定具体的交易规则，明确何时买入、何时卖出。
6.  **回测测试**：使用历史数据测试设计好的策略和模型，评估其收益表现。
7.  **评估与改进**：分析测试结果，评估模型效果，并持续改进优化。

![](img/f827d602a80105035cf20a6948bc6f03_7.png)

![](img/f827d602a80105035cf20a6948bc6f03_9.png)

## 工具与环境介绍 🛠️

![](img/f827d602a80105035cf20a6948bc6f03_11.png)

![](img/f827d602a80105035cf20a6948bc6f03_13.png)

我们使用Python进行数据分析，并借助一个强大的工具包：**ANACONDA**。它集成了Python和许多常用的科学计算库。

在ANACONDA中，我们将使用一个交互式工具：**Jupyter Notebook**。你可以在终端中输入 `jupyter notebook` 命令来启动它。

![](img/f827d602a80105035cf20a6948bc6f03_15.png)

## 股票数据获取

上一节我们介绍了分析流程和工具，本节中我们来看看如何获取程序可用的股票数据。

我们使用一个名为 **tushare** 的Python库来免费获取股票历史行情数据。它是一个开源财经数据接口包。

### 安装与导入tushare

安装tushare库非常简单，只需一行命令：
```python
pip install tushare
```
安装成功后，在代码中导入即可使用：
```python
import tushare as ts
```

### 获取历史K线数据

使用 `get_k_data` 函数可以获取股票的历史K线数据。该函数需要三个主要参数：股票代码、开始日期和结束日期。

以下是获取“万科A”（股票代码：000002）在指定时间段数据的示例代码：
```python
# 获取股票历史数据
df = ts.get_k_data(‘000002’, start=‘2019-01-01’, end=‘2019-12-31’)
# 查看前5行数据
print(df.head())
```
这段代码会返回一个包含日期(`date`)、开盘价(`open`)、收盘价(`close`)、最高价(`high`)、最低价(`low`)、成交量(`volume`)等字段的表格数据。

### 获取实时行情数据

tushare也支持获取股票的实时行情数据：
```python
# 获取单只股票实时数据
realtime_df = ts.get_realtime_quotes(‘000002’)
# 筛选需要的字段
print(realtime_df[[‘code’, ‘name’, ‘price’, ‘bid’, ‘ask’, ‘volume’, ‘amount’, ‘time’]])
```
若要获取多只股票的实时数据，可以传入一个股票代码列表。

## 计算衍生变量与指标

获取基础数据后，我们需要基于它们计算更有分析价值的衍生变量和指标。

### 基础衍生变量计算

我们可以直接对已有字段进行运算，生成简单的衍生变量。例如，计算涨跌幅、价格变动等。
```python
# 计算当日涨跌幅
df[‘p_change’] = (df[‘close’] - df[‘open’]) / df[‘open’]
# 计算最高最低价波动幅度
df[‘hl_ratio’] = (df[‘high’] - df[‘low’]) / df[‘low’]
# 计算前一日收盘价
df[‘pre_close’] = df[‘close’].shift(1)
# 计算价格变动
df[‘price_change’] = df[‘close’] - df[‘pre_close’]
```

### 计算移动平均线（MA）

移动平均线是常用的技术指标。例如，5日均线(`MA5`)是最近5个交易日收盘价的平均值。
```python
# 计算5日和10日移动平均线
df[‘ma5’] = df[‘close’].rolling(5).mean()
df[‘ma10’] = df[‘close’].rolling(10).mean()
# 删除因计算移动平均线产生的空值
df = df.dropna()
```

### 使用TA-Lib库计算复杂指标

对于更复杂的专业指标（如RSI, MACD），我们可以使用 **TA-Lib** 库来简化计算。首先需要安装这个库（可能需要单独下载whl文件安装）。

安装后，计算指标变得非常简便：
```python
import talib
# 计算12日RSI（相对强弱指标）
df[‘rsi’] = talib.RSI(df[‘close’], timeperiod=12)
# 计算5日动量指标（MOM）
df[‘mom’] = talib.MOM(df[‘close’], timeperiod=5)
# 计算指数移动平均线（EMA）
df[‘ema12’] = talib.EMA(df[‘close’], timeperiod=12)
df[‘ema26’] = talib.EMA(df[‘close’], timeperiod=26)
# 计算MACD指标
df[‘macd’], df[‘macd_signal’], df[‘macd_hist’] = talib.MACD(df[‘close’], fastperiod=12, slowperiod=26, signalperiod=9)
```
> **注意**：TA-Lib由国外开发者编写，其中`MACD`相关输出与国内常见软件的命名略有不同，但计算思路一致。

## 构建简单的预测模型 🌱

数据准备就绪后，我们可以尝试构建一个简单的机器学习模型来预测股价涨跌。

### 准备特征与目标变量

首先，我们从所有字段中选取一部分作为模型的特征（`X`），并定义我们要预测的目标（`y`）。由于A股是T+1交易制度，我们可以尝试预测“下一个交易日”的涨跌。
```python
# 选取特征字段
feature_cols = [‘close’, ‘volume’, ‘close_open’, ‘hl_ratio’, ‘ma5’, ‘ma10’, ‘rsi’, ‘mom’, ‘ema12’, ‘ema26’, ‘macd’, ‘macd_signal’, ‘macd_hist’]
X = df[feature_cols]
# 构建目标变量：下一天的股价变动方向 (涨为1，跌为-1)
df[‘next_day_change’] = df[‘price_change’].shift(-1)
y = np.where(df[‘next_day_change’] > 0, 1, -1)
# 清理空值
data = pd.concat([X, pd.Series(y, name=‘target’)], axis=1).dropna()
X = data[feature_cols]
y = data[‘target’]
```

### 划分数据集并训练模型

股票数据具有时间序列特性，因此我们按时间顺序划分训练集和测试集，而不是随机划分。
```python
# 按时间顺序划分数据集（前90%训练，后10%测试）
split_point = int(len(X) * 0.9)
X_train, X_test = X[:split_point], X[split_point:]
y_train, y_test = y[:split_point], y[split_point:]
```
我们使用 **随机森林（Random Forest）** 模型进行训练。它是一种集成学习算法，通过构建多棵决策树并汇总结果来提高预测准确性。
```python
from sklearn.ensemble import RandomForestClassifier
# 创建随机森林模型
model = RandomForestClassifier(n_estimators=100, random_state=42)
# 训练模型
model.fit(X_train, y_train)
# 使用模型进行预测
y_pred = model.predict(X_test)
```

### 评估模型效果

最后，我们可以查看模型的预测准确率，并分析哪些特征对预测最重要。
```python
from sklearn.metrics import accuracy_score
# 计算准确率
accuracy = accuracy_score(y_test, y_pred)
print(f“模型预测准确率： {accuracy:.2%}”)
# 查看特征重要性
importances = model.feature_importances_
feature_importance_df = pd.DataFrame({‘feature’: feature_cols, ‘importance’: importances}).sort_values(by=‘importance’, ascending=False)
print(feature_importance_df)
```

## 总结与回顾 🎯

本节课中我们一起学习了股票量化分析的基础全流程：
1.  我们了解了从数据获取到模型评估的完整步骤。
2.  我们使用 **tushare** 库免费获取了股票的历史与实时数据。
3.  我们学习了如何计算基础衍生变量，并利用 **TA-Lib** 库高效计算RSI、MACD等复杂技术指标。
4.  我们构建了一个简单的 **随机森林** 机器学习模型，用于预测股价次日涨跌，并对其进行了初步评估。

![](img/f827d602a80105035cf20a6948bc6f03_17.png)

记住，这是一个从纯技术角度出发的入门演示。实际投资中需要综合考虑手续费、市场情绪、政策影响等诸多因素，并且任何模型都需要经过严格、长时间的回测与模拟盘验证后才能考虑实盘应用。