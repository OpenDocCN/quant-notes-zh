# Python金融量化交易：P26：策略效果演示与指标分析 📊

在本节课中，我们将学习如何对量化策略进行效果演示与指标分析。我们将通过调整数据展示格式、实现交易逻辑，并最终解读回测结果，来评估一个简单策略的表现。

---

上一节我们介绍了如何筛选股票并准备数据，本节中我们来看看如何调整数据格式以便于后续处理。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_1.png)

我们最初的想法是横向排列每个股票名称，即第一个股票、第二个股票、第三个股票、第四个股票。但通常大家更喜欢纵向排列，即第一个股票及其财务营收数据，第二个股票及其财务营收数据。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_1.png)

因此，我们需要对数据结构进行修改。在执行结果时，我们将对最终的`DataFrame`进行调整。

其实只需要对它做一个转置操作就可以了。我们来打印一下它的转置结果，先验证这个操作是否正确，如果正确，我们再继续。

```python
# 对DataFrame进行转置
transposed_df = original_df.T
print(transposed_df)
```

![](img/e891e3c4be0ad0eb28d2a4a44e505351_3.png)

转置完成后，我们的工作并未结束。在`before_trading`函数中，我们的最终目的不是仅仅打印出来查看，而是要获取筛选出的那十只股票。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_5.png)

我们最终想要得到的结果，应该是对数据先进行转置，然后查看转置后的结果。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_7.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_3.png)

转置结果如图所示，数据变为纵向排列。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_9.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_5.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_11.png)

这样，每一行就是一个股票名称，后面跟着其实际的指标值。第一列是索引（`index`）。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_7.png)

后面就是实际的值。此时，我们只需要获取索引（即股票代码）即可。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_13.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_9.png)

看起来间隔太短，看不出明显变化。我们暂时不管，稍后会在策略分析中处理。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_15.png)

在这里，我将获取其`index`，即我想要的股票名称列表。然后，我将这个列表重新设置到`context`上下文中，作为稍后要返回的结果，或者说将其存储在我的`context`中。

```python
# 获取股票列表并存入context
context.stock_list = transposed_df.index.tolist()
```

![](img/e891e3c4be0ad0eb28d2a4a44e505351_17.png)

这一块就是我们从沪深300中筛选出的十个股票。好了，我们为它随便起个名字。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_13.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_19.png)

现在，我们不再需要打印这些中间结果了。这样，在`before_trading`函数中，我们在交易开始前就已经完成了筛选工作。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_21.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_15.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_23.png)

接下来是`handle_bar`函数，这里我们要实际执行交易操作。我们不再使用`pass`占位，而是开始编写具体逻辑。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_17.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_25.png)

第一步是进行判断。判断当前`context`中，或者说当前我是否持有一些股票。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_27.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_28.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_19.png)

我们先查看相关API，然后参考API进行编写。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_30.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_21.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_32.png)

实际上，所有示例都是通过查阅API一点点构建出来的。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_34.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_23.png)

API中有很多可解释的内容。我们找一下`context`相关的部分。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_36.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_25.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_38.png)

不在这里，在交易相关的部分。我们找一下`context`。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_40.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_27.png)
![](img/e891e3c4be0ad0eb28d2a4a44e505351_28.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_42.png)

这是行情和股票数据，不是我们需要的。还是回到刚才那里。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_30.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_44.png)

我记得这里有`context`，找一下交易相关的。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_46.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_32.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_48.png)

我们稍后也会用到。这里，`context`找到了。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_34.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_50.png)

这里包含我们投资组合的信息。我直接复制过来编写。我需要查看当前账户中的信息情况。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_52.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_36.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_54.png)

这里有一个关键指标叫做`positions`。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_38.png)

我们点进去看看。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_40.png)

它有一些属性。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_56.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_42.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_58.png)

我们查看一下。我们稍后会用到这个`positions`。它是一个包含所有仓位的字典，描述了你现在持有哪些资产。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_44.png)

好了，我们来编写代码。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_60.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_46.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_62.png)

