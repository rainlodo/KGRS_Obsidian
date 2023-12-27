# collections.defaultdict
创建一个当key不存在时返回设定值的字典

````python
kg_indexs = collections.defaultdict( list )   
for h, r, t in kg_triples:  
    kg_indexs[ str( h ) ].append([ int( t ), int( r ) ])  
return kg_indexs
````

