### 作业 5

该作业的主要目的是**学习如何搭建 Transformer 完成指定生物学任务**。

<img src="..\_static\images\q5.png" height="400px" />

本次作业涉及的数据文件 `genotype_fitness_data.tsv`与[作业四](assignment4.md)相同。

#### 数据文件

该文件中需关注的列及其含义分别如下：

- `dna`：该列指示 DNA 基因型数据。
- `fitness`：蛋白适合度，为本次作业的预测目标。

#### 关于 Transformer 框架的搭建

你可以基于 Pytorch 内置的 `TransformerEncoderLayer` 直接进行调用，注意事项请参考作业 PPT。

你也可以基于 Pytorch 自行搭建相关的类实现 Transformer 框架，该部分可参考对分易中提供的代码示例以及网上资料。部分可以帮助理解注意力机制和 Transformer 搭建的资料如下：

- [动手学深度学习 —— 注意力机制](https://zh-v2.d2l.ai/chapter_attention-mechanisms/index.html)
- [Excel 可视化注意力机制运算](https://github.com/ImagineAILab/ai-by-hand-excel/blob/main/advanced/Self-Attention.xlsx)

#### 关于词嵌入（token embedding）

词嵌入中，你可以将 DNA 序列按照先前的格式转换为串联的独热编码，然后对其**进行形状转换**并**通过一个形状为 (4, embedding_dim) 的全连接层**（`nn.Linear`）计算每个 token 的 embedding：

```Python
# 独热编码转换的示例
>>> import torch
>>> import torch.nn as nn
>>> X_one_hot = torch.tensor([[0, 0, 0, 1, 0, 1, 0, 0]], dtype=torch.float32)
>>> X_one_hot.shape
torch.Size([1, 8]) # 形状为 (batch_size, concatenated_one_hot_encoding)
>>> X_one_hot = X_one_hot.view(1, 2, 4) # 形状为 (batch_size, sequence_len, one_hot_encoding)
>>> X_one_hot
tensor([[[0., 0., 0., 1.],
         [0., 1., 0., 0.]]])
>>> linear = nn.Linear(4, 10) # 形状为 (one_hot_encoding, embedding_dim)
>>> embedding = linear(X_one_hot)
>>> embedding.shape
torch.Size([1, 2, 10]) # 形状为 (batch_size, sequence_len, embedding_dim)
>>> embedding
tensor([[[-0.5414,  0.4139, -0.2857,  0.3318,  0.4266, -0.0915,  0.3114,
          -0.4350,  0.0161,  0.3460],
         [ 0.0621,  0.1637, -0.0687, -0.1976,  0.1287,  0.2911, -0.2988,
          -0.3576,  0.1104,  0.4234]]], grad_fn=<ViewBackward0>)
```

该示例仅供理解独热编码转换到词嵌入过程中张量形状的变化，请参考上述运算过程自行搭建一个类以实现独热编码到词嵌入的转换。

你也可以选择传入 token 的索引，通过 `nn.Embedding` 计算其 embedding：

```python
>>> import torch
>>> import torch.nn as nn
>>> X_token_idx = torch.tensor([[0, 1, 3, 2, 1]]) # 假设 0，1，2，3 分别代表四个不同的碱基（此处的数字即代表碱基的 '索引'）
>>> X_token_idx.shape
torch.Size([1, 5]) # 形状为 (batch_size, sequence_len)
>>> emb = nn.Embedding(4, 10) # 形状为 (num_of_token_idx, embedding_dim)，num_of_token_idx 即索引的总数量，此处仅四种碱基因此为 4
>>> embedding = emb(X_token_idx)
>>> embedding.shape
torch.Size([1, 5, 10]) # 形状为 (batch_size, sequence_len, embedding_dim)
>>> embedding
tensor([[[-0.2933, -1.1887, -0.3512, -1.7246, -0.6260, -1.7295, -0.9526,
           0.6080, -1.2085, -1.6522],
         [-0.8493,  1.2595,  0.6788,  0.5044, -0.2911,  1.2267, -1.5274,
           1.1209,  0.0558, -0.1310],
         [-0.0354,  1.0134,  1.0404,  0.0980,  0.3095,  0.9719, -2.3796,
           1.2440, -0.3585, -0.8697],
         [-0.4226,  0.9418, -0.7537,  1.8708,  0.7265, -0.6792,  0.5071,
          -1.0920, -0.0810, -2.5961],
         [-0.8493,  1.2595,  0.6788,  0.5044, -0.2911,  1.2267, -1.5274,
           1.1209,  0.0558, -0.1310]]], grad_fn=<EmbeddingBackward0>)
```

独热编码到 embedding 的图示理解：

<img src="..\_static\images\q5_onehot2embedding.png" height="300px" />

token 索引到 embedding 的图示理解：

<img src="..\_static\images\q5_embeddinglayer.png" height="300px" />

两种 embedding 计算方式本质上是相同的。

#### 关于位置嵌入（Positional embedding）

位置嵌入中，你可以参考 Transformer 原架构中提出的计算方法，通过正弦和余弦函数计算每个位置上的位置编码（这种情况下，位置编码是固定的）。

此外，你也可以使用 `nn.Parameter` 创建一个与序列长度等长的可学习参数作为每个位置上的位置编码（这种情况下，位置编码是可学习的）。

#### FAQ

##### 为什么我的模型框架不存在问题，但是我的训练效果非常差？

作为参考，本次作业最终能达到的 Pearson R 应当在 0.8 以上。如果最终你的模型性能未达到该水平，则可检查是否存在以下问题：

- 模型的架构过于复杂导致训练缓慢及性能提升不明显，该作业中模型不需要过于复杂，你可以尝试使用更低的 Attention Block 数量及 embedding 维度。
- 在训练前期使用了早停策略，使模型的训练过快地被终止。该作业中模型可能需要经过较多 epoch 才能得到性能提升，在训练前期可能会经历震荡而过早触发早停。这种情况下可以尝试延长早停的触发时间或者尝试直接设定一个较高的 epoch 数进行训练。
