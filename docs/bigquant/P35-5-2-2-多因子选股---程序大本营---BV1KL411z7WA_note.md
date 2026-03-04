# 量化投资：P35：5.2.2：多因子选股策略构建教程 📈

![](img/b4432b010a5e31cc1b0d2ff6d217600e_1.png)

在本节课中，我们将学习如何构建一个基于基本面因子的多因子选股策略。我们将通过市盈率、市净率和成交量等指标，每月初筛选并买入30只股票，并定期进行换仓。我们将从策略逻辑、数据处理到回测实现，完整地走一遍流程。

## 策略逻辑概述

该策略的核心逻辑是：**每月初，根据市盈率（PE）、市净率（PB）和成交量（Volume）三个因子，从全市场股票中筛选出30只符合条件的股票进行买入，并持有至下个换仓日**。策略在结构上与趋势策略类似，主要分为两步：首先通过因子抽取和数据处理生成交易信号，然后将信号传递给回测引擎执行模拟交易。

## 策略构建步骤详解

### 第一步：数据准备与因子抽取

首先，我们需要获取全市场股票在一段时间内的历史数据，并抽取所需的因子。

我们使用“证券代码列表”模块来定义回测的股票池和时间范围。如果不特别指定股票代码，则默认包含全市场股票。其输出数据格式通常是一个包含`start_date`、`end_date`和`stock_list`的字典。

```python
# 示例：获取全市场股票代码列表
data_input = {
    ‘start_date‘: ‘2020-01-01‘,
    ‘end_date‘: ‘2023-01-01‘,
    ‘stock_list‘: None  # None 代表全市场
}
```

接下来，我们使用“因子抽取”模块来获取每只股票在历史每一天的市盈率、市净率和成交量数据。运行该模块后，会得到一个`DataFrame`，其列通常包括：`date`（日期）、`code`（股票代码）、`pe_ratio`（市盈率）、`pb_ratio`（市净率）、`volume`（成交量）。

### 第二步：数据处理与股票筛选

![](img/b4432b010a5e31cc1b0d2ff6d217600e_3.png)

获取原始因子数据后，我们需要进行清洗和筛选，以得到最终的候选股票池。这主要涉及两个操作：**数据过滤**和**排序**。

![](img/b4432b010a5e31cc1b0d2ff6d217600e_5.png)

**数据过滤**的目的是剔除不符合条件的股票。我们的筛选条件是：
*   市净率 **< 1.5**
*   市盈率 **< 15**
*   成交量 **> 0** (剔除停牌股)

![](img/b4432b010a5e31cc1b0d2ff6d217600e_7.png)

![](img/b4432b010a5e31cc1b0d2ff6d217600e_9.png)

在代码中，这通常通过一个过滤表达式来实现。

![](img/b4432b010a5e31cc1b0d2ff6d217600e_11.png)

**排序**的目的是在我们倾向的因子方向上优选股票。我们认为市盈率和市净率较低的股票更具投资价值，因此对过滤后的数据，按市盈率和市净率进行**升序**排列。

```python
# 示例：数据处理步骤（伪代码）
# 1. 数据过滤
filtered_df = raw_df[(raw_df[‘pb_ratio‘] < 1.5) &
                     (raw_df[‘pe_ratio‘] < 15) &
                     (raw_df[‘volume‘] > 0)]

![](img/b4432b010a5e31cc1b0d2ff6d217600e_13.png)

# 2. 排序
sorted_df = filtered_df.sort_values(by=[‘pe_ratio‘, ‘pb_ratio‘], ascending=True)
```

经过以上两步，我们得到了一个经过清洗和排序的数据集，它将被传入交易引擎，作为生成交易信号的依据。

![](img/b4432b010a5e31cc1b0d2ff6d217600e_15.png)

### 第三步：回测引擎与策略逻辑编写

![](img/b4432b010a5e31cc1b0d2ff6d217600e_17.png)

上一节我们准备好了交易信号数据，本节中我们来看看如何在回测引擎中实现具体的买卖逻辑。核心代码写在三个函数中：`initialize`（初始化）、`prepare_data`（数据准备）和`handle_data`（主逻辑）。

**1. 初始化函数 (`initialize`)**
此函数在回测开始时运行一次，用于设置策略参数和初始化上下文。
*   设置交易手续费。
*   在上下文对象`context`中创建一个索引`context.index`，初始值为0。这个索引会随着每个交易日递增，用于实现定期换仓。

```python
def initialize(context):
    # 设置手续费
    set_commission(PerOrder(buy_cost=0.0003, sell_cost=0.0013))
    # 初始化K线索引，用于控制换仓周期
    context.index = 0
    # 设置每次买入的股票数量
    context.stock_num = 30
    # 设置换仓周期（天）
    context.hold_days = 22
```

