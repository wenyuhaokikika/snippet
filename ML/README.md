<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-11 17:59:22
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-11 17:59:23
-->
# 传统的机器学习
**主要是sklearn 相关的代码**

## config设置类
```
np.set_printoptions(suppress=True)#numpy不使用科学计数法

```

输出混淆矩阵
```python
label = [0,0,1,0,1,0,0,1]
pred = [0,0,0,0,0,1,1,1]
from sklearn.metrics import confusion_matrix
tn, fp, fn, tp = confusion_matrix(label, pred).ravel()
```