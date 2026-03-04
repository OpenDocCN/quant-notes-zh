# 机器学习：P179：4-决策树graphviz安装与可视化 📊

在本节课中，我们将学习如何使用 Graphviz 工具来可视化决策树模型。与 Matplotlib 相比，Graphviz 能生成更美观、更专业的树形图。

## 概述

上一节我们介绍了使用 Matplotlib 进行决策树可视化。本节中，我们来看看另一种更美观的方式——使用 Graphviz。Graphviz 生成的决策树图形具有圆角等样式，视觉效果更佳。

## 安装 Graphviz 环境

要使用 Graphviz，需要安装两个部分：Python 库和对应的桌面软件。

以下是安装步骤：

1.  **安装 Python 库**
    在命令行中执行以下命令来安装 `graphviz` Python 库。
    ```bash
    pip install graphviz
    ```

2.  **安装 CMake 软件**
    仅安装 Python 库还不够，还需要安装 CMake 软件作为支持。请访问 CMake 官网下载页面，根据你的操作系统（如 Windows 64位）下载后缀为 `.msi` 的安装程序。安装过程中，在出现添加环境变量的选项时，请务必勾选，然后一路点击“下一步”完成安装。

3.  **安装 Graphviz 软件**
    接下来需要安装 Graphviz 软件本身。同样，访问其官网下载页面，找到适用于 Windows 的 `.exe` 安装程序（通常标记为 “Windows” 版本）。下载后双击安装，在安装向导中同样勾选“添加环境变量”的选项，然后完成安装。

**注意**：完成上述所有安装后，请关闭并重新打开你的命令行终端，以确保环境变量生效。

## 使用 Graphviz 可视化决策树

安装好环境后，我们就可以在代码中使用 Graphviz 了。

首先，需要导入必要的模块。
```python
import graphviz
from sklearn.tree import export_graphviz
```

假设我们已经训练好一个决策树模型 `model`，并且有特征数据 `X` 和标签 `y`。以下是生成并显示决策树图形的核心代码：

```python
# 导出决策树的结构数据
dot_data = export_graphviz(model,
                           out_file=None,
                           filled=True,          # 使用颜色填充节点
                           rounded=True,         # 使用圆角节点
                           feature_names=X.columns,  # 传入特征名称
                           class_names=np.unique(y)  # 传入类别名称
                          )

# 创建图形对象并显示
graph = graphviz.Source(dot_data)
graph
```
执行这段代码，Jupyter Notebook 中就会显示一个带有颜色和圆角的决策树图形。参数 `feature_names` 和 `class_names` 分别用于显示特征和分类的具体名称，使图形更易读。

## 保存决策树图形

我们可以将生成的决策树保存为图片文件，支持多种格式。

以下是保存图形的代码：
```python
# 将图形保存为PNG、JPG或PDF格式
graph.render('./decision_tree_graph', format='png')  # 保存为PNG
# graph.render('./decision_tree_graph', format='jpg')   # 保存为JPG
# graph.render('./decision_tree_graph', format='pdf')   # 保存为PDF
```
运行后，会在当前目录下生成对应的图片文件。其中，PDF 格式通常能提供最清晰的矢量图效果。

## 解决中文乱码问题

如果图形中的中文字符显示为乱码，是因为默认字体不支持中文。我们需要修改 Graphviz 生成的中间文件来指定中文字体。

以下是解决步骤：

1.  **读取并修改图形源文件**
    首先，将决策树导出为一个 `.dot` 文本文件。
    ```python
    export_graphviz(model, out_file='./graph.txt', ...) # 参数同上，指定输出文件
    ```
    然后，读取这个文件，并将其中的字体设置替换为中文字体（如“仿宋”）。
    ```python
    import re

    # 读取导出的 .dot 文件
    with open('./graph.txt', 'r', encoding='utf-8') as f:
        s = f.read()

    # 使用正则表达式替换字体名称
    s = re.sub('fontname="[^"]+"', 'fontname="FangSong"', s)

    # 将修改后的内容写入新文件
    with open('./graph_corrected.txt', 'w', encoding='utf-8') as f:
        f.write(s)
    ```

![](img/49b8763ca4a7b32ccd0aeebe5e779bb1_1.png)

2.  **从修改后的文件重新生成图形**
    使用修改后的 `.dot` 文件来创建和保存图形，中文就能正常显示了。
    ```python
    # 从修正后的文件创建图形
    with open('./graph_corrected.txt', 'r', encoding='utf-8') as f:
        corrected_graph = graphviz.Source(f.read())

    # 显示图形
    corrected_graph

    # 保存图形（此时中文正常）
    corrected_graph.render('./decision_tree_chinese', format='pdf')
    ```

![](img/49b8763ca4a7b32ccd0aeebe5e779bb1_3.png)

## 总结

![](img/49b8763ca4a7b32ccd0aeebe5e779bb1_5.png)

本节课中我们一起学习了决策树可视化的第二种方法——Graphviz。我们首先完成了 Graphviz 库及其依赖软件的安装，然后使用 `export_graphviz` 和 `graphviz.Source` 来生成并显示美观的树形图。我们还学会了如何将图形保存为不同格式的文件，并解决了可视化时可能出现的中文乱码问题。Graphviz 是展示树模型结构的强大工具，能让结果更加直观和专业。