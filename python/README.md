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