# Qlib与MLRun基础数据处理：P1：数据准备与特征工程 🚀

![](img/16e2f81d8fb5950e25b75a349609e37e_0.png)

在本节课中，我们将学习如何在Qlib和MLRun框架下，为机器学习模型准备数据并进行初步的特征工程。我们将从一个简单的股票价格数据集开始，逐步将其处理成适合模型训练的结构化数据。

---

## 数据回顾与目标设定

![](img/16e2f81d8fb5950e25b75a349609e37e_2.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_4.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_6.png)

上一节我们成功导入了数据并查看了其基本内容。数据包含了股票的开盘价、最高价、最低价、收盘价以及一个因子（factor）。

![](img/16e2f81d8fb5950e25b75a349609e37e_8.png)

然而，在传统的量化交易中，我们的目标变量（label）通常是未来的收益率，而不是像示例中那样一个预设的分类标签（如乳腺癌诊断结果）。因此，我们需要调整数据处理流程，以收盘价为基础构建适合预测的标签。

![](img/16e2f81d8fb5950e25b75a349609e37e_10.png)

## 数据处理流程解析

![](img/16e2f81d8fb5950e25b75a349609e37e_12.png)

为了进行机器学习训练，我们需要将原始数据处理成包含特征（X）和标签（y）的数据集，并划分为训练集、验证集和测试集。

![](img/16e2f81d8fb5950e25b75a349609e37e_14.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_16.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_18.png)

以下是数据处理的核心步骤：

1.  **数据加载**：使用Qlib的`D`类加载原始数据。
2.  **数据处理**：使用`DataHandler`对数据进行清洗、特征工程等操作。
3.  **数据集划分**：定义训练、验证和测试的时间段。
4.  **数据实例化**：通过`fetch`方法获取处理后的具体数据。

![](img/16e2f81d8fb5950e25b75a349609e37e_20.png)

让我们通过代码来具体实现这些步骤。

![](img/16e2f81d8fb5950e25b75a349609e37e_22.png)

## 代码实现：构建Qlib数据集

![](img/16e2f81d8fb5950e25b75a349609e37e_24.png)

首先，我们创建一个新的数据处理函数，命名为`create_qlib_dataset_v2`。

```python
import qlib
from qlib.data import D
from qlib.data.dataset import DatasetH
from qlib.data.dataset.handler import DataHandlerLP

![](img/16e2f81d8fb5950e25b75a349609e37e_26.png)

def create_qlib_dataset_v2():
    """
    创建用于机器学习训练的Qlib数据集（版本2）。
    此版本专注于为股票价格预测构建特征和标签。
    """
    # 1. 定义数据加载器 (Data Loader)
    # 设置数据的时间范围
    start_time = "2010-01-01"
    end_time = "2020-12-31"
    # 使用D.features方法加载数据，这里我们加载收盘价('$close')和一个示例因子('factor')
    data_loader = D.features(["$close", "factor"], start_time=start_time, end_time=end_time)

    # 2. 定义数据处理流程 (Data Handler)
    # 这里我们定义一个简单的处理流程：计算收益率作为标签
    # 标签y = 未来N日的收益率 (例如：明日收盘价 / 今日收盘价 - 1)
    # 特征X = 今日的因子值
    dh_config = {
        "feature": (["factor"], ["Ref($close, -1)/$close - 1"]), # 特征列和标签列的计算公式
        # 可以在此处添加更多预处理步骤，如去极值、标准化等
    }
    data_handler = DataHandlerLP(data_loader, **dh_config)

    # 3. 定义数据集划分时间段
    segments = {
        "train": ("2010-01-01", "2018-12-31"),
        "valid": ("2019-01-01", "2019-12-31"),
        "test": ("2020-01-01", "2020-12-31"),
    }

    # 4. 实例化数据集
    # 使用DataHandler和划分时间段创建DatasetH对象
    dataset = DatasetH(data_handler, segments)

    # 5. 获取数据
    # 调用fetch()方法，获取处理后的、划分好的具体数据
    df = dataset.fetch()
    
    return dataset, df
```

![](img/16e2f81d8fb5950e25b75a349609e37e_28.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_30.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_31.png)

## 关键概念详解

![](img/16e2f81d8fb5950e25b75a349609e37e_33.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_35.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_37.png)

以下是上述代码中涉及的核心概念：

![](img/16e2f81d8fb5950e25b75a349609e37e_39.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_40.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_42.png)

*   **`D.features`**：这是Qlib中用于加载指定字段（特征）数据的方法。`$close`代表收盘价，是一个内置表达式。
*   **`DataHandlerLP`**：这是用于处理学习预测（Learning to Predict）任务的数据处理器。其核心配置`feature`是一个元组，第一个元素是特征列名列表，第二个元素是标签列的计算表达式列表。
*   **标签公式 `Ref($close, -1)/$close - 1`**：
    *   `Ref($close, -1)`：引用下一期的收盘价。
    *   `$close`：当前期的收盘价。
    *   整个公式计算的是**下一期相对于当前期的收益率**，这是一个典型的未来信息，在训练时作为预测目标（y）。
*   **`DatasetH`**：Qlib中数据集的高级封装，它集成了`DataHandler`和数据集划分逻辑，方便地管理训练、验证和测试数据。
*   **`fetch()`**：这是将数据配置真正转化为`pandas DataFrame`的关键方法。只有调用`fetch()`后，数据才会被计算并加载到内存中。

## 执行与验证

![](img/16e2f81d8fb5950e25b75a349609e37e_44.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_46.png)

现在，我们执行这个函数并查看结果。

![](img/16e2f81d8fb5950e25b75a349609e37e_48.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_50.png)

```python
# 执行数据创建函数
dataset_instance, processed_data_df = create_qlib_dataset_v2()

![](img/16e2f81d8fb5950e25b75a349609e37e_52.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_53.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_55.png)

# 打印数据集信息
print("数据集类型：", type(dataset_instance))
print("\n处理后的数据DataFrame预览：")
print(processed_data_df.head())
print("\n数据列名：", processed_data_df.columns.tolist())
```

执行上述代码后，你应该能看到一个包含特征（如`factor`）和标签（如`Ref($close,-1)/$close-1`）的`DataFrame`，并且数据已经根据`segments`字典中的定义打上了训练集（train）、验证集（valid）或测试集（test）的标记。

![](img/16e2f81d8fb5950e25b75a349609e37e_57.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_59.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_60.png)

---

![](img/16e2f81d8fb5950e25b75a349609e37e_62.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_63.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_65.png)

![](img/16e2f81d8fb5950e25b75a349609e37e_67.png)

## 总结

![](img/16e2f81d8fb5950e25b75a349609e37e_69.png)

本节课中我们一起学习了如何为量化机器学习模型准备数据。

![](img/16e2f81d8fb5950e25b75a349609e37e_71.png)

1.  我们明确了在股价预测中，**标签应是未来的收益率**，而非静态分类。
2.  我们解析了Qlib标准的数据处理流程：**加载 -> 处理 -> 划分 -> 实例化**。
3.  我们实现了`create_qlib_dataset_v2`函数，其中：
    *   使用`D.features`加载原始数据。
    *   使用`DataHandlerLP`配置特征和标签的计算公式。
    *   使用`segments`字典划分数据集。
    *   使用`DatasetH`整合处理器和划分，并通过`fetch()`方法获取最终数据。

![](img/16e2f81d8fb5950e25b75a349609e37e_73.png)

现在，我们已经拥有了一个结构清晰、包含特征和标签、并已完成划分的数据集。在下一节课中，我们将以此为基础，使用MLRun来管理和执行机器学习模型的训练任务。