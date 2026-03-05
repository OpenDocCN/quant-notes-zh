# Python编程：02：Jupyter Notebook使用方法 🚀

在本节课中，我们将正式开启Python编程的学习。首先，我们将重点掌握Jupyter Notebook这一强大工具的基本使用方法，包括界面操作、常用快捷键以及一些实用的“魔法命令”。掌握这些知识将为后续的编程学习打下坚实基础。

![](img/9f918ea257dd9c29312c6fe644991960_1.png)

## 界面与基本操作 🖥️

![](img/9f918ea257dd9c29312c6fe644991960_3.png)

上一节我们开启了Python学习之旅，本节中我们来看看Jupyter Notebook这个编程环境的具体使用方法。Jupyter Notebook是一个基于网页的交互式计算环境，非常适合数据分析和机器学习。

在网页界面的上方，有两行工具栏按钮，这些按钮提供了Jupyter Notebook的核心操作功能。

以下是文件（File）菜单下的主要操作：

*   **New Notebook Python3**：新建一个Python 3代码文件。
*   **Open**：打开一个新的Notebook页面。
*   **Make a Copy**：复制当前Notebook。
*   **Save as**：将当前Notebook另存为。
*   **Rename**：重命名当前Notebook。也可以直接点击Notebook顶部的文件名进行重命名。
*   **Download as**：将Notebook下载为其他格式的文件（如.py, .pdf等）。

以下是编辑（Edit）菜单下的主要操作，主要针对单元格（Cell）：

*   **Cut Cells**：剪切选中的单元格。
*   **Copy Cells**：复制选中的单元格。
*   **Paste Cells Above / Below / & Replace**：将复制的单元格粘贴到上方、下方或替换当前单元格。
*   **Delete Cells**：删除选中的单元格。
*   **Undo / Redo**：撤销或重做上一步操作。
*   **Split Cell**：拆分当前单元格。
*   **Merge Cell Above / Below**：与上方或下方的单元格合并。
*   **Move Cell Up / Down**：将单元格上移或下移。
*   **Insert Image**：插入图片（仅在Markdown格式的单元格中可用）。

![](img/9f918ea257dd9c29312c6fe644991960_5.png)

![](img/9f918ea257dd9c29312c6fe644991960_7.png)

以下是单元格（Cell）菜单下的主要操作：

![](img/9f918ea257dd9c29312c6fe644991960_9.png)

*   **Run Cells**：运行当前选中的单元格。
*   **Run Cells and Select Below**：运行当前单元格并选中下一个。
*   **Run Cells and Insert Below**：运行当前单元格并在下方插入一个新的单元格。
*   **Run All**：运行当前Notebook中的所有单元格。
*   **Run All Above / Below**：运行当前单元格上方或下方的所有单元格。

以下是内核（Kernel）菜单下的主要操作，用于管理代码执行：

![](img/9f918ea257dd9c29312c6fe644991960_11.png)

*   **Interrupt**：中断当前正在运行的代码。
*   **Restart**：重启内核（即重置Python解释器）。
*   **Restart & Clear Output**：重启内核并清除所有已运行的输出结果。
*   **Restart & Run All**：重启内核并运行所有单元格。

工具栏上还有一些常用按钮的快捷方式，例如保存、在下方插入代码单元格、剪切、复制、粘贴、上移、下移、运行、中断和重启等。

单元格主要有两种格式：

*   **代码（Code）**：用于编写和执行Python代码。
*   **Markdown**：用于编写富文本，如标题、段落、列表、公式、图片和表格。

## 核心概念：键盘输入模式 ⌨️

了解了基本操作后，我们来看看提升效率的关键——键盘快捷键。但在使用快捷键前，必须理解Jupyter Notebook的两种键盘输入模式。

*   **编辑模式（Edit Mode）**：允许向单元格内输入代码或文本。此时单元格边框为**绿色**。
*   **命令模式（Command Mode）**：将键盘与Notebook级别的命令绑定，用于操作单元格本身（如移动、删除）。此时单元格边框为**灰色**，且左侧边框为**蓝色**。

使用 `Esc` 键可以从编辑模式切换到命令模式。
使用 `Enter` 键可以从命令模式切换到编辑模式。

## 常用快捷键大全 ⚡

熟练使用快捷键可以极大提升编程效率。以下是按模式分类的常用快捷键列表。

在命令模式下，可以对单元格进行整体操作。以下是常用命令模式快捷键：

