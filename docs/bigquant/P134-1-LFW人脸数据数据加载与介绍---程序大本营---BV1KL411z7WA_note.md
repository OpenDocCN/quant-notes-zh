# 机器学习实战：P134：LFW人脸数据加载与介绍

在本节课中，我们将学习如何加载和探索LFW（Labeled Faces in the Wild）人脸数据集。我们将使用Python的scikit-learn库来获取数据，并了解数据的基本结构，为后续的人脸识别任务做准备。

## 数据准备与加载

![](img/868817e6c632361c8c08d03017c3124f_1.png)

上一节我们介绍了支持向量机的基本概念，本节中我们来看看如何准备用于人脸识别的数据。

首先，我们需要导入必要的Python库。以下是所需的包及其作用：

```python
import numpy as np
from sklearn import svm
from sklearn.svm import SVC
import matplotlib.pyplot as plt
from sklearn.model_selection import train_test_split
from sklearn.decomposition import PCA
from sklearn.metrics import accuracy_score
from sklearn.linear_model import LogisticRegression
from sklearn import datasets
```

接下来，我们加载LFW人脸数据集。scikit-learn库提供了`fetch_lfw_people`函数来获取这个数据集。第一次加载时，程序会尝试从网络下载数据。如果网络条件不佳，可以手动从提供的网盘链接下载数据，并放置到本地指定目录。

```python
# 加载LFW人脸数据集
data = datasets.fetch_lfw_people(min_faces_per_person=70, resize=1)
```

`fetch_lfw_people`函数有几个重要参数：
*   `min_faces_per_person`：设置每个人最少包含的图片数量，用于筛选数据。
*   `resize`：调整图片大小的比例因子。设置为1表示不缩放，保持原尺寸。
*   `data_home`：指定数据集的本地存储路径，默认为`~/scikit_learn_data/`。

## 数据结构解析

加载完成后，`data`变量是一个类似字典的对象，包含了数据集的所有信息。我们来提取并查看关键部分。

![](img/868817e6c632361c8c08d03017c3124f_3.png)

```python
# 提取数据、标签和原始图片
X = data['data']        # 特征数据（二维数组，已展平）
y = data['target']      # 目标标签（每个人对应的编号）
faces = data['images']  # 原始图片数据（三维数组）

![](img/868817e6c632361c8c08d03017c3124f_5.png)

![](img/868817e6c632361c8c08d03017c3124f_7.png)

# 查看数据形状
print(f"X shape: {X.shape}")
print(f"y shape: {y.shape}")
print(f"faces shape: {faces.shape}")
```

![](img/868817e6c632361c8c08d03017c3124f_9.png)

![](img/868817e6c632361c8c08d03017c3124f_11.png)

![](img/868817e6c632361c8c08d03017c3124f_13.png)

执行上述代码，你会看到类似以下输出：
*   `X shape: (1288, 11750)`：表示有1288个样本，每个样本有11750个特征（即像素点）。
*   `y shape: (1288,)`：表示有1288个对应的标签。
*   `faces shape: (1288, 125, 94)`：表示有1288张图片，每张图片高125像素，宽94像素。

`X`和`faces`包含的是相同的数据，只是形状不同。`X`是二维的，便于机器学习算法处理；`faces`是三维的，便于我们以图片形式查看。`11750`这个数字正是`125 * 94`的结果。

## 数据可视化

为了直观理解数据，我们可以随机选择几张图片进行显示，并查看对应的人物标签。

以下是随机显示一张人脸图片并查看其对应人名的代码：

```python
# 随机选择一个索引
index = np.random.randint(0, 1288)

![](img/868817e6c632361c8c08d03017c3124f_15.png)

# 获取对应的人脸图片
face = faces[index]

# 显示图片
plt.imshow(face, cmap='gray')
plt.axis('off')
plt.show()

![](img/868817e6c632361c8c08d03017c3124f_17.png)

![](img/868817e6c632361c8c08d03017c3124f_19.png)

# 获取该图片对应的人名
target_names = data['target_names']
person_name = target_names[y[index]]
print(f"This person is: {person_name}")
```

![](img/868817e6c632361c8c08d03017c3124f_21.png)

代码说明：
1.  `np.random.randint`用于生成一个随机索引。
2.  `plt.imshow`用于显示图片。参数`cmap='gray'`指定使用灰度色彩映射，因为这是黑白图片。
3.  `target_names`是一个列表，存储了所有人名。`y[index]`是当前图片的标签编号，通过这个编号可以在`target_names`中找到对应的人名。

每次运行这段代码，都会随机显示一张不同的人脸图片并打印其姓名，例如“George W Bush”或“Tony Blair”。

此外，我们也可以从二维特征数据`X`中还原并显示图片，只需将其`reshape`回`(125, 94)`的形状即可。

```python
# 从X中还原并显示同一张图片
face_from_X = X[index].reshape(125, 94)
plt.imshow(face_from_X, cmap='gray')
plt.axis('off')
plt.show()
```

## 总结

![](img/868817e6c632361c8c08d03017c3124f_23.png)

本节课中我们一起学习了LFW人脸数据集的加载与初步探索。我们掌握了如何使用`fetch_lfw_people`函数加载数据，理解了返回的数据结构（包括特征数据`X`、标签`y`和原始图片`faces`），并学会了如何随机选取和可视化数据集中的图片，以及如何查看图片对应的人物信息。这些步骤为后续使用支持向量机等算法进行人脸识别奠定了数据基础。