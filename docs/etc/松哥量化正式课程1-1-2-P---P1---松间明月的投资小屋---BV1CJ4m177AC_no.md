# 量化投资入门：1-1-2：项目一代码详解

![](img/9993f1605e149e7599cb2f3362caa198_0.png)

在本节课中，我们将详细解析一个关于微盘股投资组合构建与分析的Python项目代码。我们将学习如何读取数据、处理数据、计算收益率、构建等权重投资组合，并最终可视化其历史表现。

---

## 数据读取与初步处理

上一节我们介绍了如何获取并拼接少量股票的历史数据。本节中，我们来看看如何处理一个包含402只微盘股的更大数据集。

首先，我们已经将微盘股数据预先处理好并保存为文件。现在，我们直接从文件中读取数据。

```python
# 读取微盘股数据
weipangu = pd.read_csv('weipangu_data.csv')
```

读取后，我们得到一个名为 `weipangu` 的DataFrame。为了了解数据结构，我们可以查看其前几行和基本信息。

```python
# 查看数据前5行
print(weipangu.head())

# 查看数据基本信息
print(weipangu.info())
```

数据中可能包含一些不需要的列，例如自动生成的索引列 `Unnamed: 0`。我们需要将其移除。

```python
# 重置索引，并将修改应用到原DataFrame
weipangu.reset_index(inplace=True)

# 使用iloc选择所有行，以及从第2列开始的所有列（移除前两列）
weipangu = weipangu.iloc[:, 2:]
print(weipangu.head())
```

**注意**：`iloc` 是基于位置的索引器。`weipangu.iloc[:, 2:]` 表示选择所有行（`:`），以及从索引2（第三列）开始到最后一列（`2:`）的所有列。Python的索引从0开始。

---

## 数据清洗与格式调整

在初步查看数据后，我们发现股票代码（ticker）的格式可能存在问题，例如前导零被Excel自动删除。我们需要将其恢复为标准格式。

以下是修复股票代码格式的步骤：

```python
# 定义一个函数，为股票代码填充前导零至6位
def fill_zero(ticker):
    return str(ticker).zfill(6)

# 将函数应用到‘ticker’列，并更新原数据
weipangu['ticker'] = weipangu['ticker'].apply(fill_zero)
print(weipangu[['ticker']].head())
```

**核心概念**：`apply()` 函数允许我们将一个自定义函数应用到DataFrame的某一列或行上。`zfill(6)` 是字符串方法，确保字符串长度至少为6位，不足则在前面补零。

---

## 计算个股日收益率

数据准备就绪后，下一步是计算每只股票的日收益率。这是评估投资表现的基础。

我们可以使用Pandas内置的 `pct_change()` 函数方便地计算百分比变化。

```python
# 按股票代码分组，然后计算每日收盘价的百分比变化
weipangu['daily_return'] = weipangu.groupby('ticker')['close'].pct_change()
print(weipangu[['ticker', 'date', 'close', 'daily_return']].head())
```

**公式解释**：日收益率 $r_t$ 的计算公式为：
$r_t = \frac{P_t - P_{t-1}}{P_{t-1}}$
其中 $P_t$ 代表当日收盘价，$P_{t-1}$ 代表前一日收盘价。`pct_change()` 函数正是执行此计算。

为了帮助理解，我们也可以用 `shift()` 函数手动实现：

```python
# 手动计算收益率的等价方法
weipangu['manual_return'] = (weipangu['close'] / weipangu.groupby('ticker')['close'].shift(1)) - 1
```

`shift(1)` 将数据向下移动一行，从而获取前一天的收盘价。

---

## 构建等权重投资组合

现在，我们有了每只股票的日收益率。假设我们构建一个等权重投资组合，即每只股票分配相同的资金。那么，投资组合的日收益率就是所有成分股票当日收益率的简单平均。

以下是计算投资组合日收益率的步骤：

首先，定义一个函数来计算给定一组收益率下的等权重组合收益。

