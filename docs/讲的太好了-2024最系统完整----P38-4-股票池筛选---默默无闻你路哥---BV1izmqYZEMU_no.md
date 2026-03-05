# Python金融分析与量化交易实战教程：P38：4-股票池筛选

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_0.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_2.png)

## 概述
在本节课程中，我们将学习如何对因子进行预处理，并基于处理后的因子对股票池进行筛选。我们将完成三个核心预处理步骤：去除离群值、标准化和中性化，然后根据因子值筛选出目标股票，为后续的调仓交易操作做准备。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_4.png)

上一节我们介绍了中性化操作，本节中我们来看看如何将预处理步骤串联起来，并执行股票筛选。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_6.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_8.png)

## 因子预处理流程
现在我们已经完成了中性化操作的编写。接下来，我们可以在此处执行之前写好的几个预处理操作。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_10.png)

我们将逐一执行这些操作。首先，改写预处理操作函数。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_12.png)

在预处理操作中，我们将按顺序编写第一个步骤。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_14.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_16.png)

### 1. 去除离群值 (3-Sigma)
第一个操作是进行三西格玛处理。我们需要传入处理对象。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_18.png)

处理对象是市值还是因子？是对因子进行处理。因此，我们需要先获取因子数据。

这里，我们的函数还没有返回值，需要为其添加一个返回值。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_20.png)

现在，我们得到一个返回值。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_22.png)

将其复制过来，命名为 `fundamental` 并转换为 DataFrame。这个 DataFrame 将包含我们所需的指标。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_24.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_26.png)

将其传入，处理对象就是我们之前计算好的市净率因子。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_28.png)

这样，我们就完成了第一步：去除离群值。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_30.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_32.png)

去除离群值之后，我们的指标就不再包含极端值了。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_34.png)

完成去除极值后，操作并未结束，我们还需要进行下一步。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_36.png)

### 2. 标准化
下一步是进行标准化操作。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_38.png)

将当前去除离群值后的结果传入，进行标准化处理。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_40.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_42.png)

这相当于一个标准化操作。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_44.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_46.png)

直接调用之前写好的 `standardize` 函数。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_48.png)

将结果赋值给一个新变量，例如 `standardized_factor`。这样就完成了标准化操作。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_50.png)

### 3. 中性化
完成标准化操作后，接下来还有中性化操作。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_52.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_54.png)

再次复制代码，将其命名为预处理后的因子，例如 `preprocessed_factor`。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_56.png)

将其等于我们上一步标准化后的结果。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_58.png)

现在，我们就完成了所有必需的预处理操作。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_60.png)

## 基于因子进行股票筛选
接下来，我们需要基于这个预处理后的因子对股票池进行筛选或调仓。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_62.png)

我们将进行筛选操作。思考一下如何进行筛选。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_64.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_66.png)

我们需要指定一个阈值。例如，在量化策略中，市净率因子是越小越好。

因此，对于当前这个指标，我们希望筛选出数值最小的前20%的股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_68.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_70.png)

我们来指定一下筛选条件。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_72.png)

我们选择前20%，即因子值最小的那部分股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_74.png)

这构成了我们的因子策略。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_76.png)

接下来就要进行选股。在选股过程中，我们需要查看当前得到的结果。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_78.png)

这是我们的因子数据。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_80.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_82.png)

基于这个因子，我们对所有股票进行判断和筛选。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_84.png)

在这里，如果某些股票的指标值不满足我们的要求，我们将其排除。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_86.png)

我们的要求是什么？我们之前提到，通常希望市净率小一些，这样可能带来更高的期望收益。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_88.png)

因此，这里我们设定条件为：因子值小于等于20%分位数的值。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_90.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_92.png)

我们先筛选出因子值较小（即市净率较低）的那部分股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_94.png)

然后，我们需要找到这些符合条件的股票代码。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_96.png)

这个判断是基于因子值进行的。判断完成后，我们得到的是满足条件的股票索引，即我们想要的这批股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_98.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_100.png)

我们获取这批股票代码。稍后将对它们进行交易操作。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_102.png)

我们先将这个股票列表存入 `context` 变量中。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_104.png)

`context.stock_list` 等于我们当前的筛选结果。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_106.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_108.png)

这样，我们就有了一个 `stock_list`。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_110.png)

## 准备调仓：识别需卖出的股票
接下来，我们要执行调仓以及买入卖出的操作。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_112.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_114.png)

要进行买卖操作，你需要知道买什么，以及哪些股票需要卖出。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_116.png)

剩下的股票将从股票池中选取。首先，我们需要查看哪些股票不在当前因子选中的池子中。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_118.png)

我们需要删除不在当前因子选中池子中的股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_120.png)

我们来编写删除操作。删除后，我们也需要知道删除了哪些股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_122.png)

返回值将包含所有需要删除的股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_124.png)

思考一下，哪些股票是需要删除的？肯定是之前持有，但不在当前股票池中的股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_126.png)

这是我当前的股票池。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_128.png)

将其复制过来，并用括号括起来。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_130.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_132.png)

我们需要找出当前持有的股票与当前股票池的差异。这里使用集合的 `difference` 操作。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_134.png)

操作对象是谁？是当前我们手中持有的股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_136.png)

我们先获取手中持有的股票。在 `context` 中，我们需要查找账户信息。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_138.png)

在账户信息中，我们需要查看持仓情况。使用 `context.portfolio.positions` 来获取。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_140.png)

这是我们现在手中持有的股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_142.png)

然后，用当前持有的股票集合，与当前股票池集合进行差异比较。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_144.png)

`difference` 的含义是：找出在当前持有但不在目标池子中的股票。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_146.png)

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_148.png)

我们将这些股票代码获取出来。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_150.png)

这样，我们就完成了识别需卖出股票的任务。

![](img/5e5f9b3d0bd113928b8baccb3cb0e7a4_152.png)

## 总结
本节课中，我们一起学习了完整的因子预处理流程，包括去除离群值、标准化和中性化。随后，我们基于处理后的因子值，设定了筛选条件（如前20%），选出了目标股票列表。最后，我们通过比较当前持仓与目标股票池，识别出了需要卖出的股票，为下一节的调仓交易操作做好了准备。