首先，获取`context`。我刚才复制了`context`的相关信息。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_48.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_64.png)

我先复制过来，看看手里有什么资产。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_66.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_50.png)

关闭API页面。在这里，我先进行判断。查看手里有什么资产，是通过`positions`来获取的。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_68.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_52.png)

因为`positions`是一个描述所有仓位的字典，所以我们先获取它。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_54.png)

既然它是一个字典，那么`keys()`中存储的就是你持有的所有资产代码。我先做一个判断：如果字典中所有持有的资产都是空的，那说明你现在手里什么也没有。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_70.png)

如果为空，我需要做什么？如果不为空（即不等于零），我们从第二天开始就需要不断进行判断。因为只要不为空，我们就要考虑卖出一些资产。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_56.png)

我们需要比较手里已有的资产和当前股票池中筛选出来的资产是否一致。

以下是遍历当前持有资产的逻辑：

```python
# 遍历当前持有的所有股票
for stock in context.portfolio.positions.keys():
    # 如果当前持有的股票不在最新的股票池中
    if stock not in context.stock_list:
        # 执行卖出操作，卖出全部（占比设置为0）
        order_target_percent(stock, 0)
```

![](img/e891e3c4be0ad0eb28d2a4a44e505351_72.png)

接下来，我们还需要执行买入操作。以下是遍历股票池并执行买入的逻辑：

```python
# 遍历当前股票池中的所有股票
for stock in context.stock_list:
    # 执行买入操作，平均分配资金（例如每只股票占1/10）
    order_target_percent(stock, 1.0 / len(context.stock_list))
```

![](img/e891e3c4be0ad0eb28d2a4a44e505351_74.png)

卖完了，剩下的就该买了。还是使用同一个函数，我直接复制过来。买入当前股票，但买多少呢？这个策略大家可以自己设定。你可以根据财务数据或其涨幅设置一个百分比。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_76.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_74.png)

或者我们简单点，采用平均分配的方式，让每只股票占整体资金的一部分。

```python
# 计算平均每只股票应占的资金比例
average_weight = 1.0 / len(context.stock_list)
for stock in context.stock_list:
    order_target_percent(stock, average_weight)
```

![](img/e891e3c4be0ad0eb28d2a4a44e505351_78.png)

好了，我们不管具体买多少，就平均买入。删除下面多余的代码。最后检查一下，最后一个`pass`没问题。这样，我们的策略基本编写完成了。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_80.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_80.png)

我们先运行一下，看看结果有什么问题。稍后我们会解释结果中包含的各项信息是什么意思。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_82.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_82.png)

哎呀，看起来结果有点恐怖，直接亏损了30%多。这可能是因为我们选择的时间段有关。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_84.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_84.png)

![](img/e891e3c4be0ad0eb28d2a4a44e505351_86.png)

稍后我们换个时间再来看结果。好了，刚才吓我一跳，可能刚开始的时候比较亏损。回测的年化收益比基准年化收益看起来更糟糕。

现在，我设计了一个策略，我们先从整体上看这些指标。主要看前四个指标，其他指标我们后续用到时再解释。

看指标主要看两点：
1.  你设计的策略的回测效果。
2.  跟随指数（即什么都不做）时的效果。

看起来反正都是赔了，但我们的策略能让你少赔一点。最大回撤控制在17%左右，还算可以。夏普比率肯定是负的，因为已经亏损，单位风险不值得。

这是一个收益的概况，我们大致画出了这样一个结果。右边还有交易详情，大家可以点进去查看。

![](img/e891e3c4be0ad0eb28d2a4a44e505351_88.png)

---

![](img/e891e3c4be0ad0eb28d2a4a44e505351_88.png)

本节课中我们一起学习了如何对量化策略进行效果演示与指标分析。我们调整了数据格式，实现了包含买卖逻辑的`handle_bar`函数，并初步解读了回测结果中的关键指标，如年化收益、最大回撤和夏普比率。虽然示例策略在测试时间段内表现不佳，但这完整展示了策略开发、回测和评估的流程。