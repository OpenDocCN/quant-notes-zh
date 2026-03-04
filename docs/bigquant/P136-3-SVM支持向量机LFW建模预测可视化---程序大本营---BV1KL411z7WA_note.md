# 机器学习实战：P136：使用SVM支持向量机进行LFW人脸识别建模、预测与可视化

在本节课中，我们将学习如何使用支持向量机（SVM）对LFW人脸数据集进行建模、预测，并最终将预测结果可视化展示出来。整个过程包括获取最佳模型参数、使用最佳参数训练模型、评估模型性能以及直观地展示预测结果。

## P136：3：获取并使用最佳参数建模

上一节我们介绍了如何通过网格搜索寻找最佳参数。本节中，我们来看看如何使用这些找到的最佳参数来构建我们的SVM模型。

执行网格搜索后，我们可以通过 `best_params_` 属性获取最优参数组合。

```python
best_params = grid_search.best_params_
```

获取到的最佳参数可能是：`{'C': 0.001, 'kernel': 'linear', 'tol': 0.01}`。这些参数是经过交叉验证筛选出的最合适组合。

既然筛选出了最佳参数，接下来我们就使用这些参数来创建SVM模型。

```python
# 使用最佳参数创建SVM模型
svc = SVC(C=0.001, kernel='linear', tol=0.01)
```

模型创建好后，我们需要将数据划分为训练集和测试集。这里我们使用PCA降维后的数据 `X_pca` 进行模型训练，但同时保留原始图像数据 `faces` 用于后续的可视化。

![](img/470919ffd16d1187d318610a8eaa8dc8_1.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_3.png)

```python
# 划分数据集，同时保留原始图像数据用于可视化
X_pca_train, X_pca_test, y_train, y_test, faces_train, faces_test = train_test_split(
    X_pca, y, faces, test_size=0.25, random_state=42
)
```

![](img/470919ffd16d1187d318610a8eaa8dc8_5.png)

请注意，`X_pca` 是原始数据 `X` 经过PCA降维后的结果，因此 `X_pca` 与 `X` 中的数据是一一对应的。同时，`faces` 是原始的三维图像数据，它与二维的 `X` 数据本质相同，只是形状不同。因此，`faces` 与 `X_pca` 在数据拆分后也能保持一一对应的关系。保留 `faces` 是为了后续能够将预测结果以图像形式展示出来。

数据准备就绪后，开始训练模型。

```python
# 训练模型
svc.fit(X_pca_train, y_train)

# 评估模型在训练集和测试集上的表现
train_score = svc.score(X_pca_train, y_train)
test_score = svc.score(X_pca_test, y_test)
print(f"训练数据得分: {train_score}")
print(f"测试数据得分: {test_score}")
```

执行代码后，你可能会看到类似 `训练数据得分: 1.0， 测试数据得分: 0.78` 的结果。这个分数表明模型可能存在过拟合（训练集完美，测试集一般），或者参数还有优化空间。支持向量机通常使用RBF核函数效果更好，我们可以尝试调整 `C`（惩罚系数）和 `tol`（容忍度）等参数来提升性能。例如，将核函数改为 `rbf`，并增大 `C` 值，可能会得到更好的测试分数。

![](img/470919ffd16d1187d318610a8eaa8dc8_7.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_9.png)

通过多次尝试，你会发现之前网格搜索得到的参数组合（如 `C=0.001, kernel=linear`）是经得起考验的，虽然测试得分可能有波动（因为 `train_test_split` 是随机划分的），但整体表现稳定。

![](img/470919ffd16d1187d318610a8eaa8dc8_11.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_13.png)

## P136：4：模型预测与结果可视化

上一节我们完成了模型的训练与评估。本节中，我们来看看如何用训练好的模型进行预测，并将预测结果以图像形式直观地展示出来。

首先，使用训练好的模型对测试集进行预测。

```python
# 对测试集进行预测
y_predict = svc.predict(X_pca_test)
```

接下来进行可视化。由于测试数据较多，我们只展示其中的一部分，例如50张图片。

![](img/470919ffd16d1187d318610a8eaa8dc8_15.png)

以下是绘制预测结果的关键步骤：

