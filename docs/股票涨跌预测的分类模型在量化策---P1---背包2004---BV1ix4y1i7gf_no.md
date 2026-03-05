# 量化金融与机器学习2024：P1：将股票涨跌预测模型应用于量化策略 📈

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_0.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_2.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_4.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_6.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_8.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_10.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_12.png)

在本节课中，我们将学习如何将上一节训练好的股票涨跌预测分类模型，整合到一个完整的量化交易策略中。我们将探讨模型的应用逻辑、数据处理的注意事项，并最终在回测框架中实现一个基于模型预测的交易策略。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_14.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_16.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_18.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_20.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_22.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_24.png)

## 模型应用的核心逻辑 💡

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_26.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_28.png)

上一节我们介绍了如何训练一个预测股票30天后涨跌的分类模型。本节中，我们来看看如何将这个模型运用到实际的投资决策中。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_30.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_32.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_34.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_35.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_37.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_39.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_41.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_42.png)

核心思想非常直接：在任何一个交易日，我们将市场上所有股票当日的特征数据（如市盈率、总市值）输入到训练好的模型中。模型会预测每只股票在未来30天上涨的概率。我们的策略就是买入那些被预测为“上涨”的股票。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_43.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_45.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_46.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_48.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_50.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_52.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_54.png)

然而，模型预测为“上涨”的股票可能有很多只。全部买入在操作上可能不现实，且回测框架处理大量股票时效率较低。因此，我们需要一个更精细的选股方法。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_56.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_58.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_60.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_62.png)

以下是解决这个问题的思路：
*   模型不仅能给出分类结果（涨/跌），还能输出股票属于“上涨”类别的**概率**。
*   我们不再买入所有被预测为上涨的股票，而是只买入其中上涨**概率最高**的前几只股票。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_64.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_66.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_68.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_70.png)

具体而言，我们使用模型的 `predict_proba()` 方法。对于一个二分类模型，它会返回一个二维数组，其中第二列（索引为1）通常代表样本属于“上涨”类别的概率。我们获取这个概率值，并对其进行排序。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_72.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_74.png)

```python
# 假设 model 是训练好的分类模型，X_current 是当前日的特征数据
probabilities = model.predict_proba(X_current)[:, 1]  # 获取属于类别1（上涨）的概率
```

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_76.png)

## 关键的数据处理步骤 🔧

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_78.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_80.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_82.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_84.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_86.png)

在将模型应用于新数据之前，有几个关键的数据处理步骤必须注意，以确保模型的预测是有效和一致的。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_88.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_90.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_91.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_93.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_95.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_97.png)

### 全量数据标准化

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_99.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_101.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_102.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_104.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_105.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_107.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_109.png)

上一节中，我们是在划分训练集和测试集之后，分别对它们进行标准化。这在单纯评估模型性能时是可行的。但现在，我们要用训练好的模型去预测全新的、未来的数据。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_111.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_113.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_115.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_117.png)

问题在于：标准化操作（如 `StandardScaler`）依赖于数据的均值和标准差。如果只用训练集的数据进行标准化，那么当模型遇到未来数据时，这些未来数据与训练数据处于不同的“尺度”下，导致预测失真。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_119.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_121.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_123.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_125.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_127.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_129.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_131.png)

因此，正确的做法是：**先对所有历史数据（包括未来用于回测的数据）进行统一的标准化**，然后再划分训练集和测试集。这样能保证所有数据都转换到同一个标准尺度上。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_133.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_135.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_137.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_138.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_140.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_142.png)

```python
from sklearn.preprocessing import StandardScaler
scaler = StandardScaler()
# 假设 df_features 包含所有年份的市盈率(pe_ratio)和总市值(market_cap)数据
df_features[['pe_ratio', 'market_cap']] = scaler.fit_transform(df_features[['pe_ratio', 'market_cap']])
```

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_144.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_146.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_148.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_150.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_152.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_154.png)

### 训练与回测时间划分

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_156.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_158.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_160.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_162.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_164.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_166.png)

为了模拟真实情况并评估模型效果，我们需要用历史数据训练模型，并用它来预测“未来”。一个简单的划分方法是：
*   **训练期**：使用前4年的数据训练模型。
*   **回测期**：使用最后1年的数据来测试策略效果。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_168.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_169.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_171.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_173.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_175.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_177.png)

