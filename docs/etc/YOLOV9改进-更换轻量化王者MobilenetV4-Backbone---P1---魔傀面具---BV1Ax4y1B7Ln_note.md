# YOLOv9改进教程：P1：更换轻量化主干网络MobileNetV4 🚀

![](img/cdd8f9bd8abfd772fc774388551c76dc_0.png)

在本节课中，我们将学习如何为YOLOv9目标检测模型更换主干网络（Backbone），特别是将其替换为轻量级的MobileNetV4。通过本教程，你将掌握在YOLOv9框架中灵活替换主干网络的基本流程。

## 概述与准备工作

在开始更换主干网络之前，需要确保你的YOLOv9项目环境已正确配置并可以运行。如果你尚未配置好环境或对YOLOv9不熟悉，建议先参考相关的入门教程。

上一节我们介绍了课程目标，本节中我们来看看具体的代码修改步骤。

![](img/cdd8f9bd8abfd772fc774388551c76dc_2.png)

## 第一步：修改基础模型文件

![](img/cdd8f9bd8abfd772fc774388551c76dc_4.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_6.png)

首先，我们需要对YOLOv9项目中的基础模型文件进行两处关键修改。这些修改位于 `models/yolo.py` 文件中的 `BaseModel` 类里。

![](img/cdd8f9bd8abfd772fc774388551c76dc_8.png)

以下是需要替换的第一个函数，请将其粘贴到指定位置：

![](img/cdd8f9bd8abfd772fc774388551c76dc_10.png)

```python
def _forward_once(self, x, profile=False, visualize=False):
    y, dt = [], []  # outputs
    for m in self.model:
        if m.f != -1:  # if not from previous layer
            x = y[m.f] if isinstance(m.f, int) else [x if j == -1 else y[j] for j in m.f]  # from earlier layers
        if profile:
            self._profile_one_layer(m, x, dt)
        x = m(x)  # run
        y.append(x if m.i in self.save else None)  # save output
        if visualize:
            feature_visualization(x, m.type, m.i, save_dir=visualize)
    return x
```

![](img/cdd8f9bd8abfd772fc774388551c76dc_12.png)

以下是需要替换的第二个函数：

![](img/cdd8f9bd8abfd772fc774388551c76dc_14.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_16.png)

```python
def fuse(self):  # fuse model Conv2d() + BatchNorm2d() layers
    LOGGER.info('Fusing layers... ')
    for m in self.model.modules():
        if isinstance(m, (Conv, DWConv)) and hasattr(m, 'bn'):
            m.conv = fuse_conv_and_bn(m.conv, m.bn)  # update conv
            delattr(m, 'bn')  # remove batchnorm
            m.forward = m.forward_fuse  # update forward
    self.info()
    return self
```

![](img/cdd8f9bd8abfd772fc774388551c76dc_18.png)

完成代码粘贴后，请在文件开头导入必要的 `timm` 库。如果尚未安装，可以使用命令 `pip install timm` 进行安装。

![](img/cdd8f9bd8abfd772fc774388551c76dc_20.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_22.png)

## 第二步：理解修改范围与通用性

![](img/cdd8f9bd8abfd772fc774388551c76dc_23.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_25.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_27.png)

需要说明的是，本次视频演示主要针对MobileNetV4进行替换。但之前发布的所有适用于YOLOv5的主干网络替换方法，同样适用于YOLOv9。

![](img/cdd8f9bd8abfd772fc774388551c76dc_29.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_31.png)

你只需要按照本视频讲解的步骤修改上述两部分代码即可。如果你想替换为其他主干网络（如FastNet、GhostNet等），大体的操作流程是相同的。

![](img/cdd8f9bd8abfd772fc774388551c76dc_33.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_35.png)

## 第三步：获取并配置新主干网络

![](img/cdd8f9bd8abfd772fc774388551c76dc_37.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_39.png)

我们以替换为MobileNetV4为例。`timm` 库集成了大量主流的卷积神经网络模型，我们无需下载额外文件，只需确保已安装该库。

以下是配置新主干网络的关键步骤：

![](img/cdd8f9bd8abfd772fc774388551c76dc_41.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_43.png)

1.  打开 `models/backbone` 目录下的对应文件（例如 `yolov5_backbone.py`）。
2.  将MobileNetV4相关的模型定义代码复制到该文件中。
3.  在文件开头的导入部分，确保导入了你将要使用的MobileNetV4变体。

例如，如果你想使用最小的MobileNetV4版本，配置可能如下所示：

![](img/cdd8f9bd8abfd772fc774388551c76dc_45.png)

```python
# 在 backbone 文件中导入
from timm.models import mobilenetv4

![](img/cdd8f9bd8abfd772fc774388551c76dc_47.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_49.png)

# ... 在配置字典或函数中指定
backbone = ‘mobilenetv4_small’
```

**核心概念**：主干网络（Backbone）是目标检测模型的特征提取器，其输出会送入**特征金字塔网络（FPN）**和检测头（Head）进行进一步处理。公式可以简化为：`特征图 = Backbone(输入图像)`。

## 第四步：处理特殊情况（如重参数化）

部分主干网络（如RepVGG）采用了**重参数化（Reparameterization）**技术，这在训练和推理阶段结构不同。

![](img/cdd8f9bd8abfd772fc774388551c76dc_51.png)

如果遇到此类网络，你需要进行额外调整：

![](img/cdd8f9bd8abfd772fc774388551c76dc_53.png)

1.  找到该主干网络在YOLOv5项目中对应的修改教程。
2.  通常需要在 `BaseModel` 的 `fuse` 函数或相关位置，添加针对该网络的重参数化处理逻辑。
3.  将对应的代码修改迁移到YOLOv9的相同位置。

![](img/cdd8f9bd8abfd772fc774388551c76dc_55.png)

## 实验运行与期望管理

![](img/cdd8f9bd8abfd772fc774388551c76dc_57.png)

完成替换后，你可以使用提供的配置文件（如 `yolov9-mobilenetv4.yaml`）启动训练。请注意，命令行中可能只需使用 `-c` 参数指定结构文件，而 `-e` 参数涉及预训练权重，可能因主干网络不同而无法直接使用。

![](img/cdd8f9bd8abfd772fc774388551c76dc_59.png)

![](img/cdd8f9bd8abfd772fc774388551c76dc_61.png)

需要理性看待替换主干网络带来的效果提升。并非所有替换都能显著提升模型精度（如mAP），有时可能旨在**减少参数量（Params）**和**计算量（FLOPs）**以提升速度。结果需要通过实验来验证。

![](img/cdd8f9bd8abfd772fc774388551c76dc_63.png)

## 总结

![](img/cdd8f9bd8abfd772fc774388551c76dc_65.png)

本节课中我们一起学习了为YOLOv9模型更换主干网络的全过程。我们首先修改了基础模型文件以支持新主干，然后以MobileNetV4为例讲解了配置方法，最后提到了处理重参数化等特殊情况的思路。记住，主干网络的替换是模型改进的常见手段之一，但其效果需要结合具体任务和数据集通过实验来评估。