# collections.defaultdict
创建一个当key不存在时返回设定值的字典

````python
kg_indexs = collections.defaultdict( list )   
for h, r, t in kg_triples:  
    kg_indexs[ str( h ) ].append([ int( t ), int( r ) ])  
return kg_indexs
````


# 解构（Unpack）
  
解构是一种将复合数据类型（如元组、列表、字典等）中的元素分离并分配给多个变量的过程。在 Python 中，解构可以用于多种数据结构，例如元组、列表、集合、字典等。
````python
kg_triples = [[1239, 2, 11917], [11917, 5, 1239], [1239, 2, 4496], [4496, 5, 1239], [1239, 2, 20384]]

for h, r, t in kg_triples 可以直接得到 h,r,t
比如第一次循环 h=1239，r=2，t=11917
````