虽然在实际操作中，模型应该定期（例如每月）用最新数据重新训练（滚动更新），但为了简化流程，我们本次采用固定的训练-测试划分。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_179.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_181.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_183.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_185.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_187.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_189.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_191.png)

```python
# 假设数据包含从2018年到2023年的数据
train_data = df_features[df_features['date'] < '2022-01-01']  # 2022年之前的数据用于训练
backtest_data = df_features[df_features['date'] >= '2022-01-01'] # 2022年及之后的数据用于回测
```

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_193.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_194.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_196.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_198.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_200.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_202.png)

## 在回测框架中集成模型 🚀

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_204.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_206.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_208.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_210.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_212.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_214.png)

现在，我们将把上述逻辑集成到一个量化回测框架中。我们将基于一个多因子选股策略模板进行修改。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_216.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_217.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_219.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_221.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_223.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_225.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_227.png)

以下是集成模型预测步骤的核心代码片段：

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_229.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_231.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_233.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_235.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_237.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_239.png)

```python
# 在回测的每日循环中
def before_trading_start(context):
    # 1. 获取当前交易日所有股票的特征数据（已标准化）
    current_data = get_current_features(context)

    # 2. 准备特征矩阵X，需确保无缺失值
    X_current = current_data[['pe_ratio', 'market_cap']].dropna().values

    # 3. 使用预训练好的模型进行预测，获取上涨概率
    prob_up = model.predict_proba(X_current)[:, 1]

    # 4. 将概率值添加回数据框，便于排序
    current_data['prob_up'] = prob_up

    # 5. 按上涨概率从高到低排序，选择概率最高的前N只股票
    # 例如，选择概率最高的前20只股票
    top_stocks = current_data.nlargest(20, 'prob_up')

    # 6. 将选出的股票代码列表设置为当日要交易的股票
    context.stock_list = top_stocks['stock_code'].tolist()
```

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_241.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_243.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_245.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_247.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_249.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_251.png)

接下来，回测框架的剩余部分（如下单、仓位管理）将使用 `context.stock_list` 中的股票来执行交易。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_253.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_255.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_256.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_257.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_259.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_261.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_263.png)

## 策略回测与结果分析 📊

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_265.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_267.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_269.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_271.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_273.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_275.png)

按照上述流程，我们使用前4年数据训练的模型，对最后1年的数据进行了回测。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_277.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_279.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_281.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_283.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_285.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_287.png)

运行回测后，我们得到了策略的净值曲线和绩效报告。需要说明的是，本次演示中策略的最终表现可能并不理想（甚至出现亏损）。这说明了几个重要问题：
1.  **模型性能**：我们使用的简单特征（市盈率、市值）和模型可能不足以准确预测市场。
2.  **过时风险**：使用固定历史数据训练的模型，随着时间的推移，其预测能力会下降，强调了滚动更新模型的重要性。
3.  **流程验证**：本节课的重点在于**掌握将机器学习模型嵌入量化策略的完整流程**，而非构建一个必胜的策略。这个流程包括数据处理、模型应用、回测集成等关键步骤。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_289.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_291.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_293.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_295.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_297.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_299.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_301.png)

## 总结与回顾 🎯

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_303.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_304.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_306.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_308.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_310.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_311.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_313.png)

本节课中我们一起学习了如何将一个股票涨跌预测的机器学习模型，从理论预测工具转变为可执行的量化交易策略。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_315.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_316.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_318.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_320.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_322.png)

我们首先明确了模型应用的核心逻辑：利用模型预测的上涨概率进行选股。接着，我们重点讲解了**全量数据标准化**和**训练/回测时间划分**这两个至关重要的数据处理步骤，这是模型能否正确应用于新数据的关键。然后，我们一步步演示了如何在回测框架的每日循环中，调用模型、处理数据、生成交易信号。最后，我们运行了回测并分析了结果，理解了模型在实际应用中可能面临的挑战。

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_324.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_326.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_328.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_330.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_332.png)

![](img/a2de46b8ab4cd5e6a4a779f31fa3f777_334.png)

通过本课，你已经掌握了将机器学习模型与量化交易策略结合的基本方法论。要获得更好的策略效果，未来的方向包括：寻找更多有效的特征、尝试更复杂的模型、实现模型的定期滚动训练以及加入更严格的风险控制模块。