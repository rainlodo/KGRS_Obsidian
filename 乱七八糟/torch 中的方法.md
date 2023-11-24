## torch 中 unsqueeze 与 squeeze
### unsqueeze可视化
在指定 dim 处添加一个大小为 1 的维度
![[Pasted image 20231123114316.png]]


### squeeze
直接调用则压缩所有大小为1的维度

## nn.Parameter(...)
将张量封装成 `nn.Parameter` 对象。`nn.Parameter` 是一个特殊的张量，用于表示模型的可学习参数。将张量封装为 `nn.Parameter` 后，这个参数会被模型自动追踪并更新。

## torch.empty(dim, 1)
创建一个大小为 `(dim, 1)` 的未初始化的张量。这里 `dim` 是传递给构造函数的参数，代表了张量的维度。

## init.xavier_uniform_(...)
对创建好的 `nn.Parameter` 进行 Xavier/Glorot 均匀初始化。Xavier初始化是一种常用的权重初始化方法，旨在加速神经网络的收敛。`xavier_uniform_` 是其中一种初始化方法，它使用均匀分布来初始化权重。
