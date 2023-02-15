<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-12 12:27:45
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-15 08:41:38
-->

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

