### 作业 3

该作业的主要目的是**学习使用各种机器学习方法对指定生物学问题进行预测及模型评估，并初步了解和实现 PyTorch 的求导功能**。

<img src="..\_static\images\q3.png" height="400px" />

在作业附件中，有提供数据文件 `Miao2020_ICB_response.tsv`，你可通过与[作业二](assignment2.md)相同的方式对其进行读取和后续处理。

#### 数据文件

该文件中需关注的列及其含义分别如下：

- 第 2-26 列：指示病人的免疫细胞相关信息，为此次作业预测时需要用到的特征。
- `Response`：本次作业的预测目标，其中 R 表示响应，NR 表示不响应，在计算时可将 NR 视为 0（R 视为 1）。

第一列 `Patient` 指示病人的编号，在后续的处理过程中可以将其 drop 掉。

#### 参考材料

- 数据集的分割可参考：[scikit-learn train_test_split](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html)
- 各种机器学习方法的实现以及交叉验证函数的使用可参考：[scikit-learn API reference](https://scikit-learn.org/stable/api/index.html)
- 各种 metric 的计算对应函数可参考：[scikit-learn metrics](https://scikit-learn.org/stable/api/sklearn.metrics.html)

