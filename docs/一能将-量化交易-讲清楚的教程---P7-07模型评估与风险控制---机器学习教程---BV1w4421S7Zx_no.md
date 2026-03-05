# 量化交易与AI：P7：模型评估与风险控制 🧠

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_1.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_3.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_5.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_6.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_8.png)

在本节课中，我们将学习三个核心内容：对前期作业进行点评与改进、介绍集成学习方法来提升模型性能，并展望量化交易后续的学习路径。课程旨在帮助大家建立更严谨的建模流程，并掌握将多个“弱”模型组合成“强”模型的实用技巧。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_10.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_11.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_12.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_13.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_14.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_16.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_17.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_18.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_19.png)

## 作业点评与建模流程优化

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_21.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_22.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_23.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_24.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_26.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_28.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_30.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_31.png)

上一节我们介绍了遗传算法等概念，本节我们首先来看看大家提交的作业情况。许多同学非常努力，但在建模的“姿势”上仍有优化空间。以下是一个优秀作业范例所展示的标准流程。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_33.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_35.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_36.png)

### 作业提交规范

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_38.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_40.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_41.png)

提交作业时，无需提供具体函数实现的代码。最佳方式是提交运行后的 Jupyter Notebook 导出的 **HTML 文件**。这样，我可以高效地审阅你的建模逻辑和结果，并提出针对性的建议，而非调试代码细节。

### 标准建模流程

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_43.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_45.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_47.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_49.png)

以下是建立一个稳健模型应遵循的关键步骤：

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_51.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_52.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_53.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_55.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_57.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_59.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_61.png)

1.  **数据初探与可视化**
    在建模前，首要任务是检查数据是否正确。使用 `pandas.DataFrame.head()` 查看数据前几行，并进行简单的可视化，为后续建模热身。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_62.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_64.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_66.png)

2.  **划分数据集与数据清洗**
    明确定义训练集和测试集（例如70%/30%），并分离特征 `X` 和目标变量 `Y`。进行必要的数据清洗工作，例如处理空值。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_68.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_70.png)

3.  **模型训练与初步评估**
    使用选定的模型（如线性回归、随机森林）进行训练，并在测试集上评估性能。对于回归任务，常用 **R²分数** 和 **均方误差（MSE）** 作为评估指标。
    ```python
    # 示例：计算R²分数
    from sklearn.metrics import r2_score
    r2 = r2_score(y_true, y_pred)
    ```

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_72.png)

4.  **深入分析与迭代优化**
    这是投入时间最多、最能体现价值的部分。不应仅仅满足于运行默认参数的模型并汇报结果。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_74.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_76.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_78.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_80.png)

### 关键优化点

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_82.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_84.png)

*   **理解你的数据**：对于分类任务，务必绘制**类别分布直方图**。如果发现类别不平衡（例如99%的样本属于同一类），则需要采用过采样、欠采样或调整类别权重等方法处理，否则模型可能会忽略少数但重要的类别。
*   **不要偷懒，要迭代思考**：当一个模型表现不佳时（例如随机森林的R²为负值），应深入思考原因。是因为树的数量不够？参数设置不当？还是特征或数据本身有问题？**永远不要想当然地认为参数搜索空间很小**。
*   **指导思想**：
    1.  **不要偷懒**：对每个候选模型进行细致的参数探索。
    2.  **迭代与思考**：分析模型结果，理解其背后的原因。
    3.  **扩大搜索空间**：有时更好的结果隐藏在更广阔的参数空间中。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_86.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_87.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_88.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_90.png)

对于金融数据，模型性能（如R²）通常不会很高。在当前数据集上，**将R²从0.06提升到0.07以上**，才意味着建模工作真正开始步入正轨。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_91.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_93.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_95.png)

### 特征工程思路

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_97.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_99.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_101.png)

特征工程是量化模型的另一个核心。对于每个时间点 `t` 的股票数据，特征可以大致分为三类：
1.  **长线特征**：基本面数据等。
2.  **短线特征**：技术指标等。
3.  **自定义特征**：例如基于新闻的情感分析得分、另类数据等。这部分最能体现研究员的独创性。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_103.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_105.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_107.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_109.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_110.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_112.png)

---

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_114.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_116.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_117.png)

## 集成学习：化腐朽为神奇的利器 ⚙️

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_119.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_121.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_123.png)

当你拥有多个模型但单个模型性能都不尽如人意时，集成学习（Ensemble Learning）就是你的“救命神器”。其核心思想是“三个臭皮匠，顶个诸葛亮”。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_125.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_126.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_128.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_130.png)

### 集成学习在流程中的位置

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_132.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_134.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_136.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_138.png)

一个完整的量化建模流程通常包括：
1.  **特征提取**
2.  **建模**（得到多个基础模型）
3.  **集成**（将基础模型组合成最终模型）
4.  **策略开发**（基于模型预测制定交易规则）

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_140.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_142.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_144.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_146.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_148.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_150.png)

**集成是建模流程中必不可少且收益显著的最后一环。**

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_152.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_154.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_156.png)

### 基础集成方法

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_158.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_159.png)

1.  **硬投票**
    对于分类问题，将多个模型的预测结果（+1或-1）直接相加，根据总和的正负决定最终类别。
    `最终预测 = sign(模型1预测 + 模型2预测 + ... + 模型N预测)`
    这种方法平等看待每个模型，未考虑模型性能差异。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_161.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_163.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_165.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_166.png)

