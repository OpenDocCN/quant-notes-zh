# 懒人数据分析：P1：在Excel中运行Python或R语言代码 🚀

在本节课中，我们将学习如何在一个Excel插件中运行Python或R语言的代码。这使我们能够在熟悉的Excel环境中，利用Python或R的强大计算能力进行更复杂的数据分析。

## 概述与背景

上一节我们介绍了课程主题，本节中我们来看看为什么需要在Excel中运行其他编程语言。

通常，搜索“如何在Python或R中读取Excel数据”的结果很多，但反过来“如何在Excel中运行Python或R代码”则相对小众。Excel通过可视化按钮操作，对大多数人来说更易上手，学习代码的成本较高。

然而，Excel自身的计算能力有限。虽然通过简单的公式组合能完成编码、合并、描述统计等工作，但对于涉及多元统计和线性代数的复杂分析（如因子分析），Excel难以实现或缺乏完整方案。因此，需要调用Python或R执行计算，再将结果返回Excel。

最初尝试用Python实现此流程，但在开发学术数据分析功能（如因子分析）时，发现Python在问卷分析、结构方程模型等领域的库和教程相对较少。而R语言在统计和学术研究领域有更丰富的资源和支持。因此，后续也实现了在Excel中调用R语言代码的功能。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_1.png)

## 插件安装与配置

上一节我们了解了需求背景，本节中我们来看看如何安装和配置必要的工具。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_3.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_5.png)

首先需要安装一个专用的Excel插件。以下是完整的安装流程。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_7.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_9.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_1.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_11.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_13.png)

1.  **下载插件**：进入指定的交流群，在群公告中找到插件介绍链接。链接内包含下载地址和各功能的使用方法。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_3.png)
2.  **解压文件**：下载的文件是一个压缩包，解压后会看到一个安装程序图标。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_5.png)
3.  **运行安装**：关闭所有Excel窗口，双击安装程序。安装包较小（几兆），默认安装到D盘，按照提示点击“下一步”即可。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_7.png)
4.  **验证安装**：安装完成后打开Excel，如果出现一个新的选项卡，则表明安装成功。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_15.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_15.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_17.png)

如果安装未成功，请对照文档中列出的可能原因进行检查。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_19.png)

## 配置Python环境

![](img/ee4a9d7dc466a737843ad205d9e82a4f_21.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_23.png)

安装好插件后，我们需要配置Python环境才能运行代码。核心是关联Excel插件与电脑上的Python程序。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_25.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_27.png)

前提是电脑上需要安装Python。如果熟悉Python，可以自行安装并管理所需的库。如果不熟悉，可以使用插件提供的预配置压缩包。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_29.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_21.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_31.png)

以下是配置步骤：

1.  **获取Python环境**：在插件文档的“运行Python或R语言”章节底部，找到提供的Python压缩包（例如 `PY3114.7z`，代表Python 3.11.4版本）并下载。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_25.png)
2.  **解压并固定位置**：将压缩包解压到一个合适的位置（例如桌面），**此后该文件夹位置不应再变动**，否则需要重新配置。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_29.png)
3.  **关联DLL文件**：
    *   在Excel插件选项卡中，点击“R或Python”按钮。
    *   选择语言为“Python”。
    *   在“DLL路径”框中，需要填入Python解释器核心文件（`python3xx.dll`）的完整路径。该文件位于解压后的文件夹内（例如 `PY3114/python311.dll`）。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_31.png)
    *   可以直接在文件资源管理器复制该文件的地址，粘贴到输入框（去掉首尾引号），或点击右侧浏览按钮直接选择该文件。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_33.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_33.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_35.png)

完成以上步骤后，Excel插件便与Python环境关联成功。

## 在Excel中运行Python代码示例

环境配置完成后，我们通过一个具体例子来演示如何在Excel中运行Python代码。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_37.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_39.png)

首先，使用插件的“示例数据”功能生成测试数据。这会创建名为 `DF1` 和 `DF2` 的两个工作表。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_37.png)