![](img/b4432b010a5e31cc1b0d2ff6d217600e_19.png)

**2. 数据准备函数 (`prepare_data`)**
此函数用于处理从外部传入的过滤排序后的数据，计算出**每天应该买入的30只股票列表**。
*   读取外部数据。
*   使用`pandas`的`groupby`操作，按日期(`date`)对股票进行分组。
*   在每个分组内，选取排序靠前（即PE、PB最小）的30只股票，作为该日期的目标买入列表。

```python
def prepare_data(external_data):
    # external_data 是经过过滤和排序的DataFrame
    # 按日期分组，并取每个分组的前30行（股票）
    daily_buy_list = external_data.groupby(‘date‘).head(30)
    return daily_buy_list
```

**3. 主逻辑函数 (`handle_data`)**
此函数在每个交易日运行，是策略的核心。
*   首先，将`context.index`加1。
*   判断是否为换仓日：`context.index % context.hold_days == 0`。如果不是，则直接返回，不进行任何操作。
*   如果是换仓日：
    *   **卖出**当前持有的、但不在今日目标买入列表中的所有股票。
    *   **买入**今日目标买入列表中的所有股票，并采用等权重配置资金。

![](img/b4432b010a5e31cc1b0d2ff6d217600e_21.png)

```python
def handle_data(context, data):
    # 更新K线索引
    context.index += 1

    # 判断是否为换仓日（假设hold_days=22）
    if context.index % context.hold_days != 0:
        return

    # 获取今日的目标买入股票列表
    today = data.current_dt.strftime(‘%Y-%m-%d‘)
    target_stocks = prepare_data(context.filtered_data).loc[today, ‘code‘].tolist()

    # 卖出逻辑：卖出当前持有但不在目标列表中的股票
    for stock in context.portfolio.positions:
        if stock not in target_stocks:
            order_target_percent(stock, 0)  # 将该股票持仓比例调整为0，即全卖出

    # 买入逻辑：等权重买入目标列表中的股票
    if len(target_stocks) > 0:
        weight = 1.0 / len(target_stocks)
        for stock in target_stocks:
            order_target_percent(stock, weight)  # 买入该股票，使其持仓占比为weight
```

### 第四步：回测设置与运行

![](img/b4432b010a5e31cc1b0d2ff6d217600e_23.png)

最后，我们需要对回测引擎进行参数配置，然后运行策略。
*   **初始资金**：例如设置为1，000，000元。
*   **价格模式**：例如使用`‘close‘`（收盘价）进行交易。
*   **运行回测**：点击运行后，引擎将按日模拟交易，并输出结果。

![](img/b4432b010a5e31cc1b0d2ff6d217600e_25.png)

![](img/b4432b010a5e31cc1b0d2ff6d217600e_26.png)

回测结果通常包括：
*   **收益曲线图**：展示策略净值与基准（如沪深300指数）的对比。
*   **交易详情**：列出每一笔买卖记录。
*   **每日持仓与收益**：展示每日的持仓组合和当日收益。
*   **绩效指标**：如年化收益率、夏普比率、最大回撤等。
*   **日志输出**：帮助调试和理解策略运行过程。

![](img/b4432b010a5e31cc1b0d2ff6d217600e_28.png)

一个简单的基于低市盈率、低市净率的基本面选股策略，长期来看有较大概率跑赢市场指数，这背后是“价值投资”的逻辑支撑。回测结果可以验证这一逻辑的有效性。

## 总结

![](img/b4432b010a5e31cc1b0d2ff6d217600e_30.png)

本节课中，我们一起学习并实践了一个完整的**多因子选股策略**的构建过程。我们从策略逻辑（每月初买入低PE、低PB的30只股票并定期换仓）出发，逐步实现了：
1.  **数据准备**：获取全市场股票的PE、PB、成交量因子。
2.  **数据处理**：通过**过滤**（PB<1.5， PE<15， 成交量>0）和**排序**（PE、PB升序）筛选出候选股票。
3.  **策略编写**：在回测引擎中，通过`initialize`、`prepare_data`和`handle_data`三个函数，实现了定期换仓、卖出旧仓、等权重买入新仓的核心逻辑。
4.  **回测分析**：运行策略并解读收益曲线、交易详情等结果，以评估策略效果。

![](img/b4432b010a5e31cc1b0d2ff6d217600e_32.png)

![](img/b4432b010a5e31cc1b0d2ff6d217600e_34.png)

通过这个案例，你可以掌握多因子策略从构思到回测的基本框架，并可以尝试修改因子、调整参数或改变选股数量来创建属于自己的策略。