# 量化交易机器学习：P1：数据导入与准备 📊

![](img/d98e5d866297b5973a6bb14f4e189120_0.png)

![](img/d98e5d866297b5973a6bb14f4e189120_2.png)

![](img/d98e5d866297b5973a6bb14f4e189120_4.png)

在本节课中，我们将学习如何在 MLRun 和 Qlib 框架的基础上，使用 Qlib 的开源库 `qlib` 来准备和导入量化交易所需的机器学习数据。我们将重点关注数据源的配置、环境搭建以及初步的数据加载流程。

![](img/d98e5d866297b5973a6bb14f4e189120_6.png)

---

![](img/d98e5d866297b5973a6bb14f4e189120_8.png)

![](img/d98e5d866297b5973a6bb14f4e189120_10.png)

## 环境搭建与库安装 🛠️

![](img/d98e5d866297b5973a6bb14f4e189120_12.png)

上一节我们概述了课程目标，本节中我们来看看如何搭建所需的环境并安装必要的库。

![](img/d98e5d866297b5973a6bb14f4e189120_14.png)

首先，需要从 GitHub 获取 `qlib` 的开源代码。该库近期有更新，例如增加了新的测试模块。其安装要求注明支持 Python 3.7 和 3.8，但在 Python 3.9 环境下使用 `pip` 安装可能会失败。由于我们的 MLRun 环境基于 Python 3.9，因此需要采用源码安装方式。

进入 MLRun 环境后，执行源码安装命令。安装过程可能需要一些时间，并可能遇到依赖冲突的警告，例如 `pandas` 或 `mlflow` 的版本问题。遵循官方推荐的安装方式可以避免这些问题。

![](img/d98e5d866297b5973a6bb14f4e189120_16.png)

![](img/d98e5d866297b5973a6bb14f4e189120_18.png)

安装完成后，可以验证 `qlib` 是否成功安装。

---

![](img/d98e5d866297b5973a6bb14f4e189120_20.png)

![](img/d98e5d866297b5973a6bb14f4e189120_22.png)

## 数据准备与下载 📥

![](img/d98e5d866297b5973a6bb14f4e189120_24.png)

环境搭建好后，下一步是准备数据。`qlib` 支持多种数据源，在本教程中，我们将使用其提供的高质量示例数据集。

![](img/d98e5d866297b5973a6bb14f4e189120_26.png)

![](img/d98e5d866297b5973a6bb14f4e189120_28.png)

![](img/d98e5d866297b5973a6bb14f4e189120_30.png)

以下是数据准备的核心步骤：

1.  **配置数据源**：在代码中指定使用 `qlib` 的 `instruments` 数据源，并设置所需的时间范围。
2.  **下载数据**：运行数据下载脚本。数据文件大约 400MB，下载速度取决于网络状况。
3.  **初始化与加载**：数据下载完成后，运行初始化脚本将数据载入 `qlib` 系统。这个过程会将原始数据处理成可供模型使用的格式。

关键代码示例如下，用于下载并初始化数据：
```python
# 设置数据源和时间范围
provider_uri = "~/.qlib/qlib_data/cn_data"
region = "cn"
time_period = ("2008-01-01", "2024-01-01")

# 下载数据
qlib_data = QlibData(provider_uri, region, time_period)
qlib_data.download_data()

# 初始化qlib
qlib.init(provider_uri=provider_uri, region=region)
```

![](img/d98e5d866297b5973a6bb14f4e189120_32.png)

---

![](img/d98e5d866297b5973a6bb14f4e189120_34.png)

![](img/d98e5d866297b5973a6bb14f4e189120_36.png)

![](img/d98e5d866297b5973a6bb14f4e189120_38.png)

## 在 MLRun 工程中集成数据流程 🔄

![](img/d98e5d866297b5973a6bb14f4e189120_40.png)

![](img/d98e5d866297b5973a6bb14f4e189120_42.png)

![](img/d98e5d866297b5973a6bb14f4e189120_44.png)

我们已经完成了基础的数据准备，本节中我们来看看如何将这些步骤集成到 MLRun 的工程项目中，实现可复现和可管理的数据流水线。

在 MLRun 中，我们通过创建函数（Function）和任务（Task）来组织代码。我们需要创建一个专门的数据预处理函数。

![](img/d98e5d866297b5973a6bb14f4e189120_46.png)

![](img/d98e5d866297b5973a6bb14f4e189120_48.png)

1.  **创建 MLRun 工程**：首先，在 MLRun 中创建一个新工程，例如命名为 `quick_qlib`。
2.  **定义数据预处理函数**：仿照 `qlib` 示例，编写一个数据获取和初始化的函数。这个函数将封装数据下载和 `qlib.init` 的调用。
3.  **注册函数**：将该函数注册到 MLRun 工程中，使其成为一个可调用的任务。

![](img/d98e5d866297b5973a6bb14f4e189120_50.png)

以下是集成到 MLRun 的数据预处理函数框架：
```python
import mlrun
from qlib.data import D
from qlib.config import REG_CN

def qlib_cn_data_prep(context: mlrun.MLClientCtx):
    """
    数据预处理函数：下载并初始化qlib中国A股数据
    """
    provider_uri = "~/.qlib/qlib_data/cn_data"
    region = REG_CN

    # 下载数据 (此处应调用具体的下载逻辑)
    # ...

    # 初始化qlib
    qlib.init(provider_uri=provider_uri, region=region)

    # 获取数据句柄，可返回以供后续步骤使用
    dh = D.features(D.instruments("csi300"), ["$close", "$volume"])
    df = dh.fetch()
    
    # 记录数据
    context.log_dataset("qlib_cn_data", df=df)
```
将这个函数保存并注册后，即可在 MLRun 的流水线中作为独立任务运行。

![](img/d98e5d866297b5973a6bb14f4e189120_52.png)

![](img/d98e5d866297b5973a6bb14f4e189120_54.png)

---

## 运行验证与总结 ✅

![](img/d98e5d866297b5973a6bb14f4e189120_56.png)

最后，我们运行创建的数据预处理任务进行验证。在 MLRun 工程界面中执行该任务，如果一切配置正确，任务将成功完成，并输出数据加载的日志信息。

![](img/d98e5d866297b5973a6bb14f4e189120_58.png)

![](img/d98e5d866297b5973a6bb14f4e189120_59.png)

![](img/d98e5d866297b5973a6bb14f4e189120_61.png)

![](img/d98e5d866297b5973a6bb14f4e189120_63.png)

本节课中我们一起学习了：
1.  如何为 MLRun 和 Qlib 搭建 Python 环境并安装 `qlib` 库。
2.  如何下载和初始化 `qlib` 提供的标准金融数据集。
3.  如何将数据准备流程封装成函数，并集成到 MLRun 工程中，为后续的模型训练做好准备。

![](img/d98e5d866297b5973a6bb14f4e189120_65.png)

![](img/d98e5d866297b5973a6bb14f4e189120_67.png)

通过本课的学习，我们建立了量化研究的数据基础。下一步，我们将利用这些准备好的数据，进行特征工程和机器学习模型的训练。