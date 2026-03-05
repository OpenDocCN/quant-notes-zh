# Python机器学习量化选择全流程：P1：代码解析与论文精读 📈

在本节课中，我们将学习一个完整的Python机器学习量化选股流程。我们将使用沪深300成分股数据，通过构建技术指标特征，应用多种机器学习模型（如XGBoost、SVM、随机森林、决策树）来预测股票涨跌，并进行策略回测与评估。本教程旨在为初学者提供一个清晰、可操作的框架。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_1.png)

## 概述与数据准备 📊

首先，我们介绍整个项目的流程框架。整个流程从数据选取开始，到最终策略性能评估结束。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_3.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_1.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_5.png)

我们选取的数据是沪深300指数的300只成分股。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_7.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_3.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_9.png)

接下来，我们需要基于原始数据构建用于模型训练的特征。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_5.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_11.png)

特征选择了一系列技术指标。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_7.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_13.png)

特征构建完成后，需要对数据进行标准化处理。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_9.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_15.png)

标准化之后，进行模型的搭建工作。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_17.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_11.png)

以及模型的参数调优。由于本次是演示，我们没有进行耗时的调参，但相关的调参代码已经提供。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_19.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_13.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_21.png)

调参代码已经列出，可供后续使用。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_23.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_15.png)

然后进入模型评估阶段。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_17.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_25.png)

模型评估的目标是预测涨跌，因此我们计算准确率。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_27.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_19.png)

此外，还计算召回率、F1得分、精确率，并绘制ROC曲线。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_29.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_21.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_31.png)

最后，我们在测试集上进行策略回测，绘制收益率曲线。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_33.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_23.png)

回测完成后，我们还分析了特征的重要性。最终对策略性能进行评估，计算年化收益率、波动率、夏普比率、索提诺比率、最大回撤、偏度、峰度和5% VaR值。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_35.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_25.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_37.png)

现在，让我们开始详细解析代码。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_27.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_39.png)

我将代码框放大，以便大家能更清楚地查看。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_41.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_29.png)

之前有反馈说代码看不清楚。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_43.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_31.png)

因此我进行了放大处理。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_45.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_33.png)

首先，我们导入股票日频数据。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_47.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_35.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_49.png)

数据包含300只股票，我们可以打印查看一下。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_37.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_51.png)

数据包含收盘价、开盘价、涨跌幅等字段。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_39.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_53.png)

总共有超过52万条数据。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_41.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_55.png)

数据时间范围是从2015年到2023年6月的沪深300成分股。接下来，我们对`pct_change`字段进行处理。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_57.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_43.png)

原始`pct_change`是乘以100后的值，这里我们将其除以100，还原为百分比小数形式。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_45.png)

然后，我们计算一个`shift_return`。原始数据没有后面这两列。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_47.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_59.png)

为了预测未来的目标变量，我们将`pct_change`向前移动一个单位。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_61.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_49.png)

例如，原来的`pct_change`是-0.0122，向前移动后，这个值就对应到了下一天。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_63.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_51.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_65.png)

移动操作使用了`groupby`函数，对每只股票的`pct_change`分别进行移动。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_53.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_67.png)

这里有一个关键点需要注意。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_55.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_69.png)

不能直接使用`df[‘pct_change’].shift(1)`。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_57.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_71.png)

而是必须使用`groupby`函数按股票代码分组后再进行`shift`。如果直接整体移动，会导致一只股票最后一天的收益率被错误地赋给下一只股票的第一天，造成数据污染。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_59.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_73.png)

因此我们使用`groupby`函数来确保移动的正确性。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_75.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_61.png)

## 特征工程 🛠️

![](img/bf95abcffd9a9c69a4827554fc5d44c8_77.png)

上一节我们完成了数据的基本处理，本节中我们来看看如何构建特征变量。特征变量我们选择了一系列技术指标。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_63.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_79.png)

技术指标包括过去1天、5天、10天和30天的收益率。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_65.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_81.png)

我们选择标准差来代表波动率，例如过去30天的波动率。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_83.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_67.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_85.png)

还选择了偏度和峰度。此外，成交量的增长率`pct_volume`也是一个特征。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_69.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_87.png)

`close`减`open`（当日涨跌幅），`high`减`low`（当日振幅）也作为衍生指标。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_89.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_71.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_91.png)

`prev_close`是前一天的收盘价。`price_diff`是价格差，注意这不是收益率。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_73.png)

