<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-12 12:27:45
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-15 08:41:38
-->
**import**
```
import matplotlib.pyplot as plt
import seaborn as sns
sns.set(font_scale=2)
import matplotlib as mpl
mpl.rcParams['font.sans-serif'] = ['KaiTi']
mpl.rcParams['font.serif'] = ['KaiTi']
plt.rc('font', family='KaiTi', size=25)
```

reference
```
https://stackoverflow.com/questions/43214978/how-to-display-custom-values-on-a-bar-plot
```

# seaborn
## 全局设置
```python
import matplotlib.pyplot as plt
import seaborn as sns
sns.set(font_scale=2)
plt.rcParams['font.sans-serif']=['SimHei'] #用来正常显示中文标签
plt.rcParams['axes.unicode_minus']=False #用来正常显示负号
```
## seaborn基础用法
```
f, (ax1, ax2) = plt.subplots(2, 2,figsize = (25,6), sharey=False)#
g = sns.heatmap(df1, annot=False,cmap="crest",ax=ax1[0])
```
函数解释一下
```
1,plt.subplots:figsize画布大小，nrows, ncol行数和列数，sharex, sharey子图的横纵坐标是否对齐，squeeze返回的ax对象为np数组，默认True，
height_ratios，width_ratios可以定义行的高度和列的宽度。


```

- [ML](./ML.md)

常用代码
- 画4张图
```python
f, (ax1, ax2) = plt.subplots(2, 2,figsize = (25,6), sharey=False)
g = sns.heatmap(df1, annot=False,cmap="crest",ax=ax1[0])
plt.setp(ax1[0].get_xticklabels(), rotation=45, ha='right')
ax1[0].title.set_text('shared_jaccard')
#ax1[0].set_ylim(tmp['degree'].min(),tmp['degree'].max())
ax1[0].set_xticks([])
ax1[0].set_xlabel('')

g = sns.heatmap(df2, annot=False,cmap="crest",ax=ax1[1])
plt.setp(ax1[1].get_xticklabels(), rotation=45, ha='right')
ax1[1].title.set_text('jaccard')
#ax1[1].set_ylim(0,1)
ax1[1].set_xticks([])
ax1[1].set_yticks([])
ax1[1].set_xlabel('')
ax1[1].set_ylabel('')

g = sns.heatmap(df3, annot=False,cmap="crest",ax=ax2[0])
plt.setp(ax2[0].get_xticklabels(), rotation=45, ha='right')
ax2[0].title.set_text('shared_edges')
#ax2[0].set_ylim(0,1)

g = sns.heatmap(df4, annot=False,cmap="crest",ax=ax2[1])
plt.setp(ax2[1].get_xticklabels(), rotation=45, ha='right')
ax2[1].title.set_text('shared_nodes')
ax2[1].set_yticks([])
ax2[1].set_ylabel('')
#ax2[1].set_ylim(tmp['personalized_pagerank'].min(),tmp['personalized_pagerank'].max())

f.suptitle('细胞系之间的全图网络比较', fontsize=25)
plt.show()
```
