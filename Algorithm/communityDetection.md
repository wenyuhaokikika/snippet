<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-03-10 09:19:24
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-03-10 13:38:04
-->
```cw = sppiB.community_walktrap(steps=4)```得到dendrogram对象,再使用```as_clustering```将dendrogram转化为VertexClustering

如果转化为VertexClustering时报错，一般原因是图没有连通，使用下面的函数处理```fix_dendrogram(sppiB, cw)```
```sppiB.modularity(cw.as_clustering(n))```
```python
#https://github.com/igraph/python-igraph/issues/17
#https://lists.nongnu.org/archive/html/igraph-help/2014-02/msg00067.html
#https://github.com/igraph/igraph/issues/2042
#https://github.com/igraph/igraph/issues/675
def fix_dendrogram(graph, cl):
    already_merged = set()
    for merge in cl.merges:
        already_merged.update(merge)

    num_dendrogram_nodes = graph.vcount() + len(cl.merges)
    not_merged_yet = sorted(set(range(num_dendrogram_nodes)) - already_merged)
    if len(not_merged_yet) < 2:
        return

    v1, v2 = not_merged_yet[:2]
    cl._merges.append((v1, v2))
    del not_merged_yet[:2]

    missing_nodes = range(num_dendrogram_nodes,
            num_dendrogram_nodes + len(not_merged_yet))
    cl._merges.extend(zip(not_merged_yet, missing_nodes))
    cl._nmerges = graph.vcount()-1
```