`pct_change`才是收益率。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_93.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_75.png)

然后我们构建移动平均线指标，如`MA5`、`MA10`，也可以构建`MA20`。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_95.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_77.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_97.png)

还包括RSI动量指标、`EMA26`、MACD、KDJ等多个指标。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_99.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_79.png)

总共构建了多个技术指标。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_101.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_81.png)

例如`MA5`、`MA10`、RSI、动量效应、`EMA20`等等。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_103.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_83.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_104.png)

接下来，我们查看一下数据集的时间范围，从2015年到2023年。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_106.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_85.png)

为了加快演示时的训练速度，我们只使用最后一年的数据，即2022年6月到2023年6月。如果使用全部数据，训练速度会非常慢。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_108.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_87.png)

因为总数据量超过52万条。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_110.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_89.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_112.png)

本地电脑运行速度有限。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_91.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_114.png)

因此只选择最后一年数据作为案例展示。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_116.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_93.png)

设置好特征(`feature`)和标签(`label`)之后。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_118.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_95.png)

我们对特征数据进行标准化处理，使用`(x - mean) / std`，即Z-score标准化。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_120.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_97.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_122.png)

Z-score标准化。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_124.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_99.png)

可以查看一下数据，训练集是2022年6月到2023年6月，共48,568条数据。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_101.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_126.png)

测试集使用的是2023年2月到2023年6月，共4个月的数据。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_128.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_103.png)

训练集使用了8个月的数据。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_130.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_105.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_132.png)

构建特征变量和目标变量，即`feature_names`和`label_name`。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_107.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_134.png)

查看数据维度，有21个特征，训练集48,568条数据。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_109.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_136.png)

测试集24,600多条数据。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_138.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_111.png)

## 模型构建与评估 🤖

![](img/bf95abcffd9a9c69a4827554fc5d44c8_140.png)

上一节我们准备好了标准化后的数据，本节中我们进行模型构建和参数调优。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_142.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_113.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_144.png)

这里列出了调参和不调参两种代码。不调参直接使用默认参数训练。由于数据量较大，本次演示不进行调参。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_115.png)

如果大家想进行调参，相关代码也已列出，包括决策树(DT)、随机森林(RF)、支持向量机(SVM)和XGBoost的参数网格。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_146.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_117.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_147.png)

大家可以使用这些代码进行调参，但这需要较好的电脑配置和更长的运行时间。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_119.png)

本次不调参的运行时间大约需要10分钟。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_121.png)

模型训练部分会运行较长时间。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_149.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_123.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_151.png)

接下来，开始预测下一天的涨跌。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_152.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_125.png)

为了绘制ROC曲线，我们需要模型预测的概率值。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_154.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_127.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_156.png)

ROC曲线是根据预测为上涨的概率来计算的。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_129.png)

因此，我们既输出了最终的类别预测（0或1），也输出了概率预测。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_131.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_158.png)

最终，测试集数据增加了以下几列：`y_pred_dt`, `y_pred_rf`, `y_pred_svm`是类别预测，`proba_dt`, `proba_rf`, `proba_svm`是预测的概率值。我们取概率值的第二列，即预测为上涨的概率。例如，概率小于0.5则预测为0（下跌），大于0.5则预测为1（上涨）。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_160.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_133.png)

测试集的后四列就是根据概率值得出的预测结果。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_162.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_135.png)

然后进行模型准确性评估。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_164.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_137.png)

评估计算四个指标：准确率、召回率、精确率和F1得分。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_166.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_139.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_167.png)

从结果看，SVM的准确率最高，达到0.55，但F1得分较低，可能是未调参的原因。实际上，随机森林的准确率最好，达到了0.56。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_141.png)

我们展示了随机森林的ROC曲线，AUC值为0.52。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_143.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_169.png)

在当前的学术论文中，股票涨跌预测的AUC值能达到0.54或0.55就已经是非常高的水平了。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_145.png)

超过0.56的准确率是非常夸张的数字。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_171.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_147.png)

## 策略回测与评估 📉

![](img/bf95abcffd9a9c69a4827554fc5d44c8_173.png)

上一节我们评估了模型的预测性能，本节我们进入策略回测阶段，绘制收益率曲线。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_175.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_149.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_177.png)

收益率曲线的绘制基于以下逻辑：我们对预测为上涨的概率值进行排序。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_151.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_179.png)

排序后，我们进行等权重配置。这里有两种投资组合构建策略。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_181.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_153.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_183.png)

