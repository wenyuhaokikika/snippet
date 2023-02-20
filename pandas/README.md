<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-11 14:22:46
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-11 15:03:51
-->
# pandas

## 常用功能

### 读取数据
#### 参数意义
pd.read_csv/pd.read_table
```
filepath_or_buffer: str, path object or file-like object,输入文件地址或者文件句柄，文件地址支持，本地储存，ftp和http，如果prefix为gz等压缩文件，则读取为压缩文件无需解压，或者在compression参数中指定压缩类型。
header: int, list of int, None, default ‘infer’,设置header 默认第一行为columns，设置为None则是数字为columns
comment: str, optional  忽略以"comment"为打头的行，比如comment ="#"，则忽略#开头的行，在读取gtf和vcf文件中可以用到。
low_memory: bool, default True，一般读取大文件时应该设置这个参数为False。
iterator: bool, default False，返回迭代器。
chunksize: int,设置每个chunk的大小，iterator或者chunksize设置都会返回一个迭代器，一般读取大文件会用。
mode：读写文件的模式，如'w+','a+'，如果设置为'a+'，则可以向同一个文件多次写入并叠加。
```
pd.read_excel/

#### Example
1，返回迭代器后不可以使用groupby等参数，必须自行考虑每个chunk的处理方式
```python
# 对大文件迭代写入小文件
import shutil
from collections import defaultdict
from loguru import logger
import pandas as pd
import os
root = '/data/wenyuhao/55/data/PPI/IR/cellpairs'
pairs = pd.read_table('/data/wenyuhao/55/data/PPI/IR/pairs.txt',low_memory=False,chunksize=1000000)
def clean(d):
    if os.path.exists(d):
        shutil.rmtree(d)
    os.makedirs(d)
    return False
panD = defaultdict(lambda:True, {})#defaultdict(bool)
chunkNum = 0
for d in pairs:
    chunkNum+=1
    for cell,df in d.groupby('RRID'):
        d = os.path.join(root,cell)
        if panD[d]:
            panD[d]=clean(d)
        df.to_csv(os.path.join(d,'pairs'),sep='\t',index=False,mode='a+',header=False)
        logger.info("cellLine:"+cell+'\tchunkNum:'+str(chunkNum))
```

2，数据框迭代输出
使用python的迭代器输出
pandas得到数据框的迭代器：
```
def iterDf(df,par):
    step=int(df.shape[0]/par)+1
    for i in range(0,df.shape[0],step):
        yield df.iloc[i:i+step,:]
```

读取数据框带有引号的数据
```python
pd.read_csv(f'/data/wenyuhao/55/data/GO/tmp/GOF/res{i}.csv',quotechar='"',
                   skipinitialspace=True,escapechar='\\')
```
读取压缩文件
从xena下载数据
```python
snv=pd.read_csv('https://gdc-hub.s3.us-east-1.amazonaws.com/download/TCGA-LUAD.mutect2_snv.tsv.gz',sep='\t',compression='gzip')
```

### pandas 一行分多行
```python
#两种方法 explode和使用join
df.explode(s)#s是个list最小单元的series

tmp=hint['pmid:method:quality'].str.split('|').apply(lambda x:",".join([i.split(':')[1] for i in x]))
hint=hint.join(tmp.str.split(',',expand=True).stack().reset_index(level=1, drop=True).rename('psi-mi'))
varients=varients.join(varients['effect'].str.split(',',expand=True).stack().reset_index(level=1, drop=True). rename('Effect'))
```

### 多行合并一行list
```goa=goa.groupby([4])[2].apply(list).to_frame().reset_index()```

### 根据某一列的最大（小）值去重某一列
```python
pd.concat([df.sort_values('numSpectraProteinObserved',ascending=False).iloc[:1,:] for k,df in d31.groupby('geneSymbol')])
```

## apply
### 给apply添加进度条
```python
from tqdm._tqdm_notebook import tqdm_notebook
import pandas as pd
tqdm_notebook.pandas()
df = pd.DataFrame(np.random.randint(0, int(1e8), (10000, 1000)))
df.groupby(0).progress_apply(lambda x: x**2)
```

### 用joblib写pandas的apply并行：
ref:https://github.com/joblib/joblib
```
from joblib import Parallel, delayed
def proxyCount(df,ref):#ref = pathwayDf['catalysis-precedes']
    return df.apply(ccountPath,axis=1,args=(ref,))
result = Parallel(n_jobs=20,prefer='processes')(delayed(proxyCount)(i,\
                         pathwayDf['controls-transport-of']) for i in iterDf(ppi,20))
ppi['controls-transport-of']=pd.concat(result)
```

### 用pandarallel 处理并行
ref:https://github.com/nalepae/pandarallel
```
from pandarallel import pandarallel
pandarallel.initialize(nb_workers=23,progress_bar=True)
ppi['OR']=ppi.parallel_apply(lambda x:calOR(x['gene1'],x['gene2']) if (x['gene1'] in tcgaGenes)\
                             &(x['gene2'] in tcgaGenes) else 0,axis=1)
```

### 根据列名去重
```cellLineExp=cellLineExp.loc[:,~cellLineExp.columns.duplicated()]```



