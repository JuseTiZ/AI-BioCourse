### 作业 2

该作业的主要目的是**基于生物学数据进行数据的预处理，并通过线性模型完成指定生物学任务**。

<img src="..\_static\images\q2.png" height="400px" />

在作业附件中，有提供数据文件 `sgRNA_data.tsv`，你可将其移动至与 jupyter notebook 文件相同的文件夹并通过 pandas 库对其进行读取：

```python
import pandas as pd
data = pd.read_csv("./sgRNA_data.tsv", sep="\t")
```

#### 数据文件

该文件中需关注的列及其含义分别如下：

- `sgRNA_Context`：sgRNA + context，从**第五个碱基开始到第二十五个碱基**为 sgRNA 的序列（可以通过字符串切片操作将其挑取出来）。
- `Target_Gene`：该 sgRNA 靶向的基因。
- `sgRNA_Activity`：sgRNA 的编辑效果，为本次作业的预测目标。

#### 关于独热编码

独热编码（One-Hot encoding）是一种常用的分类变量处理方法，其思路是将每个类别值转换为一个二进制向量：

- 创建一个长度为类别总数的向量。
- 对于各类别所对应的位置标记为 1，其余位置标记为 0。

以该作业的情况为例，在使用单碱基独热编码时，共四种情况：

<img src="..\_static\images\q2_onehot.png" height="300px" />

其中，四种碱基 `A` `C` `G` `T` 分别对应一个长度为 4 的向量，并仅在一个位置上为 1（其他皆为 0）。对于一段序列（例如上图中的 `ACG`），可**将其所有的碱基转换为向量并串联，即可得到该序列对应的独热编码结果**。对于二碱基独热编码同理，共有 16 种可能的二碱基组合，其中每一种组合都对应一个长度为 16 的向量。注意，二碱基独热编码时，相邻的二碱基是有重叠的：

<img src="..\_static\images\q2_onehot_twoorder.png" height="300px" />

为了得到 sgRNA 序列的独热编码结果，你可以使用字典保存每一种单碱基（二碱基）和对应向量的关系，通过 `for` 循环遍历该序列的单碱基（二碱基）并将把对应向量保存至同一个列表中，最后使用 `numpy` 的 `concatenate` 将其串联至一起：

```python
import numpy as np

one_hot_list = [
    [1, 0, 0, 0],
    [0, 0, 0, 1],
    [0, 1, 0, 0]
] # 假设这为你已得到的 sgRNA 序列编码（单碱基）

np.concatenate(one_hot_list)
```

```output
array([1, 0, 0, 0, 0, 0, 0, 1, 0, 1, 0, 0])
```

具体的实现方式非常多，以上只是其中的一个思路，仅作参考用。

#### 挑选基因

该作业使用留一基因法进行验证，其他在生物学中常见的类似操作还有留一染色体验证，留一个体验证等。某些场景下，这种计算方式能更准确地评估模型的泛化能力并指导模型选择。

你可以通过 `random` 或 `numpy` 库随机挑选一个基因，也可自行挑选。作为拓展，如果你感兴趣，也可以试试通过留一基因交叉验证生成一个折外预测集（out-of-fold）进行模型评估。

#### 参考材料

- 数据框的处理可参考 [Pandas 数据结构 - DataFrame](https://www.runoob.com/pandas/pandas-dataframe.html)，矩阵的处理和转换可参考 [NumPy 教程](https://www.runoob.com/numpy/numpy-tutorial.html)。
- 线性模型的使用可参考 scikit-learn 页面：[LinearRegression](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LinearRegression.html) / [Lasso](https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.Lasso.html)。

#### FAQ

##### 为什么使用 Lasso 预测时，预测结果扁平且与真实值不存在显著相关？

该问题可以参照作业 PPT 的最后一页。Lasso 的 `alpha` 值默认为 1，在该作业中带来的正则化可能过强，你可以尝试调低该值并重新进行训练和预测。

##### 为什么我进行训练时，花了很长时间都没有运行完？

在使用 `.fit` 进行训练时，输入的特征矩阵 `X` 和目标向量 `y` 应该满足以下格式要求：

特征矩阵 `X`：数组、列表或支持数组接口的其他类型，形状应为 `(n_samples, n_features)`，其中 `n_samples` 是样本数量（行数），`n_features` 是特征数量（列数）。该作业中，独热编码后的 `n_features` 应当为 **80（单碱基）和 304（二碱基）**。

目标向量 `y`：应为一维数组或列表，形状为 `(n_samples,)`，检查方式同上。

训练一直运行可能是由于特征矩阵 `X` 存在问题，请检查其形状是否符合预期。

