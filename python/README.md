<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-12 11:11:27
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-12 22:02:38
-->
# python

## 读取文件

读取tar.gz文件
```python
tar = tarfile.open("/data/wenyuhao/interactomeinsider/interactome3D/proteins_327.tgz","r:gz")
[member.name for member in tar.getmembers()]

for member in tar.getmembers():
    with tar.extractfile(member) as f:
        for l in f:
            print(l)
            break
```

xml转json
```python
with gzip.open('/data/wenyuhao/55/CCLE/paper/pubmed21n0001.xml.gz','r') as f:
    gg=xmltodict.parse(f.read())
```

## tqdm
两层tqdm展示进度
```python
#https://stackoverflow.com/questions/45742888/using-multiple-bars
with tqdm(neg.iterrows(), desc='Outter',position=0) as outter_range:
    for i,r in outter_range:
        leave = i == neg.shape[0]
        for i1,r1 in tqdm(pos.iterrows(), desc='Inner',position=1,leave=leave):
            time.sleep(.3)
```

# Anaconda
1,新建环境
```sh
conda create --name myenv
conda create -n myenv python=3.9
conda create -n myenv scipy
conda create -n myenv scipy=0.17.3
conda create -n myenv python=3.9 scipy=0.17.3 astroid babel

conda env create -f environment.yml
conda env list

conda create --prefix ./envs jupyterlab=3.2 matplotlib=3.5 numpy=1.21 #在本地创建conda的环境

conda env update --prefix ./env --file environment.yml  --prune # update
conda create --name myclone --clone myenv  #clone env 克隆一份本地的环境

```
