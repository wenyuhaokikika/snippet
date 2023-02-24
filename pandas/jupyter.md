<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-19 20:38:18
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-19 20:39:19
-->
# jypyter

## Magic 
### %store
%store 命令可以让你在两个不同的 notebook 中传递变量。
```
data = 'this is the string I want to pass to different notebook'
%store data
del data # This has deleted the variable
Stored 'data' (str)
```
现在，在另外一个 notebook 中...
```
%store -r data
print(data)
this is the string I want to pass to different notebook
```

## show
### 展示多个dataframe
```python
from pydataset import data
```