![](img/470919ffd16d1187d318610a8eaa8dc8_17.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_19.png)

1.  **设置画布尺寸**：根据要展示的行列数计算合适的画布大小。
2.  **循环绘制子图**：为每一张要展示的测试图片创建一个子图。
3.  **显示人脸图片**：在子图中显示对应的原始人脸图像（来自 `faces_test`）。
4.  **添加标题**：在图片上方显示该人脸的真实姓名和模型预测的姓名。

![](img/470919ffd16d1187d318610a8eaa8dc8_21.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_23.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_25.png)

```python
import matplotlib.pyplot as plt

![](img/470919ffd16d1187d318610a8eaa8dc8_27.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_29.png)

# 设置画布大小：10行5列，每列宽2，每行高3（为标题留出空间）
plt.figure(figsize=(5*2, 10*3))

![](img/470919ffd16d1187d318610a8eaa8dc8_31.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_33.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_35.png)

# 循环绘制前50个测试样本
for i in range(50):
    # 创建子图，10行5列，当前是第i+1个
    plt.subplot(10, 5, i+1)
    
    # 显示对应的人脸图片（使用faces_test中的数据）
    plt.imshow(faces_test[i], cmap='gray')
    plt.axis('off')  # 关闭坐标轴刻度
    
    # 获取真实姓名和预测姓名
    # y_test[i] 是真实类别编号，通过 target_names 转换为姓名
    true_name = target_names[y_test[i]].split()[-1]  # 取姓氏部分
    # y_predict[i] 是预测类别编号
    predict_name = target_names[y_predict[i]].split()[-1]
    
    # 为子图添加标题，格式为：真实姓名 \n 预测姓名
    plt.title(f"True: {true_name}\nPred: {predict_name}")

![](img/470919ffd16d1187d318610a8eaa8dc8_37.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_39.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_41.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_43.png)

plt.tight_layout()  # 自动调整子图参数，使之填充整个图像区域
plt.show()
```

![](img/470919ffd16d1187d318610a8eaa8dc8_45.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_47.png)

**代码逻辑说明**：
*   `faces_test` 和 `X_pca_test` 在数据拆分时是一一对应的，因此 `faces_test[i]` 对应的就是 `X_pca_test[i]` 所代表的人脸图像。
*   `y_test[i]` 是 `faces_test[i]` 的真实标签（数字编码），通过 `target_names` 列表可以将其转换为人名。
*   同理，`y_predict[i]` 是模型对 `X_pca_test[i]` 的预测标签。
*   在标题中，我们只显示人名的姓氏部分（通过 `.split()[-1]` 实现），以使显示更简洁。

![](img/470919ffd16d1187d318610a8eaa8dc8_49.png)

运行上述代码后，你将看到一个包含50张人脸图像的网格。每张图像上方会标注其真实身份和模型预测的身份。通过观察，你可以直观地评估模型的准确率，例如“十个当中可能有两个预测错误”。这种可视化方式非常有助于理解模型在具体样本上的表现。

## 总结

![](img/470919ffd16d1187d318610a8eaa8dc8_51.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_53.png)

本节课中我们一起学习了使用支持向量机（SVM）完成LFW人脸识别项目的完整流程：
1.  **获取最佳参数**：利用 `GridSearchCV` 进行网格搜索，找到SVM模型（`C=0.001, kernel=linear, tol=0.01`）的最佳超参数组合。
2.  **构建与训练模型**：使用获得的最佳参数实例化SVM模型，并将PCA降维后的数据划分为训练集和测试集进行模型训练。
3.  **评估模型性能**：计算模型在训练集和测试集上的得分，理解模型表现并认识参数调优的重要性。
4.  **预测与可视化**：使用训练好的模型对测试集进行预测，并通过Matplotlib将原始人脸图像、真实标签及预测标签一同可视化展示出来，从而直观、有效地评估模型的识别效果。

![](img/470919ffd16d1187d318610a8eaa8dc8_55.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_57.png)

![](img/470919ffd16d1187d318610a8eaa8dc8_59.png)

整个过程展示了如何将一个机器学习算法应用于实际的图像分类任务，从数据预处理、模型调参、训练评估到结果展示，构成了一个完整的实践闭环。