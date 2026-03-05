# 时间序列预测：P1：协变量 📊

在本节课中，我们将要学习如何在时间序列预测中引入和使用协变量。协变量是除目标时间序列外，用于辅助预测的额外特征。我们将了解协变量的两种类型，并通过一个股价预测的实例，学习如何在Darts库中创建和使用协变量。

---

## 协变量与多变量的区别

上一节我们介绍了多变量时间序列预测。本节中我们来看看协变量。

![](img/703fb7c8cc3128114cf3589d654b3c06_1.png)

协变量和多变量都有一个共同点，即它们都涉及多个时间序列。区别在于，在多变量预测中，模型需要同时预测所有变量。而在协变量预测中，模型只预测目标时间序列，其他变量仅作为辅助特征引入，模型不负责预测这些协变量。

![](img/703fb7c8cc3128114cf3589d654b3c06_3.png)

在机器学习股价预测中，我们不仅使用股价数据，还会引入如市盈率、市值等特征。在时间序列预测中，这些额外的特征通常通过协变量来引入。将一些不重要的、无需预测的特征作为协变量，可能效果更好。

---

## 协变量的两种类型

协变量主要分为两种：过去协变量和未来协变量。

![](img/703fb7c8cc3128114cf3589d654b3c06_5.png)

*   **过去协变量**：在预测未来目标值时，协变量本身在未来的值是未知的。例如，在预测未来股价时，未来的市盈率数据是未知的，因此市盈率只能作为过去协变量。
*   **未来协变量**：在预测未来目标值时，协变量本身在未来的值是已知的。最典型的例子是时间（如月份、年份），因为未来的日期是确定的。

下图清晰地展示了这两种协变量的区别：

![](img/703fb7c8cc3128114cf3589d654b3c06_3.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_7.png)

---

## 创建时间协变量实例

![](img/703fb7c8cc3128114cf3589d654b3c06_9.png)

以下是如何从日期索引中创建两个简单协变量的例子。

首先，从训练集的时间索引中提取月份，并将其值除以12进行归一化，投影到0到1之间。

```python
# 假设 `series` 是目标时间序列
month_series = series.time_index.month / 12.0
```

接着，从时间索引中提取年份，并减去一个基准年份（例如2020），以得到一个较小的数值序列。

```python
year_series = series.time_index.year - 2020
```

![](img/703fb7c8cc3128114cf3589d654b3c06_11.png)

然后，使用Darts的`concatenate`函数将这两个序列合并为一个多组分的协变量时间序列。

![](img/703fb7c8cc3128114cf3589d654b3c06_13.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_15.png)

```python
from darts import concatenate
covariates = concatenate([month_series, year_series], axis=1)
covariates = covariates.with_columns_renamed(['0', '1'], ['month', 'year'])
```

![](img/703fb7c8cc3128114cf3589d654b3c06_17.png)

最后，我们可以将这个协变量序列可视化出来。

![](img/703fb7c8cc3128114cf3589d654b3c06_19.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_21.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_9.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_23.png)

图中显示了两条线：一条是归一化的月份值，在每年内周期波动；另一条是处理后的年份值，逐年递增。这种基于时间的特征在机器学习特征工程中非常有用，能有效提升预测精度。

![](img/703fb7c8cc3128114cf3589d654b3c06_25.png)

---

![](img/703fb7c8cc3128114cf3589d654b3c06_27.png)

## 在模型中使用协变量

![](img/703fb7c8cc3128114cf3589d654b3c06_29.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_31.png)

创建好协变量后，就可以在训练模型时使用它。以下是在Darts的`NBEATSModel`中使用过去协变量的示例代码。

![](img/703fb7c8cc3128114cf3589d654b3c06_33.png)

```python
from darts.models import NBEATSModel

![](img/703fb7c8cc3128114cf3589d654b3c06_35.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_37.png)

model = NBEATSModel(
    input_chunk_length=30,
    output_chunk_length=12,
    n_epochs=50
)
model.fit(
    series=train_series,  # 训练集目标序列
    past_covariates=covariates,  # 传入过去协变量
    verbose=True
)
```

![](img/703fb7c8cc3128114cf3589d654b3c06_39.png)

训练完成后，进行预测时也需要提供对应的协变量数据。注意，预测步长不能超过协变量序列的未来长度。

![](img/703fb7c8cc3128114cf3589d654b3c06_41.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_42.png)

```python
# 预测时也需要提供协变量
prediction = model.predict(
    n=12,
    series=train_series,
    past_covariates=covariates
)
```