2.  **加权平均/混合**
    为每个模型的预测结果赋予权重，加权求和得到最终预测。关键是**如何确定权重**。
    `最终预测 = α1 * 模型1预测 + α2 * 模型2预测 + ... + αN * 模型N预测`
    一种简单方法是根据模型准确率分配权重。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_168.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_170.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_172.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_173.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_174.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_176.png)

### 高级集成方法：堆叠

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_178.png)

堆叠（Stacking）是一种更强大的方法，它使用一个**元模型**来学习如何最佳地组合基础模型的预测。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_180.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_182.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_184.png)

**核心思想**：将基础模型在验证集上的**预测结果**作为新的特征，将验证集的**真实标签**作为目标，训练一个次级模型（元模型）来学习这个映射关系。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_186.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_188.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_190.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_192.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_194.png)

**关键步骤（以防数据泄露）**：
1.  将训练集分为 `K` 折（例如5折）。
2.  对于每个基础模型（如SVM），使用其中 `K-1` 折训练，并在剩下的1折上做预测。遍历所有折，得到完整训练集上的一组“交叉验证预测”。
3.  对所有基础模型重复步骤2，得到多组预测，这些预测构成新的特征矩阵。
4.  用这个新的特征矩阵和原始标签，训练元模型（如线性回归）。
5.  将训练好的基础模型在原始测试集上预测，再将预测结果输入训练好的元模型，得到最终集成预测。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_196.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_198.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_199.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_201.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_203.png)

这种方法可以多层堆叠，且几乎总能带来性能提升。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_205.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_207.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_209.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_211.png)

---

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_212.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_214.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_216.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_217.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_219.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_220.png)

## 自适应提升算法：AdaBoost 🚀

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_222.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_224.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_226.png)

AdaBoost 是集成学习中另一个里程碑式的算法，它通过迭代调整数据权重和模型权重，将一系列“弱分类器”组合成一个“强分类器”。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_228.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_230.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_232.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_234.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_236.png)

### 核心思想

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_238.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_240.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_242.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_243.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_245.png)

1.  **对样本加权**：每一轮迭代后，增加被当前模型**分错**的样本的权重，减少分对样本的权重。这样，下一轮模型会更关注难分的样本。
2.  **对分类器加权**：根据每个弱分类器的准确率，为其分配一个权重 `α`。性能越好的分类器，在最终投票中的话语权越大。
3.  **迭代与组合**：上述过程不断迭代，最终的总分类器是所有弱分类器的加权投票结果。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_247.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_249.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_251.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_253.png)

### 算法流程与关键公式

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_255.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_257.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_259.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_261.png)

1.  **初始化**：为每个训练样本赋予相同的权重 `w_i = 1/N`。
2.  **循环迭代（对于 t=1 到 T）**：
    a. **训练弱分类器**：使用当前样本权重 `w_t` 训练一个弱分类器 `h_t`。
    b. **计算错误率**：计算 `h_t` 的加权错误率 `ε_t`。
    c. **计算分类器权重**：`α_t = 0.5 * ln((1 - ε_t) / ε_t)`。错误率越低，`α_t` 越大。
    d. **更新样本权重**：对于每个样本 `i`，根据其是否被 `h_t` 正确分类来更新权重：
        *   若正确：`w_i` 略微减小。
        *   若错误：`w_i` 增大。
        更新后需归一化，使所有权重之和为1。
3.  **组合最终模型**：`H(x) = sign( Σ(α_t * h_t(x)) )`

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_263.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_265.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_267.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_269.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_271.png)

### AdaBoost 的神奇之处

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_273.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_275.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_277.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_279.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_281.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_283.png)

*   **理论保证**：在理想条件下，最终模型的错误率可以指数级下降。
*   **不易过拟合**：实践中，即使迭代次数很多，测试错误率也常常持续下降而不反弹。
*   **实现简化**：其权重更新过程可以简化为一个非常优雅的形式：**在每一轮，将所有被正确分类的样本的总权重降至1/2，将所有被错误分类的样本的总权重提升至1/2**。这个简化大大提升了计算效率。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_285.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_287.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_288.png)

**实践建议**：可以直接使用 `scikit-learn` 中的 `AdaBoostClassifier` 或 `AdaBoostRegressor`，并可以自定义基础估计器（默认是决策树桩）。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_290.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_292.png)

---

## 课程总结与展望 🗺️

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_294.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_296.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_298.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_300.png)

本节课我们一起深入探讨了模型评估的细节、通过集成学习提升模型性能的方法，并介绍了强大的AdaBoost算法。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_302.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_304.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_306.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_308.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_310.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_312.png)

至此，我们已经覆盖了量化交易中**数据获取**、**特征表示**、**模型建立**和**模型集成**这几个核心基石。接下来的课程，我们将进入新的领域：

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_314.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_316.png)

1.  **策略与风控**：当你拥有一个预测准确率为80%、置信度95%的模型后，如何分配资金（例如使用凯利公式）？如何控制风险？这是将预测转化为盈利的关键。
2.  **交易引擎开发**：如何构建一个事件驱动的自动化交易系统，将数据、模型、策略和执行连接起来。
3.  **案例分析**：通过研读真实的量化研究论文和案例，获取在特征工程和模型构建上的灵感。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_318.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_320.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_321.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_323.png)

结课后，大家的主要工作将集中在**寻找新特征**和**开发新模型**上，而数据管道和交易引擎则可以借助开源平台或固化下来。请务必掌握好建模与集成这两项核心技能。

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_325.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_326.png)

![](img/4622ea09b7502d9fbf01a7c1ef8e0a6c_328.png)

**本周作业**：使用相同的数据集，运用本节课学习的集成方法（Stacking, Blending, AdaBoost等），将模型的R²分数提升到 **0.07 以上**。