*   **`Shift + ↑/K`**：连续向上选中多个单元格。
*   **`Shift + ↓/J`**：连续向下选中多个单元格。
*   **`A`**：在当前单元格上方插入新单元格。
*   **`B`**：在当前单元格下方插入新单元格。
*   **`D, D` (按两次D)**：删除选中的单元格。
*   **`X`**：剪切选中的单元格。
*   **`C`**：复制选中的单元格。
*   **`V`**：在下方粘贴单元格。`Shift + V` 则在上方粘贴。
*   **`Z`**：撤销上一次的单元格删除操作。
*   **`M`**：将当前单元格转换为Markdown格式。
*   **`Y`**：将当前单元格转换为Code格式。
*   **`1` 到 `6`**：将当前单元格转换为相应级别的标题（Markdown格式）。
*   **`Shift + Enter`**：运行当前单元格，并选中下一个单元格。
*   **`Ctrl + Enter`**：运行当前单元格，并保持选中状态。
*   **`Alt + Enter`**：运行当前单元格，并在下方插入一个新单元格。

在编辑模式下，可以对单元格内的内容进行编辑。以下是常用编辑模式快捷键：

*   **`Tab`**：代码自动补全或增加缩进。
*   **`Shift + Tab`**：减少缩进或查看函数/对象的文档提示。
*   **`Ctrl + ]`**：增加选中行的缩进。
*   **`Ctrl + [`**：减少选中行的缩进。
*   **`Ctrl + A`**：全选单元格内容。
*   **`Ctrl + Z`**：撤销上一步编辑操作。
*   **`Ctrl + /`**：注释或取消注释选中的行。
*   **运行单元格的快捷键**（`Shift+Enter`, `Ctrl+Enter`, `Alt+Enter`）与命令模式下相同。

## 实用“魔法命令” 🪄

除了快捷键，Jupyter Notebook还提供了一系列以 `%` 或 `%%` 开头的“魔法命令”（Magic Commands），用于执行一些特殊功能。魔法命令主要有两种语法：

*   **行魔法（Line Magic）**：以单个 `%` 开头，作用于本行。例如：`%timeit`
*   **单元魔法（Cell Magic）**：以两个 `%` 开头，作用于整个单元格。例如：`%%timeit`

以下是几个非常实用的魔法命令介绍：

*   **`%lsmagic`**：列出所有可用的魔法命令。
    ```python
    %lsmagic
    ```

*   **`%magic?`** 或 **`%命令名?`**：查看魔法命令或任何对象的帮助文档。
    ```python
    %lsmagic?
    ```

*   **`%matplotlib inline`**：让 `matplotlib` 绘制的图表直接嵌入在Notebook中显示，而不是弹出独立窗口。
    ```python
    %matplotlib inline
    import matplotlib.pyplot as plt
    plt.plot([1,2,3,4])
    plt.show()
    ```

*   **`%time` 与 `%timeit`**：测量代码运行时间。`%time` 运行一次，`%timeit` 会多次运行取平均值，更准确。
    ```python
    %time sum(range(1000000))
    %timeit sum(range(1000000))
    ```

*   **`%%time` 与 `%%timeit`**：测量整个单元格代码的运行时间。
    ```python
    %%timeit
    total = 0
    for i in range(1000000):
        total += i
    ```

*   **`%run`**：运行外部的Python脚本（.py文件）。
    ```python
    %run hello.py  # 假设当前目录有 hello.py 文件
    ```

*   **`%pwd`**：打印当前工作目录。
    ```python
    %pwd
    ```

*   **`%cd`**：更改当前工作目录。`%cd ..` 返回上级目录。
    ```python
    %cd ./my_folder  # 进入子目录
    %cd ..           # 返回上级目录
    ```

*   **`%who` / `%whos`**：查看当前命名空间中定义的所有变量。`%whos` 会显示更详细的信息（类型、值等）。
    ```python
    a = 10
    b = “hello”
    %whos
    ```

## 总结 📝

本节课中，我们一起学习了Jupyter Notebook这一核心工具。我们首先熟悉了其网页界面的基本操作，包括文件管理、单元格编辑和代码运行。接着，我们深入理解了**编辑模式**和**命令模式**这两种关键的键盘输入状态，并掌握了大量提升效率的**快捷键**。最后，我们探索了功能强大的**魔法命令**，它们能帮助我们列出命令、查看文档、测量性能、管理文件路径和变量空间。

![](img/9f918ea257dd9c29312c6fe644991960_13.png)

熟练掌握Jupyter Notebook的使用，将为你后续的Python编程学习铺平道路，让你能更专注于代码逻辑本身。在接下来的课程中，我们将开始学习Python的基础语法和数据类型。