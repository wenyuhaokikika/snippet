<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-17 16:36:15
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-17 16:36:16
-->
# bioinformatics snippet

## biopython
写入写出fasta文件
```python
from Bio import SeqIO
from Bio.SeqRecord import SeqRecord

#读取文件
h = [seq_record for seq_record in SeqIO.parse("/data/wenyuhao/55/uniprot/uniprot_sprot.fasta", "fasta")]
#读取压缩文件
import gzip
h = [seq_record for seq_record in SeqIO.parse(gzip.open("/data/wenyuhao/55/uniprot/uniprot_sprot.fasta.gz", "rt"), "fasta")]
#写入文件
sequences = [SeqRecord(Seq(r['sequence']),id="sp|"+r['uniprotAC']+"-"+r['featureId'],
           description=r['uniprotAC']+"-"+r['featureId']+" "+r['description'],**{k:r[k] for k in ['GN','DB','OS','OX']}) for i,r in pepi.iterrows()]
with open("example.fasta", "w") as output_handle:
    SeqIO.write(sequences, output_handle, "fasta")
```

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

### 使用subprocess
subprocess查询某个位置的基因：
```
from subprocess import Popen, PIPE
def queryGene(row):
    cHr,start,end=row[0].replace('chr',''),str(row[1]),str(row[2])
    query = '{}:{}-{}'.format(cHr,start,end)
    process = Popen(['tabix', '-f', '/data/wenyuhao/interactomeinsider/tabix/sorted.gtf.gz', query], stdout=PIPE)
    res=pd.read_csv(process.stdout,sep='\t',header=None)
    scc=res[res[2]=='exon'][8].apply(deal)
    return {'gene':set(scc.apply(lambda x:x['gene_name']).to_list())
            ,'ENSG':set(scc.apply(lambda x:x['gene_id']).to_list())}
```

查看某个基因名对应的ENSG编号```grep -n 'GLIS2' /data/wenyuhao/downloadTCGA/Homo_sapiens.GRCh38.94.gtf | awk '{print $10}' | uniq```
