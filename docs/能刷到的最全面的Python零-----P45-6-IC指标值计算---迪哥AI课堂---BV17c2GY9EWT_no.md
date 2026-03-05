# Python金融分析量化交易教程：P45：6-IC指标值计算 📊

在本节课中，我们将学习如何计算IC指标值。IC值是衡量因子（如技术指标）与未来收益率之间相关性的重要指标，是量化交易策略评估的核心步骤。我们将从获取数据开始，逐步完成数据格式转换，并最终计算出斯皮尔曼秩相关系数。

## 数据准备：获取收盘价

上一节我们介绍了因子的计算，本节中我们来看看如何获取计算IC值所需的实际收益率数据。计算IC值需要将因子值与股票的实际收益率进行关联计算。因此，我们首先需要知道股票每日的实际收盘价格，以便计算收益率。

以下是获取收盘价数据的步骤：

1.  使用 `get_price` 函数获取指定股票池在特定时间段内的价格数据。
2.  从返回的多维价格数据中，提取出我们需要的“收盘价”数据，并将其转换为二维的 `DataFrame` 格式。
3.  为数据框设置清晰的索引和列名，便于后续处理。

![](img/1e9ea4efccf215c2fe274ad68cfbcb44_1.png)

```python
# 获取股票池的收盘价数据
price = get_price(
    stock_pool,  # 股票池列表
    start_date='2019-01-01',  # 起始日期
    end_date='2020-01-01',  # 结束日期
    fields=['close']  # 指定获取收盘价字段
)

# 提取收盘价数据并整理格式
close_price = price['close']  # 假设返回的是多维数据，提取收盘价部分
close_price.index.name = 'date'  # 设置索引名为‘date’
close_price.columns.name = 'code'  # 设置列名为‘code’
```
执行上述代码后，我们将得到一个二维的 `DataFrame`，其索引是日期，列是股票代码，对应的值是每日的收盘价。

![](img/1e9ea4efccf215c2fe274ad68cfbcb44_3.png)

## 数据格式转换

现在我们已经有了因子数据和价格数据。为了计算IC值，我们需要使用一个特定的工具函数将数据转换为要求的格式。这个函数通常较长，无需记忆，可以直接从相关文档或代码库中复制使用。

以下是进行数据格式转换的步骤：

1.  从工具库（如 `utils`）中导入或复制数据转换函数。
2.  将我们准备好的因子数据和收盘价数据作为参数传入该函数。
3.  函数会返回一个结构化的数据对象，其中包含了按日期、股票代码组织的因子值、未来收益率以及因子分组信息。

```python
# 假设从 utils 模块导入数据转换函数
from utils import convert_to_analysis_format

# 进行数据格式转换
analysis_data = convert_to_analysis_format(
    factor_data=processed_factor_data,  # 之前处理好的因子数据
    price_data=close_price  # 上一步获取的收盘价数据
)
```
转换后的数据对象会包含多个字段，例如：
*   `factor`: 因子值。
*   `1D`、`5D`、`10D`: 分别代表未来1天、5天、10天的收益率。我们主要使用 `1D`（即次日收益率），其计算公式为：**收益率 = (今日收盘价 - 昨日收盘价) / 昨日收盘价**。
*   `factor_quantile`: 系统自动根据因子值的大小，将其划分到5个分组中（例如1到5），数字越大代表该时刻的因子值在同组股票中排名越高。

## 计算IC指标值

数据准备就绪后，我们就可以计算IC指标值了。IC值通常指斯皮尔曼秩相关系数，它衡量的是因子排名与未来收益率排名之间的单调关系。我们将使用量化库中现成的性能分析函数来完成计算。

![](img/1e9ea4efccf215c2fe274ad68cfbcb44_5.png)

以下是计算IC值的步骤：

![](img/1e9ea4efccf215c2fe274ad68cfbcb44_7.png)

![](img/1e9ea4efccf215c2fe274ad68cfbcb44_9.png)

1.  从性能分析模块（如 `performance`）中导入或找到计算因子信息系数（IC）的函数。
2.  将上一步转换好的 `analysis_data` 作为参数传入该函数。
3.  函数会返回一个包含每日IC值等信息的序列或数据框。

```python
# 假设从 performance 模块导入计算IC的函数
from performance import factor_information_coefficient

![](img/1e9ea4efccf215c2fe274ad68cfbcb44_11.png)

# 计算IC值
ic_series = factor_information_coefficient(analysis_data)
```
计算完成后，`ic_series` 中存储的就是我们需要的每日IC指标值。我们可以通过 `ic_series.head()` 查看前几天的IC值，或对其进行进一步分析，如计算IC均值、ICIR（信息比率）等，以评估因子的预测能力。

![](img/1e9ea4efccf215c2fe274ad68cfbcb44_13.png)

本节课中我们一起学习了IC指标值的完整计算流程。我们从获取收盘价数据开始，经历了数据格式转换，最终利用专用函数计算出了衡量因子与收益率相关性的IC值。这是检验量化因子有效性的关键一步，为后续的策略构建与回测奠定了基础。