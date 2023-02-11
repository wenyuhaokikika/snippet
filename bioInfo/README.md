# bioinformatics snippet

## Tabix
### 命令行
```shell
#下载人类hg38的gff注释文件
wget http://ftp.ensembl.org/pub/current_gtf/homo_sapiens/Homo_sapiens.GRCh38.103.gtf.gz
mkdir tabix
#对gff文件排序并压缩
(grep ^"#" Homo_sapiens.GRCh38.94.gtf; grep -v ^"#" Homo_sapiens.GRCh38.94.gtf | sort -k1,1 -k4,4n) | bgzip > sorted.gtf.gz
#建立索引
tabix -p gff sorted.gtf.gz
#文件夹下多了索引文件sorted.gtf.gz.tbi
#查询
tabix sorted.gtf.gz 11:700235-702958
```

### python 接口
```python
import tabix
#ffc解析gtf文件最后一列的函数
ffc=lambda x:{i[0]:i[1] for i in [i.split(' ') for i in x.replace('"','').replace('; ',';').split(';')] if len(i)==2}
def queryGene1(row):
    tb = tabix.open('/data/wenyuhao/interactomeinsider/tabix/sorted.gtf.gz')
    cHr,start,end=row[0].replace('chr',''),str(row[1]),str(row[2])
    records = tb.query(cHr,int(start),int(end))
    #records=[(deal(i[-1])['gene_name'],deal(i[-1])['gene_id']) for i in records if i[2]=='exon']
    return pd.DataFrame(list(records))#set(records)
```
