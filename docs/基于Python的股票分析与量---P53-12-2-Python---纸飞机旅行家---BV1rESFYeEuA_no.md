# 基于Python的股票分析与量化交易入门到实践 - P53：12.2 Python量化交易--因子分析_量化因子分析--自定义因子实战

![](img/02afddc58235bfb6bb09e290821ef25c_1.png)

在本节课中，我们将学习如何创建和使用自定义量化因子，并通过单因子分析来评估其有效性。自定义因子是量化策略开发的核心，它允许我们根据自己的投资逻辑构建独特的选股指标。

![](img/02afddc58235bfb6bb09e290821ef25c_3.png)

![](img/02afddc58235bfb6bb09e290821ef25c_5.png)

上一节我们介绍了因子分析的概述，本节中我们来看看如何具体实现一个自定义因子。

![](img/02afddc58235bfb6bb09e290821ef25c_7.png)

## 自定义因子类介绍

![](img/02afddc58235bfb6bb09e290821ef25c_9.png)

自定义因子类继承自平台内置的`Factor`类，它由三个基本属性和一个核心函数构成。

以下是自定义因子类的三个基本属性：

*   **`name`**：自定义因子的名称。它只能由字母、数字和下划线构成，且第一个字符不能是数字。名称不能与Python的关键词相同，也不能与基础因子（如财务类因子、技术指标）的名称重复。
*   **`max_window`**：获取数据的最长时间窗口。通常指日线数据，例如`max_window=10`表示需要最近10天的数据。
*   **`dependencies`**：自定义因子所依赖的基础因子名称列表。例如，可以依赖`‘close’`（收盘价）、`‘high’`（最高价）等。

![](img/02afddc58235bfb6bb09e290821ef25c_11.png)

核心函数是`compute`，它负责实现因子的具体计算逻辑。

## 自定义因子的使用流程

使用自定义因子进行分析通常遵循以下三个步骤：

1.  **构造自定义因子的属性**：即定义上述的`name`、`max_window`和`dependencies`。
2.  **实现因子计算**：在`compute`函数中编写计算逻辑。
3.  **进行因子分析**：使用分析函数（如单因子分析）来评估因子的表现。

![](img/02afddc58235bfb6bb09e290821ef25c_13.png)

## 核心函数：`compute`

`compute`函数是实现因子计算逻辑的地方。它接收一个参数`data`。

`data`是一个字典类型，其`key`是`dependencies`中定义的因子名称，`value`是对应的`DataFrame`格式数据表。

例如，一个计算10日均线的因子可以这样实现：
```python
def compute(self, data):
    # 获取过去10天的收盘价数据
    close_data = data[‘close’]
    # 计算10日均线
    ma_10 = close_data.mean(axis=1)
    return ma_10
```
该函数返回一个`pandas.Series`，其`index`为股票代码，`value`为计算出的因子值。

![](img/02afddc58235bfb6bb09e290821ef25c_15.png)

## 单因子分析函数

![](img/02afddc58235bfb6bb09e290821ef25c_17.png)

自定义因子构造完成后，我们需要通过分析来检验其有效性。本节介绍常用的`analyze_factor`函数进行单因子分析。

该函数参数较多，以下是几个关键参数的解释：

*   **`factor`**：要分析的因子。可以传入自定义因子类的实例，也可以直接传入计算好的`DataFrame`或`Series`。本节主要使用自定义因子实例。
*   **`start_date` / `end_date`**：分析的回测开始和结束日期。
*   **`industry`**：行业分类标准，默认为聚宽一级(`‘jq_l1’`)。支持申万、聚宽、证监会等多种分类。
*   **`universe`**：股票池。可以是字符串（如`‘000300.XSHG’`代表沪深300指数成分股），也可以是股票代码列表。**注意**：当`factor`参数传入的是`DataFrame`或`Series`时，此参数无效。
*   **`quantiles`**：分位数的数量。用于将股票按因子值分组。
*   **`periods`**：调仓周期列表，例如`[1,5,10]`，表示在1天、5天、10天后检查并调整仓位。
*   **`weight_method`**：计算收益的加权方法。例如`‘equal’`（等权）或`‘market_value’`（市值加权）。
*   **`use_real_price`**：是否使用动态复权价格，默认为`False`。

## 代码实战：构建10日均线因子

![](img/02afddc58235bfb6bb09e290821ef25c_19.png)

讲解了这么多理论知识，现在让我们进入代码实战环节。请注意，因子分析更偏向于研究，因此建议在研究环境中进行，以便更好地查看图表和分析结果。

首先，我们导入必要的模块并忽略警告信息。
```python
import warnings
warnings.filterwarnings(‘ignore’)
from jqfactor import Factor, analyze_factor
```

接下来，我们定义一个计算10日均线的自定义因子类`MA10`。
```python
# 定义10日均线因子类
class MA10(Factor):
    # 设置因子名称
    name = ‘ma10’
    # 设置最大数据窗口为10天
    max_window = 10
    # 设置依赖的基础因子为收盘价
    dependencies = [‘close’]

    # 实现因子计算逻辑
    def compute(self, data):
        # 获取过去10天的收盘价数据
        close_data = data[‘close’]
        # 计算10日收盘价的均值，即10日均线
        ma_10 = close_data.mean(axis=1)
        return ma_10
```

自定义因子类构建完成后，我们使用`analyze_factor`函数对其进行单因子分析。
```python
# 进行单因子分析
factor = MA10() # 实例化我们的因子
start_date = ‘2022-01-01’
end_date = ‘2022-12-31’
weight_method = ‘market_value’ # 使用市值加权
universe = ‘000300.XSHG’ # 股票池为沪深300指数成分股
industry = ‘jq_l1’ # 使用聚宽一级行业分类
quantiles = 8 # 分为8组
periods = (1, 5, 10) # 考察1，5，10天的调仓周期

result = analyze_factor(
    factor=factor,
    start_date=start_date,
    end_date=end_date,
    weight_method=weight_method,
    universe=universe,
    industry=industry,
    quantiles=quantiles,
    periods=periods
)
```

运行上述代码后，我们将得到因子分析的结果，其中包含了信息系数（IC）、分组收益等多项指标，可用于评估`MA10`因子的选股能力。

## 总结

![](img/02afddc58235bfb6bb09e290821ef25c_21.png)

本节课中我们一起学习了量化因子分析中自定义因子的创建与实战。我们首先介绍了自定义因子类，它继承自`Factor`类，包含`name`、`max_window`、`dependencies`三个基本属性和一个核心的`compute`函数。接着，我们讲解了使用自定义因子的标准流程：构造属性、实现计算、进行分析。然后，我们详细说明了用于单因子分析的`analyze_factor`函数及其关键参数。最后，在代码实战环节，我们构建了一个简单的10日均线因子`MA10`，并演示了如何对其进行单因子分析，从而完成了从理论到实践的完整学习路径。