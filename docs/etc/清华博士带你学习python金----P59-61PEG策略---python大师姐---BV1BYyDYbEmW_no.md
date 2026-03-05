# Python金融量化投资分析与股票交易：P59：PEG策略

## 概述
在本节课中，我们将学习一个由著名投资人彼得·林奇提出的选股策略——PEG策略。我们将理解其核心概念、计算公式，并最终用Python代码实现它。

![](img/fb950e7dd63bf9cc8a377aa17c17380d_1.png)

---

## 核心概念解析

上一节我们介绍了多种选股策略，本节中我们来看看PEG策略。

PEG策略源于彼得·林奇的观点：任何一家公司股票，如果定价合理，其市盈率就会与收益增长率相等。

### 市盈率（PE）
市盈率是一个重要的金融指标，用于评估公司估值。其计算公式为：
**PE = 股价(P) / 每股收益(EPS)**

为了便于理解，我们可以将公式变形：
**PE = 总市值 / 净利润**

市盈率可以粗略地理解为：在当前盈利水平不变的情况下，投资者需要多少年才能收回投资成本。例如，一家市盈率为2的公司，意味着大约2年可以回本。

### 收益增长率（G）
收益增长率衡量的是公司盈利的增长速度。其计算公式为：
**G = (当前EPS - 上一期EPS) / 上一期EPS**

这个指标反映了公司盈利能力的成长性。

### PEG指标
结合市盈率和收益增长率，我们得到PEG指标。根据彼得·林奇的理论，在股价合理时，**PE = G**。因此，我们构建指标：
**PEG = PE / G**

为了将其转换为百分数形式，有时会写作：
**PEG = (PE / G) * 100**

*   **PEG ≈ 1**：股价可能处于合理估值。
*   **PEG < 1**：可能意味着股价被低估，投资价值较高。
*   **PEG > 1**：可能意味着股价被高估。

PEG策略的核心思想是：选择PEG值最低的N只股票，因为它们被低估的可能性更大。该策略综合考察了公司当前价值（PE）和未来成长性（G），尤其适用于成长型公司。

---

![](img/fb950e7dd63bf9cc8a377aa17c17380d_3.png)

## 策略实施要点

理解了PEG的计算原理后，我们来看看在实施策略时需要注意的关键点。

以下是实施PEG策略的具体步骤和注意事项：

1.  **数据获取**：需要获取股票池中所有股票的股价（用于计算PE）和每股收益EPS数据（用于计算PE和G）。
2.  **计算指标**：为每只股票计算其PE值和G值，进而得到PEG值。
3.  **过滤无效值**：必须过滤掉PEG值为负或无效的股票。这通常是因为：
    *   **PE为负**：意味着公司净利润为负（亏损），此类股票应排除。
    *   **G为负**：意味着公司盈利在倒退，成长性为负，也应排除。
4.  **选股**：在过滤后的股票中，选取PEG值最小的N只股票作为投资组合。
5.  **调仓**：按照策略设定的周期（如每月、每季度），重复以上步骤，更新投资组合。

---

## Python代码实现

![](img/fb950e7dd63bf9cc8a377aa17c17380d_5.png)

理论部分已经清晰，接下来我们就开始写代码。我们将使用`pandas`库进行数据处理。

假设我们已经有了一个包含股票代码、股价、当前EPS和上一期EPS的DataFrame `df`。

![](img/fb950e7dd63bf9cc8a377aa17c17380d_7.png)

![](img/fb950e7dd63bf9cc8a377aa17c17380d_9.png)

```python
import pandas as pd

![](img/fb950e7dd63bf9cc8a377aa17c17380d_11.png)

# 假设 df 是一个DataFrame，包含以下列：’symbol‘（股票代码），’price‘（股价），’eps_current‘（当前EPS），’eps_previous‘（上一期EPS）
# df = pd.read_csv(‘your_stock_data.csv’)

![](img/fb950e7dd63bf9cc8a377aa17c17380d_13.png)

def calculate_peg(df):
    """
    计算PEG值
    """
    # 1. 计算市盈率 (PE)
    df[‘PE‘] = df[’price‘] / df[’eps_current‘]

    # 2. 计算收益增长率 (G)，并避免除以零
    df[‘G‘] = (df[’eps_current‘] - df[’eps_previous‘]) / df[’eps_previous‘].replace(0, pd.NA)

    # 3. 计算PEG (这里乘以100转换为百分数形式，也可不乘)
    df[‘PEG‘] = (df[’PE‘] / df[’G‘]) * 100

    return df

![](img/fb950e7dd63bf9cc8a377aa17c17380d_15.png)

def peg_strategy(df, n=10):
    """
    执行PEG选股策略
    :param df: 包含PEG值的DataFrame
    :param n: 选择股票的数量
    :return: 选出的股票代码列表
    """
    # 1. 过滤掉无效数据：PE或G为负、空值或无穷大的行
    df_valid = df[
        (df[’PE‘] > 0) &
        (df[’G‘] > 0) &
        (df[’PEG‘] > 0) &
        (df[’PEG‘].notna()) &
        (df[’PEG‘] != float(‘inf‘))
    ].copy()

    # 2. 按PEG值升序排序（值越小越好）
    df_sorted = df_valid.sort_values(by=’PEG‘, ascending=True)

    # 3. 选择PEG最小的前n只股票
    selected_stocks = df_sorted.head(n)[’symbol‘].tolist()

    return selected_stocks

# 使用示例
# df_with_peg = calculate_peg(your_dataframe)
# portfolio = peg_strategy(df_with_peg, n=10)
# print(f“本期选中的股票代码：{portfolio}”)
```

**代码说明**：
*   `calculate_peg`函数负责计算PE、G和PEG三个核心指标。
*   `peg_strategy`函数是策略核心：
    *   首先严格过滤数据，确保PE和G为正，且PEG为有效正数。
    *   然后根据PEG值排序，选取最小值对应的股票。
*   在实际应用中，你需要将`your_dataframe`替换为从数据库或API获取的真实股票数据。

![](img/fb950e7dd63bf9cc8a377aa17c17380d_17.png)

---

![](img/fb950e7dd63bf9cc8a377aa17c17380d_19.png)

## 总结
本节课中我们一起学习了PEG策略。我们从彼得·林奇的理论出发，理解了市盈率（PE）和收益增长率（G）的含义，并掌握了PEG指标的计算公式及其投资逻辑。最后，我们使用Python实现了完整的PEG选股策略，包括数据计算、有效性过滤和股票筛选。该策略是一个将公司估值与成长性相结合的有效工具，特别适合用于发掘潜在的被低估的成长型公司。