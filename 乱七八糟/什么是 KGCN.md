
KGCN 即知识图谱卷积网络（Knowledge Graph Convolutional Networks），能够结合知识图谱信息完成推荐

# 什么什么是图神经网络的消息传递机制
图神经网络（Graph Neural Network，简称GNN）是一种用于处理图数据的机器学习模型。在图神经网络中，消息传递机制是一种核心的操作，用于在图结构中传递信息和更新节点的表示。

消息传递机制通常包括以下步骤：

1. **初始化节点表示（Initialization）：** 首先，每个节点都被赋予一个初始的表示，通常是通过将节点的特征向量作为初始表示。
    
2. **消息传递（Message Passing）：** 在每一轮的消息传递中，每个节点都会从其邻居节点接收消息，并根据接收到的消息更新自己的表示。这里的消息可以理解为邻居节点的信息，通过这些信息来更新当前节点的表示。
    
3. **聚合邻居信息（Aggregation）：** 接收到的消息通常会被聚合起来，形成一个综合的邻居信息。常见的聚合操作包括求和、求平均等。
    
4. **更新节点表示（Update）：** 利用聚合后的邻居信息，节点会更新自己的表示。这个更新过程可以通过神经网络层或其他学习模块来完成。
    
5. **重复迭代（Iteration）：** 上述的步骤会被重复执行多次，每一轮迭代中，节点的表示都会不断地被更新。

# 怎么实现推荐
KGCN的中心思想是利用图神经网络的消息传递机制与基本的推荐思想结合训练

尽管 KGCN 叫做 GCN（图卷积）但主要还是以计算注意力的方式进行消息传递，这个注意力可以理解为该关系影响用户行为的偏好程度。

首先创建item，user，relation 的向量空间

$$
\begin{aligned}item_{emb},\;relation_{emb},\;user_{emb}=embedding(\\itemNum,\;relationNum,\;userNum,dim)
\end{aligned}
$$

在代码上
````python
self.user_embedding = nn.Embedding(user_num, e_dim, max_norm=1)  
self.entity_embedding = nn.Embedding(entity_num, e_dim, max_norm=1)  
self.relation_embedding = nn.Embedding(relation_num, e_dim, max_norm=1)
````

随后开始进行消息传递流程

权重计算：
$$
\begin{aligned}\omega^u_{R_i}=g(u,R_i)\end{aligned}\quad g与f类似，此处进行求内积
$$


![[Pasted image 20231227163517.png]]


predict：
$$
\begin{aligned}y_{uv}=f(u, v)\quad其中 f(·) 可以是任意函数，作者采用了内积\\
最后计算概率\quad p=sigmoid(y_{uv})
\end{aligned}
$$


