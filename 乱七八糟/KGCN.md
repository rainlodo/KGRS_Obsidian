# 什么是 KGCN

KGCN 即知识图谱卷积网络（Knowledge Graph Convolutional Networks），能够结合知识图谱信息完成推荐
KGCN的中心思想是利用图神经网络的消息传递机制与基本的推荐思想结合训练

尽管 KGCN 叫做 GCN（图卷积）但主要还是以计算注意力的方式进行消息传递，这个注意力可以理解为该关系影响用户行为的偏好程度

# 什么什么是图神经网络的消息传递机制
图神经网络（Graph Neural Network，简称GNN）是一种用于处理图数据的机器学习模型。在图神经网络中，消息传递机制是一种核心的操作，用于在图结构中传递信息和更新节点的表示。

消息传递机制通常包括以下步骤：

1. **初始化节点表示（Initialization）：** 首先，每个节点都被赋予一个初始的表示，通常是通过将节点的特征向量作为初始表示。
    
2. **消息传递（Message Passing）：** 在每一轮的消息传递中，每个节点都会从其邻居节点接收消息，并根据接收到的消息更新自己的表示。这里的消息可以理解为邻居节点的信息，通过这些信息来更新当前节点的表示。
    
3. **聚合邻居信息（Aggregation）：** 接收到的消息通常会被聚合起来，形成一个综合的邻居信息。常见的聚合操作包括求和、求平均等。
    
4. **更新节点表示（Update）：** 利用聚合后的邻居信息，节点会更新自己的表示。这个更新过程可以通过神经网络层或其他学习模块来完成。
    
5. **重复迭代（Iteration）：** 上述的步骤会被重复执行多次，每一轮迭代中，节点的表示都会不断地被更新。


## 问题定义
给出用户项目交互矩阵Y与知识图谱G，预测用户u对之前未交互过的项目v感兴趣的概率，目标为学习一个概率预测函数
$$
\begin{aligned}\hat{y}_{u,v}=F(u,v|\Theta,Y,G)
\end{aligned}
$$
(item, h, t 都在同一向量空间)

## KG 的作用体现在哪？
用于构建 adj_entity 和 adj_relation （实体邻接列表与关系邻接列表，在每个项目id对应的下标处放上与其直接相连的项目以及他们之间的关系，邻居数受限于超参 n_neighbor）

example：(n_neighbor=5)
这是一个 adj_entity 的一部分
![[Pasted image 20231229231806.png]]

## 为什么这么做？
为了获得与待推荐项目 v 直接相连的项目的实体表示以及他们之间的关系表示，为计算用户关系分数和获得 v 的邻域表示并获得本轮流程 v 的最终表示做好准备
````python
def get_neighbors(self, items):  
    e_ids = [self.adj_entity[item] for item in items]  
    r_ids = [self.adj_relation[item] for item in items]  
    e_ids = torch.LongTensor(e_ids)  
    r_ids = torch.LongTensor(r_ids)  
    neighbor_entities_embs = self.entity_embedding(e_ids)  
    neighbor_relations_embs = self.relation_embedding(r_ids)  
    return neighbor_entities_embs, neighbor_relations_embs
````

## 用户-项目交互数据中表示用户与项目是否交互的数据不参与上述计算流程，只用于确认 KGCN 的预测是否正确，在训练模型时用以计算 loss
````python
# 此处 r 是表示用户与项目是否交互的数据
for u, i, r in tqdm(DataLoader(train_set, batch_size=batchSize, shuffle=True)):
	pre = net(u, i)  
	loss = loss_fcn(pre, r.float())  
	optimizer.zero_grad()  
	loss.backward()
````

# 怎么进行训练

## 首先创建item，user，relation 的向量空间，并获取adj_entity与adj_relation

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

## 随后开始进行消息传递流程