第一种策略：买入所有预测值为1（上涨）的股票。
第二种策略：根据上涨概率排序，只买入概率最高的前5只或前10只股票。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_155.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_185.png)

本文选择第一种方法，即买入所有预测上涨的股票，并计算其平均收益率。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_186.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_157.png)

`np.mean()` 用于计算等权重下的平均收益。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_188.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_159.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_190.png)

只要预测上涨就买入，并进行等权重资金分配。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_191.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_161.png)

为了进行回测，我们导入了沪深300指数数据作为基准。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_193.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_163.png)

沪深300指数作为基准线。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_195.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_165.png)

接下来，将我们机器学习构建的投资组合收益率与沪深300基准的收益率进行合并。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_197.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_167.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_199.png)

合并后的数据包含沪深300、决策树(DT)、随机森林(RF)和SVM策略的累计收益率。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_169.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_201.png)

从结果看，最终沪深300的累计收益率为0.90，即下跌了10%。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_203.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_171.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_205.png)

而随机森林和SVM策略的累计收益率分别为1.18和1.006，即上涨了18%和0.6%。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_173.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_207.png)

绘制收益率曲线图。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_209.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_175.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_211.png)

可以发现，随机森林(RF)的收益率最高。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_177.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_213.png)

这也间接印证了随机森林的预测准确率最高。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_179.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_215.png)

其次是SVM策略。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_181.png)

最后是决策树(DT)策略，收益率为0.509。从图中看，随机森林收益率最高，其次是决策树，最后是沪深300基准。这是第一种策略（买入所有看涨股票）的结果。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_183.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_217.png)

我给大家展示一下第二种策略。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_219.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_185.png)

第二种策略是根据上涨概率排序，选择前5只或前10只股票构建组合。我们运行一下选择前5只股票的代码。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_221.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_187.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_223.png)

选择前5只股票后，由于投资标的数量很少，可以发现SVM、RF的走势基本一致，但决策树(DT)策略在后期取得了较好的效果。但这并不能证明DT策略更优，因为结果严重依赖于我们选择的股票数量。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_189.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_225.png)

我们再将其改为选择前10只股票看看。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_227.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_191.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_229.png)

改为10只后，SVM策略的效果变得最好。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_193.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_231.png)

话不多说，我们还是改回第一种策略，即买入所有预测上涨的股票。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_195.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_233.png)

改回第一种策略。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_235.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_197.png)

接下来，我们分析特征的重要性。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_199.png)

分析发现，`return_1day`（过去一天的收益率）的特征重要性最高。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_237.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_201.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_239.png)

其次是`close-open`（当日涨跌幅）、`prev_close`（前收）、`MACD`、`return_30day`、`return_5day`等。这个结果非常符合我们的认知：过去一天的收益率对明天的收益率有直接影响，`close-open`、`MACD`、过去30天、5天的收益率都影响着未来的趋势。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_203.png)

结果符合认知。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_205.png)

这是特征重要性的表现。接下来对策略性能进行全面评估。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_207.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_241.png)

我们计算了多个评估指标：年化收益率、波动率、夏普比率、最大回撤等。首先看年化收益率，沪深300基准和决策树(DT)策略为负。我们发现机器学习构建的投资组合策略均超过了基准策略。随机森林(RF)和SVM策略的年化收益率为正，而基准收益率为负的25%。随机森林和SVM能达到5%和1%的正收益。再看最大回撤，随机森林的最大回撤也小于沪深300的最大回撤。夏普比率也是随机森林和SVM优于基准和DT。随机森林的各项指标均是最优的。

![](img/bf95abcffd9a9c69a4827554fc5d44c8_209.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_243.png)

## 总结 🎯

本节课中我们一起学习了Python机器学习量化选股的全流程。我们从数据获取与预处理开始，构建了包含多个技术指标的特征工程，应用了决策树、随机森林、SVM等模型进行涨跌预测，并进行了详细的模型评估。最后，我们基于预测结果构建了投资策略，在测试集上进行了回测，并从多个维度评估了策略性能，发现随机森林模型在本案例中表现最佳。

代码将提供在视频下方，感兴趣的同学可以自行下载。如果大家有任何疑问，欢迎在视频下方留言讨论。祝大家学习愉快，工作顺利！

![](img/bf95abcffd9a9c69a4827554fc5d44c8_245.png)

![](img/bf95abcffd9a9c69a4827554fc5d44c8_211.png)