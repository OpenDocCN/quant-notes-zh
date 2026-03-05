# Python数据分析与可视化：P87：05 CSS样式初识_1 🎨

![](img/aa750694e02e9de5b2cb2e2f868b1c88_1.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_3.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_5.png)

在本节课中，我们将要学习CSS样式的基础知识。CSS是用于美化网页样式的技术，本节将介绍其三种基本应用方式，为后续学习网页布局打下基础。

![](img/aa750694e02e9de5b2cb2e2f868b1c88_7.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_9.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_11.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_13.png)

上一节我们介绍了HTML的基本元素，本节中我们来看看如何为这些元素添加样式。

![](img/aa750694e02e9de5b2cb2e2f868b1c88_15.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_17.png)

## CSS的三种应用方式

![](img/aa750694e02e9de5b2cb2e2f868b1c88_19.png)

CSS有三种方式可以为HTML标签元素添加样式。

![](img/aa750694e02e9de5b2cb2e2f868b1c88_21.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_23.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_24.png)

以下是三种方式的简要介绍：

![](img/aa750694e02e9de5b2cb2e2f868b1c88_26.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_28.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_30.png)

1.  **内联样式**：在HTML标签元素上直接使用`style`属性。
    *   代码示例：`<div style="border: 1px solid red; margin: 10px;">这是一个div</div>`
    *   特点：样式直接写在标签内，修改时需要找到对应标签。在专业开发中较少使用，因为样式与结构耦合度高，不利于维护和复用。仅适用于个别、独立的样式需求。

![](img/aa750694e02e9de5b2cb2e2f868b1c88_32.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_34.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_36.png)

2.  **内部样式表**：在HTML文档的`<head>`区域内使用`<style>`标签来包含样式规则。
    *   代码示例：
        ```html
        <head>
            <style type="text/css">
                div {
                    border: 1px solid red;
                    margin: 10px;
                }
                p {
                    background-color: yellow;
                    font-size: 18px;
                    font-family: "PingFang SC";
                }
            </style>
        </head>
        ```
    *   特点：样式集中在文档头部，通过**选择器**（如`div`， `p`）来指定哪些元素应用样式。可以批量修改同一类元素的样式，是常用的方式之一。

![](img/aa750694e02e9de5b2cb2e2f868b1c88_38.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_40.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_42.png)

3.  **外部样式表**：将样式规则写在一个独立的`.css`文件中，然后在HTML文件中通过`<link>`标签引入。
    *   外部CSS文件 (`test.css`) 内容：
        ```css
        div {
            border: 1px solid red;
            margin: 10px;
        }
        p {
            background-color: yellow;
            font-size: 18px;
            font-family: "PingFang SC";
        }
        ```
    *   HTML中引入方式：
        ```html
        <head>
            <link rel="stylesheet" type="text/css" href="test.css">
        </head>
        ```
    *   特点：实现了样式与HTML结构的完全分离。同一个CSS文件可以被多个HTML页面引用，便于统一网站风格和维护，是专业开发中最推荐的方式。

![](img/aa750694e02e9de5b2cb2e2f868b1c88_44.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_46.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_48.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_50.png)

## 样式应用效果与对比

![](img/aa750694e02e9de5b2cb2e2f868b1c88_52.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_54.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_56.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_58.png)

内部样式表和外部样式表通过**选择器**来工作。例如，`p { ... }`这个规则，会选择页面中所有的`<p>`标签并应用其内部定义的样式。这相比内联样式只能作用于单个标签，效率更高，也更易于管理。

![](img/aa750694e02e9de5b2cb2e2f868b1c88_60.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_62.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_64.png)

当项目规模变大，拥有多个页面时，外部样式表的优势就体现出来了。你只需要在一个`.css`文件中定义好通用样式，然后在所有需要的HTML页面中引入它即可。这就像在Python中导入模块一样，实现了代码的复用。

![](img/aa750694e02e9de5b2cb2e2f868b1c88_66.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_68.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_70.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_72.png)

在专业开发中，通常使用**内部样式表**和**外部样式表**，尽量避免使用**内联样式**，以保持代码的清晰和可维护性。

![](img/aa750694e02e9de5b2cb2e2f868b1c88_74.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_76.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_78.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_80.png)

![](img/aa750694e02e9de5b2cb2e2f868b1c88_82.png)

本节课中我们一起学习了CSS的三种基本应用方式：内联样式、内部样式表和外部样式表。理解了它们各自的写法、特点及适用场景，特别是内部和外部样式表通过选择器批量控制样式的核心机制。请务必动手尝试这三种方式，为后续的网页布局学习做好准备。