和 [[DeepWide]] 的模型类似，DeepFM 模型同样由浅层模型和深层模型联合训练得到。不同点主要有以下两点：
1. wide 模型部分由 [[LR]] 替换为 [FM] 。FM 模型具有自动学习交叉特征的能力，避免了原始 DeepWide 模型中浅层部分人工特征工程的工作。
2. 共享原始输入特征。DeepFM 模型的原始特征将作为 FM 和Deep 模型部分的共同输入，保证模型特征的准确与一致。

DeepFM模型包含 FM 和 [[DNN]] 两部分，FM模型可以抽取 low-order 特征，DNN 可以抽取 high-order 特征。无需 DeepWide 模型一般进行人工特征工程。