```python
def calculate_portfolio_return(group):
    """
    计算一组股票（同一天）的等权重平均收益率。
    group: 包含同一天所有股票收益率的DataFrame分组。
    """
    valid_stocks = group['daily_return'].count()  # 计算有效股票数量
    if valid_stocks == 0:
        return np.nan  # 如果没有有效数据，返回NaN
    # 等权重组合收益 = 所有股票收益率之和 / 股票数量
    return group['daily_return'].sum() / valid_stocks
```

接着，我们按日期分组，并应用这个函数。

```python
# 按日期分组，并计算每日的投资组合收益率
portfolio_return = weipangu.groupby('date').apply(calculate_portfolio_return)
portfolio_return.name = 'portfolio_return'  # 为得到的Series命名
print(portfolio_return.head())
```

**代码解析**：`groupby(‘date’)` 将数据按日期分组。`apply(calculate_portfolio_return)` 对每个日期分组应用我们定义的函数，计算该日所有股票的平均收益率。结果是一个以日期为索引的Series。

---

## 计算并可视化累计收益率

得到投资组合的日收益率序列后，我们可以计算其累计收益率，从而观察投资组合在一段时间内的整体表现。

累计收益率的计算是将每日的收益率进行连乘（复利计算）。

```python
# 将投资组合收益率Series转换为DataFrame，便于操作
portfolio_df = pd.DataFrame(portfolio_return)

# 计算累计收益率: (1 + r1) * (1 + r2) * ... * (1 + rn) - 1
portfolio_df['cumulative_return'] = (1 + portfolio_df['portfolio_return']).cumprod() - 1
print(portfolio_df.head())
```

**核心概念**：`cumprod()` 函数计算序列的累计乘积。`(1 + portfolio_df[‘portfolio_return’]).cumprod()` 实现了 $(1+r_1)(1+r_2)...(1+r_n)$ 的计算。

最后，我们使用Matplotlib库将累计收益率绘制成曲线图。

```python
import matplotlib.pyplot as plt

# 设置画布大小
plt.figure(figsize=(12, 6))

# 绘制累计收益率曲线
plt.plot(portfolio_df.index, portfolio_df['cumulative_return'])

# 添加图表标签
plt.title('微盘股等权重投资组合累计收益率 (2020-2024)')
plt.xlabel('日期')
plt.ylabel('累计收益率')
plt.xticks(rotation=45)  # 旋转X轴标签，避免重叠
plt.grid(True)

# 显示图形
plt.tight_layout()
plt.show()
```

---

## 策略表现分析与总结

通过生成的收益率曲线图，我们可以对微盘股投资组合的历史表现进行分析。从示例图（假设）中可以看到，在2020年至2024年间，该组合经历了冲高和回落的过程。

*   **市场周期**：在牛市阶段（如示例中的某些时段），小盘股和微盘股往往表现出更高的弹性，涨幅可能更大。
*   **近期表现**：根据示例数据，在近期市场调整中，微盘股组合出现了较大回撤，这提示了投资此类资产的高波动性风险。
*   **策略局限性**：本项目构建的是一个简单的**等权重、买入持有**策略，没有考虑择时、调仓、交易成本等因素。在实际投资中，策略要复杂得多。

本节课中我们一起学习了：
1.  如何使用Pandas读取和处理大规模的股票数据。
2.  如何清洗数据并调整格式（如修复股票代码）。
3.  如何利用 `groupby()`、`pct_change()` 和 `apply()` 函数计算个股及投资组合的收益率。
4.  如何使用 `cumprod()` 计算累计收益率，并用Matplotlib进行可视化。
5.  如何对一个简单的投资策略进行初步的实现和表现回顾。

![](img/9993f1605e149e7599cb2f3362caa198_2.png)

这个项目为我们后续学习更复杂的量化策略（如动量策略、均值回归等）打下了坚实的基础。在接下来的课程中，我们将在此基础上，引入因子分析、风险模型和更精细的回测框架。