计算用户关系分数：*（计算实体的邻域表示时，用户关系分数充当个性化过滤器，因为我们将相对于这些用户特定分数有偏见的邻居聚集在一起。）*
$$
\begin{aligned}\pi^u_{R_i}=g(u,R_i)\quad g可为任意函数，此处进行求内积
\end{aligned}
$$
$$
\begin{split}一般来说，\pi^u_{R_i}表示关系r对用户u的重要性。例如，用户可能对与他以前喜欢的 \\
电影共享同一明星的电影更感兴趣而另一个用户可能更关心电影的类型。&\\
\end{split}
$$

随后使用 softmax 进行归一化(标准化)
$$
\begin{aligned}{\tilde\pi^u_{R_i}}=Softmax(\pi^u_{R_i})=\frac{\exp(\pi^u_{R_i}}{{\sum}_{j\in N_{(V)}}\exp(\pi^u_{R_i})} \quad 对 \pi^u_{R_i} 进行归一化 \\\\ 其中N_{(V)}为节点v的一阶邻居集
\end{aligned}
$$
随后
$$
\begin{aligned}进行一次加权求和操作得到 \tilde{v} \\
\tilde{v}={{\sum}_{i\in N_{(V)}}{\tilde{\omega}^u_{R_i}}}·e_i \quad 
\end{aligned}
$$


在代码上
````python
# [batch_size, n_neighbor]  
user_relation_scores = torch.sum(user_embeddings * neighbor_relations, dim=2)  
# [batch_size, n_neighbor]  
user_relation_scores_normalized = F.softmax(user_relation_scores, dim=-1)
# [batch_size, dim ]  
neighbor_vectors = torch.sum(user_relation_scores_normalized * neighbor_entitys, dim=1)
````

## 进行消息聚合
$$
\begin{aligned}经过消息聚合后便可得到本轮物品 V 的向量 v\\
\end{aligned}
$$
$$
\begin{aligned}聚合公式 \quad v = \sigma(W·agg(\tilde{v},e)) + b
\end{aligned}
$$
其中，作者给出三种聚合方式如下
1.求和聚合
$$
\begin{aligned} agg_{sum}(\tilde{v},e)= \tilde{v} + e \\
\\ e为V的初始向量
\end{aligned}
$$
2.拼接聚合
$$
\begin{aligned} &agg_{neighbor}(\tilde{v},e)= \tilde{v} || e \\\\
&使用拼接聚合时需要注意维度变化，假设\tilde{v}与e原维度都为F则拼接后变为2F，\\ &W和b的维度需要做相应的变化
\end{aligned}
$$

3.邻居聚合
$$
\begin{aligned}& agg_{sum}(\tilde{v},e)= \tilde{v} \\
&因为计算\tilde{v}的过程本身就可以称为邻居聚合
\end{aligned}
$$

在代码上
````python
if self.aggregator_method == 'sum':  
    output = item_embeddings + neighbor_vectors # neighbor_vectors 即 v波浪
elif self.aggregator_method == 'concat':  
    # [batch_size, dim * 2]  
    output = torch.cat([item_embeddings, neighbor_vectors], dim=-1)  
else:  # neighbor  
    output = neighbor_vectors
````

*若需要进行下一轮对V的聚合则需要将此处得到的v当作初始向量，继续重复上述流程（消息传递与聚合）*

## 预测：
$$
\begin{aligned}\widehat{y}_{uv}=f(u, v)\quad其中 f(·) 可以是任意函数，作者采用了内积\\
最后计算概率\quad p=sigmoid(y_{uv})
\end{aligned}
$$
````python
out = torch.sigmoid(torch.sum(user_embeddings * out_item_embeddings, dim=-1))
````

## 损失计算
若是 CTR 预估可以使用 BCE 损失函数，若是评分预测则可以使用平方差损失函数

$$
\begin{aligned} & BCE Loss=−(ytrue​log(y)+(1−ytrue​)log(1−y)) \\
& MSE Loss=n1​∑i=1n​(ytrue,i​−yi​)2
\end{aligned}
$$

将损失向前传播即可训练模型
````python
pre = net(u, i)  
loss = loss_fcn(pre, r.float())  
optimizer.zero_grad()  
loss.backward()  
optimizer.step()
````


# 怎么实现推荐？