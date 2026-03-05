# Python金融分析与量化交易实战：P41：41.3-训练时间序列数据预测结果 📈

![](img/7a42618780ae1b04c1f9bc3afb706fe1_0.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_2.png)

在本节课中，我们将学习如何准备时间序列数据并训练一个LSTM模型来进行预测。我们将从数据标准化开始，逐步构建数据集，定义模型，并进行训练与评估。

## 数据标准化与预处理

上一节我们介绍了如何构建时间序列数据。本节中，我们来看看如何对数据进行标准化处理，这是训练模型前的重要步骤。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_4.png)

标准化操作的目的是让数据以零为中心，并且各维度的取值范围尽可能一致。其核心公式如下：

![](img/7a42618780ae1b04c1f9bc3afb706fe1_6.png)

**标准化公式：**
`X_normalized = (X - mean) / std`

![](img/7a42618780ae1b04c1f9bc3afb706fe1_8.png)

其中，`mean`是训练数据的均值，`std`是训练数据的标准差。这个操作有两个作用：
1.  **减去均值**：让数据以原点为中心对称分布。
2.  **除以标准差**：将各维度的数据缩放至一个相近的数值范围（例如-1到1之间）。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_10.png)

**关键注意事项：**
计算均值和标准差时，**只能使用训练集的数据**。验证集和测试集的数据不能参与此计算，以确保模型训练过程不会“看到”未来的信息，防止数据泄露。

以下是数据准备的核心代码逻辑：
```python
# 仅使用训练集计算均值和标准差
train_mean = train_data.mean()
train_std = train_data.std()

![](img/7a42618780ae1b04c1f9bc3afb706fe1_12.png)

# 对训练集、验证集、测试集都使用训练集的统计量进行标准化
train_data_normalized = (train_data - train_mean) / train_std
val_data_normalized = (val_data - train_mean) / train_std
test_data_normalized = (test_data - train_mean) / train_std
```

![](img/7a42618780ae1b04c1f9bc3afb706fe1_14.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_16.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_18.png)

## 构建时间序列数据集

![](img/7a42618780ae1b04c1f9bc3afb706fe1_20.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_22.png)

数据标准化后，下一步是将其转换为模型可用的序列样本。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_24.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_26.png)

我们使用一个滑动窗口来创建样本。例如，指定窗口大小`window_size=20`，意味着每个样本由连续的20个时间步数据组成，其标签（`target`）是这20个点之后的下一个值（当`target=1`时）。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_28.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_30.png)

执行此操作后，原始数据集的末尾会减少`window_size`个数据点，因为无法再构建一个完整的序列。例如，30万个数据点，窗口大小为20，最终得到的序列数量为`300,000 - 20 = 299,980`。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_32.png)

转换后的数据维度为：`(样本数量, 窗口大小, 特征数量)`。在本例的简单实验中，我们只使用单特征，因此特征数量为1。对应的标签`Y`的维度为`(样本数量, 1)`。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_34.png)

## 构建TensorFlow数据集

![](img/7a42618780ae1b04c1f9bc3afb706fe1_36.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_38.png)

数据转换完成后，我们需要将其封装成TensorFlow的`Dataset`对象，以便高效地进行批处理和训练。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_40.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_42.png)

以下是构建数据集的步骤：
1.  指定`batch_size`（批大小）。
2.  使用`tf.data.Dataset.from_tensor_slices`将`X`和`Y`数据转换为数据集。
3.  对训练集进行`shuffle`（打乱）操作，以增加训练的随机性。
4.  应用`batch`方法生成批数据。
5.  验证集的构建方法类似，但通常不需要进行打乱操作。

相关代码如下：
```python
batch_size = 256

![](img/7a42618780ae1b04c1f9bc3afb706fe1_44.png)

# 构建训练数据集
train_dataset = tf.data.Dataset.from_tensor_slices((X_train, y_train))
train_dataset = train_dataset.shuffle(buffer_size=10000).batch(batch_size)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_46.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_48.png)

# 构建验证数据集
val_dataset = tf.data.Dataset.from_tensor_slices((X_val, y_val))
val_dataset = val_dataset.batch(batch_size)
```

![](img/7a42618780ae1b04c1f9bc3afb706fe1_50.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_52.png)

## 定义LSTM模型

准备好数据后，我们来定义一个简单的LSTM预测模型。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_54.png)

模型结构非常简单：
1.  **LSTM层**：这是核心层，用于捕捉时间序列中的长期依赖关系。我们可以指定隐藏单元的数量（如64、128等）。在本例中，我们设置其仅返回最后一个时间步的输出。
2.  **全连接层**：由于我们只需要预测下一个值（一个标量），因此在LSTM层后添加一个只有1个神经元的全连接层。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_56.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_58.png)

以下是模型定义的示例代码：
```python
model = tf.keras.Sequential([
    tf.keras.layers.LSTM(units=64, input_shape=(window_size, num_features)),
    tf.keras.layers.Dense(1)
])
```

![](img/7a42618780ae1b04c1f9bc3afb706fe1_60.png)

## 模型训练与结果分析

![](img/7a42618780ae1b04c1f9bc3afb706fe1_62.png)

模型定义好后，我们指定损失函数（如均方误差`MSE`）和优化器，然后开始训练。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_64.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_66.png)

在训练过程中，我们观察训练损失和验证损失的变化，以判断模型是否收敛。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_68.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_70.png)

训练完成后，我们对结果进行可视化分析。通常会绘制以下内容进行对比：
*   **真实值**：时间序列真实的后续值。
*   **模型预测值**：LSTM模型预测的结果。
*   **基线预测值**：一个简单的统计基线，例如使用过去20个值的**平均值**作为预测值。这有助于评估模型是否比简单方法有提升。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_72.png)

从初步结果来看，仅使用单一特征的LSTM模型预测结果与真实值存在一定差异，但相比简单的平均值基线可能有所改善。这为进一步优化（例如使用更多特征、调整模型结构）提供了方向。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_74.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_76.png)

## 总结

![](img/7a42618780ae1b04c1f9bc3afb706fe1_78.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_80.png)

本节课中我们一起学习了时间序列预测的完整流程。
1.  我们首先对数据进行了标准化预处理，并强调了仅使用训练集统计信息的重要性。
2.  接着，我们将数据构建成`(序列样本, 标签)`的形式。
3.  然后，我们使用TensorFlow的`Dataset` API高效地组织了训练和验证数据。
4.  之后，我们定义了一个由LSTM层和全连接层组成的简单预测模型。
5.  最后，我们训练了模型，并通过与真实值及基线方法的对比，初步评估了预测效果。

![](img/7a42618780ae1b04c1f9bc3afb706fe1_82.png)

![](img/7a42618780ae1b04c1f9bc3afb706fe1_84.png)

这个过程为使用深度学习进行金融时间序列预测奠定了实践基础。