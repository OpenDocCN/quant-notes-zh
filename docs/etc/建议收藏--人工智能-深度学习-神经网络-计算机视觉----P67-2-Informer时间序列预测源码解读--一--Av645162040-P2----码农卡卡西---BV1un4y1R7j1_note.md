# Python机器学习与量化交易：P67：2. Informer时间序列预测源码解读（一）

在本节课中，我们将学习如何解读Informer时间序列预测模型的源码。我们将从如何查找和评估开源项目开始，逐步深入到代码的具体执行流程，重点关注数据的读取与处理部分。通过本教程，你将掌握一套分析和理解开源机器学习项目代码的通用方法。

## 概述：如何开始一个开源项目

上一节我们介绍了Informer模型的基本原理，本节中我们来看看如何在实际项目中应用它。面对一个新的机器学习任务，第一步往往是寻找合适的开源实现。一个高效的检索和评估流程至关重要。

以下是开始使用一个开源项目的标准步骤：

1.  **在GitHub上搜索项目**：使用项目名（如“informer”）或相关论文名进行搜索。
2.  **评估项目质量**：优先选择Star数量多、维护活跃的项目，这通常意味着代码质量更高、社区支持更好。
3.  **仔细阅读README文档**：这是项目的说明书，包含了环境要求、数据准备、训练和测试命令等关键信息。
4.  **查看Issues板块**：在下载代码前，浏览Issues可以帮助你预知可能遇到的常见问题，判断项目是否易于使用。
5.  **下载源码**：对于初学者，最简单的方式是直接点击`Code`按钮，选择`Download ZIP`，解压后用PyCharm等IDE打开。

![](img/2c9c9931db0c57b81c99b7c2223514b2_1.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_3.png)

## 源码入口与参数解析

![](img/2c9c9931db0c57b81c99b7c2223514b2_5.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_7.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_9.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_11.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_13.png)

我们打开下载的源码，找到入口文件`main_informer.py`。理解程序如何开始运行是读懂源码的第一步。

![](img/2c9c9931db0c57b81c99b7c2223514b2_15.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_17.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_19.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_21.png)

```python
# main_informer.py 中解析命令行参数的部分
import argparse
parser = argparse.ArgumentParser(description='Informer')
parser.add_argument('--model', type=str, default='informer', help='模型名称')
parser.add_argument('--data', type=str, default='ETTh1', help='数据类型')
parser.add_argument('--root_path', type=str, default='./data/ETT/', help='数据根路径')
parser.add_argument('--data_path', type=str, default='ETTh1.csv', help='数据文件')
# ... 此处省略大量其他参数定义
args = parser.parse_args()
```

程序首先通过`argparse`模块解析用户从命令行传入的所有参数。这些参数控制了模型的方方面面，例如：
*   `model`：选择使用的模型架构（如 `informer`）。
*   `data`：选择要使用的数据集。
*   `seq_len`：输入序列的历史长度，例如 `96` 表示使用过去96个时间点的数据。
*   `label_len`：解码器输入中已知标签的长度，例如 `48`。
*   `pred_len`：需要预测的未来序列长度，例如 `24`。
*   `batch_size`：训练时的批大小。
*   `learning_rate`：学习率。

![](img/2c9c9931db0c57b81c99b7c2223514b2_23.png)

在IDE中运行或调试时，我们通常在参数解析器中直接设置这些参数的默认值，而不是通过命令行输入。

## 数据加载与预处理初探

![](img/2c9c9931db0c57b81c99b7c2223514b2_25.png)

解析完参数后，程序进入训练流程。核心的一步是加载和准备数据。我们通过`Exp_Informer`类的`_get_data`方法来探究这个过程。

![](img/2c9c9931db0c57b81c99b7c2223514b2_27.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_29.png)

```python
# 在 _get_data 方法中初始化数据集
from data.data_loader import Dataset_Custom
train_data = Dataset_Custom(
    root_path=args.root_path,
    data_path=args.data_path,
    flag='train',
    size=[args.seq_len, args.label_len, args.pred_len],
    features=args.features,
    target=args.target,
    timeenc=args.timeenc,
    freq=args.freq
)
```