在代码运行界面，勾选 `DF1` 和 `DF2`，表示将这两个工作表的数据导入Python环境，在代码中可以直接用同名变量（`DF1`, `DF2`）调用。

以下是一段示例Python代码及其解释：

```python
import pandas as pd
DF_append = pd.concat([DF1, DF2])  # 纵向追加数据
DF_merge = pd.merge(DF1, DF2)      # 横向匹配数据
col_names = DF_append.columns.tolist()  # 获取追加后数据的列名
row_count = DF_append.shape[0]          # 获取追加后数据的行数
```

*   **`import pandas as pd`**: 导入 `pandas` 库，它是处理表格数据的核心工具。
*   **`pd.concat`**: 实现**纵向追加**。将 `DF2` 的数据行接在 `DF1` 下方，列数不足处填充缺失值（NaN）。
*   **`pd.merge`**: 实现**横向匹配**。默认根据两个表共有的列进行匹配，合并后列数增加。
*   **`.columns` 和 `.shape[0]`**: 分别用于获取数据的列名和行数。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_41.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_43.png)

在“返回值”区域，我们需要指定将哪些Python变量结果返回到Excel。本例中返回四个结果：`DF_append`, `DF_merge`, `col_names`, `row_count`。每个返回值对应在Excel中生成的一个新工作表。

点击“确定”执行代码。首次运行可能较慢（涉及初始化），后续运行会更快。执行成功后，可以在Excel中查看返回的四个新工作表，分别对应追加结果、匹配结果、列名列表和行数。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_41.png)

通过这个示例，我们演示了在Excel中执行Python代码并获取结果的完整流程。**更合理的用法是**：先在专业的Python编辑器（如PyCharm）中调试好代码，确保无误后，再复制到插件中运行以获取结果。

## 配置与运行R语言代码

了解了Python的运行方式后，我们来看看如何配置和运行R语言代码，其流程与Python非常相似。

1.  **获取R环境**：同样从插件文档中下载提供的R语言压缩包（例如 `R4.4.0.7z`）并解压到固定位置。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_45.png)
2.  **关联DLL文件**：
    *   在插件界面选择语言为“R”。
    *   找到解压后R文件夹内的DLL文件，路径通常为 `R-4.4.0/bin/x64/R.dll`。将此路径填入“DLL路径”。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_47.png)
3.  **运行R代码**：使用“示例数据”生成测试数据表（`DF1`, `DF2`）。插件界面会提供对应的R语言示例代码，其功能（追加、匹配、获取列名和行数）与Python示例一致，但语法是R的。
    ```r
    DF_append <- rbind(DF1, DF2)  # 纵向追加
    DF_merge <- merge(DF1, DF2)   # 横向匹配
    col_names <- colnames(DF_append) # 列名
    row_count <- nrow(DF_append)     # 行数
    ```
4.  **查看结果**：执行后，R代码的结果同样会以新工作表的形式返回到Excel中。
    ![](img/ee4a9d7dc466a737843ad205d9e82a4f_53.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_45.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_47.png)

## 总结与未来计划

![](img/ee4a9d7dc466a737843ad205d9e82a4f_49.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_51.png)

本节课中我们一起学习了如何在一个Excel插件中配置Python和R语言环境，并运行代码将结果返回Excel。关键在于正确下载预配置的环境压缩包，并将其核心DLL文件路径关联到插件。

掌握此功能的目的，并非鼓励大家直接在Excel中编写复杂代码，而是为后续开发高级数据分析功能（如因子分析、中介效应分析等）打下基础。这些复杂功能将依赖Python或R的后端计算。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_53.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_57.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_55.png)

这是一个初步版本，未来会持续迭代优化。计划基于此能力逐步添加因子分析、简单及复杂的中介调节分析等功能。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_57.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_59.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_59.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_61.png)

欢迎提出对新功能的建议或对现有功能的优化意见，共同完善这个工具。

![](img/ee4a9d7dc466a737843ad205d9e82a4f_63.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_65.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_67.png)

![](img/ee4a9d7dc466a737843ad205d9e82a4f_65.png)