![](img/703fb7c8cc3128114cf3589d654b3c06_44.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_46.png)

加入时间协变量后，模型的预测结果可能会有改善。下图对比了使用协变量前后的预测效果，可以看到预测曲线更贴近真实值。

![](img/703fb7c8cc3128114cf3589d654b3c06_48.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_57.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_49.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_51.png)

---

![](img/703fb7c8cc3128114cf3589d654b3c06_53.png)

## 组合多种协变量

![](img/703fb7c8cc3128114cf3589d654b3c06_55.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_57.png)

除了时间特征，我们还可以引入其他类型的协变量，例如股票的交易量。以下是组合多种协变量的方法。

![](img/703fb7c8cc3128114cf3589d654b3c06_59.png)

首先，确保你拥有其他协变量序列（例如`volume_series`）。然后，使用`concatenate`函数将所有协变量合并。

![](img/703fb7c8cc3128114cf3589d654b3c06_61.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_63.png)

```python
# 假设 volume_series 是交易量序列
all_covariates = concatenate([month_series, year_series, volume_series], axis=1)
```

![](img/703fb7c8cc3128114cf3589d654b3c06_65.png)

接着，在模型训练和预测时，将这个组合后的协变量序列传入即可。

![](img/703fb7c8cc3128114cf3589d654b3c06_67.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_69.png)

```python
model.fit(series=train_series, past_covariates=all_covariates)
prediction = model.predict(n=12, series=train_series, past_covariates=all_covariates)
```

![](img/703fb7c8cc3128114cf3589d654b3c06_71.png)

---

![](img/703fb7c8cc3128114cf3589d654b3c06_73.png)

## 使用内置工具生成时间协变量

Darts库提供了便捷的内置工具`datetime_attribute_timeseries`，可以自动从时间索引生成多种时间协变量，无需手动计算。

![](img/703fb7c8cc3128114cf3589d654b3c06_75.png)

以下是可生成的部分时间特征类型：
*   `month`: 月份
*   `hour`: 小时
*   `dayofweek`: 星期几
*   `year`: 年份

![](img/703fb7c8cc3128114cf3589d654b3c06_77.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_79.png)

你可以通过一个字典来定义需要生成哪些特征，并指定它们是过去协变量还是未来协变量。

![](img/703fb7c8cc3128114cf3589d654b3c06_81.png)

```python
from darts.utils.timeseries_generation import datetime_attribute_timeseries as dt_attr

![](img/703fb7c8cc3128114cf3589d654b3c06_83.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_84.png)

# 定义一个字典来生成时间协变量
covariates_dict = {
    'past': ['month', 'year'],  # 作为过去协变量
    'future': ['dayofweek']     # 作为未来协变量
}
```

![](img/703fb7c8cc3128114cf3589d654b3c06_86.png)

然后，在初始化模型时，通过`add_encoders`参数传入这个字典。

![](img/703fb7c8cc3128114cf3589d654b3c06_88.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_90.png)

```python
model = NBEATSModel(
    input_chunk_length=30,
    output_chunk_length=12,
    add_encoders=covariates_dict,  # 自动生成并添加时间协变量
    n_epochs=50
)
```

![](img/703fb7c8cc3128114cf3589d654b3c06_92.png)

这样，Darts会在模型内部自动处理时间协变量的生成和匹配，简化了代码流程。

---

![](img/703fb7c8cc3128114cf3589d654b3c06_94.png)

![](img/703fb7c8cc3128114cf3589d654b3c06_96.png)

## 总结

![](img/703fb7c8cc3128114cf3589d654b3c06_98.png)

本节课中我们一起学习了时间序列预测中的协变量。

![](img/703fb7c8cc3128114cf3589d654b3c06_100.png)

我们首先区分了协变量与多变量预测的核心不同：协变量是辅助特征，模型不对其进行预测。然后，我们了解了**过去协变量**和**未来协变量**两种类型。通过一个股价预测的实例，我们实践了如何从日期中创建时间协变量，并将其应用到Darts的模型中进行训练和预测。最后，我们介绍了组合多种协变量的方法，以及利用Darts内置工具自动生成复杂时间协变量的便捷技巧。

![](img/703fb7c8cc3128114cf3589d654b3c06_102.png)

协变量是提升时间序列模型预测能力的重要手段，合理利用时间和业务相关的协变量，可以有效捕捉数据中的周期性、趋势性等复杂模式。