这里初始化了一个`Dataset_Custom`对象，它是PyTorch `Dataset`类的子类，负责按需加载和格式化我们的时间序列数据。关键参数包括：
*   `flag`：标识是训练集(`train`)、验证集(`val`)还是测试集(`test`)。
*   `size`：一个列表，定义了 `[seq_len, label_len, pred_len]`。
*   `features`：处理特征的模式，`‘M’` 表示多变量预测。
*   `target`：指定数据中哪一列是预测目标。

## 深入数据集类：`Dataset_Custom`

要理解数据如何被加工，我们需要进入`Dataset_Custom`类。在`__getitem__`方法中，我们可以看到模型一次输入输出的具体结构。

```python
# Dataset_Custom 中 __getitem__ 方法的核心逻辑（概念性代码）
def __getitem__(self, index):
    # 计算序列的起始点
    s_begin = index
    s_end = s_begin + self.seq_len
    r_begin = s_end - self.label_len
    r_end = r_begin + self.label_len + self.pred_len

    # 提取输入序列 (seq_len)
    seq_x = self.data[s_begin:s_end]
    # 提取解码器输入和预测目标 (label_len + pred_len)
    seq_y = self.data[r_begin:r_end]

    # 提取对应的时间戳
    seq_x_mark = self.stamp[s_begin:s_end]
    seq_y_mark = self.stamp[r_begin:r_end]

    return seq_x, seq_y, seq_x_mark, seq_y_mark
```

![](img/2c9c9931db0c57b81c99b7c2223514b2_31.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_33.png)

这段代码揭示了Informer数据组织的关键：
1.  `seq_x`：编码器的输入，长度为 `seq_len`（如96），是过去的历史数据。
2.  `seq_y`：包含两部分。前 `label_len`（如48）个点是解码器的已知输入（有标签），后 `pred_len`（如24）个点是模型需要学习预测的未来目标。
3.  `seq_x_mark` 和 `seq_y_mark`：分别是 `seq_x` 和 `seq_y` 对应的时间特征（如星期几、第几小时等），经过嵌入(`embedding`)后提供给模型，以利用时间信息。

![](img/2c9c9931db0c57b81c99b7c2223514b2_35.png)

![](img/2c9c9931db0c57b81c99b7c2223514b2_37.png)

这种 `[seq_x, seq_y]` 的构造方式，正是Informer论文中“ProbSparse Attention”和“Decoder Self-Attention Distilling”等机制得以实现的基础。

## 数据标准化与划分

在数据集初始化时，还会进行数据标准化和划分。

```python
# 在 Dataset_Custom.__init__ 中
self.scaler = StandardScaler()
# 仅使用训练集数据拟合标准化器
train_data = df_data[border1s[0]:border2s[0]]
self.scaler.fit(train_data.values)
# 对所有数据（训练、验证、测试）进行标准化转换
self.data = self.scaler.transform(df_data.values)
```

这里使用`StandardScaler`进行标准化，即对每个特征减去均值并除以标准差。**重要**的是，标准化器的参数（均值和标准差）只从训练集数据中计算得到，然后将其应用于验证集和测试集。这避免了数据泄露，确保模型评估的公正性。

数据划分通常按比例进行，例如70%训练，20%测试，10%验证。边界值(`border1s`, `border2s`)用来记录每个数据集在原始数据数组中的起止索引。

![](img/2c9c9931db0c57b81c99b7c2223514b2_39.png)

## 总结

![](img/2c9c9931db0c57b81c99b7c2223514b2_41.png)

本节课中我们一起学习了Informer时间序列预测项目源码的初步解读流程。我们从如何寻找和评估GitHub上的开源项目开始，逐步深入到代码的入口、参数解析，并重点剖析了数据加载模块`Dataset_Custom`。我们了解到：

1.  **项目启动**：善于利用README和Issues是高效使用开源代码的关键。
2.  **参数控制**：模型行为通过大量命令行参数灵活配置。
3.  **数据核心**：时间序列数据被组织成`(seq_x, seq_y)`对，其中`seq_y`前一部分作为解码器先验输入，后一部分是预测目标。
4.  **预处理**：数据标准化需严格遵守仅在训练集上拟合规则，再应用到所有数据集的原则。

![](img/2c9c9931db0c57b81c99b7c2223514b2_43.png)

在下一节中，我们将继续探索源码，深入模型`（Model）`的构建细节，理解Informer独特的注意力机制是如何在代码中实现的。