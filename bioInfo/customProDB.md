<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-03-02 08:49:29
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-03-02 08:49:29
-->
# customProDB

API:https://bioconductor.org/packages/release/bioc/manuals/customProDB/man/customProDB.pdf

## 主要函数介绍
```R
library(customProDB)

aaVariation：变体可以分为SNV和INDEL。通过将 positionincoding() 的输出作为输入，aaVariation() 函数预测 SNV 在隐藏的转录本中的结果，例如同义或非同义。 Variations can be divided into SNVs and INDELs. By taking the output of positionincoding() as input, aaVariation() function predicts the consequences of SNVs in the harbored transcript, such as synonymous or non-synonymous.

Outputproseq:输出 FASTA 格式文件包含表达水平高于cutoff值的蛋白质 output FASTA format file contains proteins that have expression level above the cutoff

Outputaberrant:输出FASTA  protein，只能用于indel。 Short insertion/deletion may lead to aberrant proteins in cells. We provide a function to generate FASTA file containing this kind of protein.

OutputsharedPro：输出多个样本中高表达蛋白的序列， Output the sequences of proteins with high expressions in multiple samples

OutputVarprocodingseq：输入rpkm矩阵，输出在所有样本中共表达的蛋白序列文件 this function takes RPKM matrix as input, users can set two paramteters,cutoff and shared, to gen-erated a consensus expressed database。

OutputVarprocodingseq：根据snv文件输入，输出snv突变之后的蛋白序列，Output the variant(SNVs) protein coding sequences。

OutputVarproseq_single:也是产生一个突变后的序列，不同的是一个突变对应一个序列 Output the non-synonymous SNVs into FASTA file, one SNV per sequence。

Positionincoding：对于那些标有“编码”的变异，positionincoding() 函数计算每个转录本编码序列中变异的位置。 For those variations labeled with "Coding", positionincoding() function computes the position of variation in the coding sequence of each transcript。

```

## 使用vcf和参考基因组获得突变后的蛋白序列
customProDB有两个主要的函数处理
  1,OutputVarprocodingseq:如果你的突变是snp突变，使用OutputVarprocodingseq。
  2，如果你的突变是

总之呢，本来我只想拿到一个个体突变之后的蛋白，但是现在我要做两步并把他们最终的结果merge在一起，这就很烦。他的https://github.com/chambm/customProDB 上面关闭了issues，Bug Reports还是404，简直了，但是我目前除了customProDB，并没有找到类似功能的packages。

除了customProDB还有两种方法：
  ```
  1，vcftools：vcftools有子命令vcf-consensus，它可以使用参考基因组和突变数据vcf文件，得到突变后的基因组文件使用R packagesBiostrings:translate()将对应的cds区域的dna序列转化为蛋白序列。

  2，gatk 提供了从突变到序列的工具
  Gatk提供了子命令FastaAlternateReferenceMaker，功能和vcf-consensus，区别是vcftools文档中说vcf-consensus只能处理snp和较短的indel。
  得到突变后的基因组文
  ```


主要使用函数OutputVarprocodingseq
文档中给出的示例
```R
vcffile <- system.file("extdata/vcfs", "test1.vcf", package="customProDB")
vcf <- InputVcf(vcffile)
table(values(vcf[[1]])[['INDEL']])
index <- which(values(vcf[[1]])[['INDEL']] == FALSE)
SNVvcf <- vcf[[1]][index]
load(system.file("extdata/refseq", "exon_anno.RData",
package="customProDB"))
load(system.file("extdata/refseq", "dbsnpinCoding.RData",
package="customProDB"))
load(system.file("extdata/refseq", "procodingseq.RData",
package="customProDB"))
load(system.file("extdata/refseq", "ids.RData", package="customProDB"))
load(system.file("extdata/refseq", "proseq.RData", package="customProDB"))
postable_snv <- Positionincoding(SNVvcf, exon, dbsnpinCoding)
txlist <- unique(postable_snv[, 'txid'])
codingseq <- procodingseq[procodingseq[, 'tx_id'] %in% txlist, ]
mtab <- aaVariation (postable_snv, codingseq)
OutputVarprocodingseq(mtab, codingseq, ids, lablersid=TRUE)
```
在代码中可以看出来，主要包含四个数据框Exon,dbsnpinCoding,procodingseq,ids来注释。

这四个数据框可以根据customProDB提供的函数```1,PrepareAnnotationEnsembl 2,PrepareAnnotationRefseq```,从名字看出来，一个是以ensembl为开始的ens编号，另一种是以NM/R开头的ncbi编号，我们使用ensembl提供的编号。
```R
library(customProDB)
ensembl <- useMart("ENSEMBL_MART_ENSEMBL", dataset="hsapiens_gene_ensembl",host="https://sep2019.archive.ensembl.org", path="/biomart/martservice",archive=FALSE)
annotation_path ='annoPath'
PrepareAnnotationEnsembl(mart=ensembl, annotation_path=annotation_path,splice_matrix=FALSE, dbsnp=NULL,COSMIC=FALSE)
```
在文件夹annoPath下面会产生下面的文件,一般会网络超时错误，多下几次就好了。

```
$ ls annoPath 
exon_anno.RData  ids.RData  procodingseq.RData  proteinseq.RData  txdb.sqlite
```
加载vcf进行运算
```
library(customProDB)
vcfFile = '/data/wenyuhao/55/gencodegenes/GRCh38p13R41V98/all.vcf.gz'
vcf <- InputVcf(vcfFile)
length(vcf)
vcf = vcf[[1]]
```


```
load(file = "annoPath/exon_anno.RData")
load(file = "annoPath/ids.RData")
load(file = "annoPath/procodingseq.RData")
load(file = "annoPath/proteinseq.RData")

dbsnpinCodingGr = makeGRangesFromDataFrame(dbsnpinCoding)
postable <- Positionincoding(vcf, exon, dbsnpinCoding)
txlist <- unique(postable[, 'txid'])
codingseq <- procodingseq[procodingseq[, 'tx_id'] %in% txlist, ]
mtab <- aaVariation (postable, codingseq)
ouput = OutputVarprocodingseq(mtab, codingseq, ids, lablersid=TRUE)
write.table(a,'output',quote=F,col.name=T,row.names=F,sep='\t')

```
1，Exon


![exon1](https://user-images.githubusercontent.com/40107519/222306650-6f1e511e-0a16-450b-82a7-cdfcd35cfc8c.png)
![exon2](https://user-images.githubusercontent.com/40107519/222306705-efcca2e0-caa0-4dcb-9797-946d7ea3139e.png)
![exon3](https://user-images.githubusercontent.com/40107519/222306719-14637021-b444-43fb-8162-8bf7632efc30.png)

2,ids


![ids](https://user-images.githubusercontent.com/40107519/222306779-751f64e6-1e38-45da-bb59-44c0f8d0002e.png)

3,dbsnpinCoding


![dbsnpinCoding](https://user-images.githubusercontent.com/40107519/222306861-f98cade8-a24d-4d26-9c4c-1659e73486eb.png)

4,procodingseq


![procodingseq](https://user-images.githubusercontent.com/40107519/222306898-6d963d3f-c274-445a-a8a4-84d1c5f78c07.png)

下面根据人类基因组序列fasta和基因组注释gff3文件，将上面4个数据框照猫画虎即可。

最终代码
```R
library(customProDB)
vcf <- InputVcf('/data/wenyuhao/tmp/runWGS/tmp/results/annotated/all.vcf.gz')
length(vcf)

index <- which(values(vcf[[1]])[['INDEL']] == FALSE)
SNVvcf <- vcf[[1]][index]

ids = read.table(file= "/data/wenyuhao/TTT/ids", header = TRUE,sep='\t')
exon = read.table(file= "/data/wenyuhao/TTT/exon", header = TRUE,sep='\t')
dbsnpinCoding = read.table(file= "/data/wenyuhao/TTT/dbsnpinCoding", header = TRUE,sep='\t')
procodingseq = read.table(file= "/data/wenyuhao/TTT/procodingseq", header = TRUE,sep='\t')


postable_snv <- Positionincoding(SNVvcf, exon, dbsnpinCoding)
txlist <- unique(postable_snv[, 'txid'])
codingseq <- procodingseq[procodingseq[, 'tx_id'] %in% txlist, ]
mtab <- aaVariation (postable_snv, codingseq)
ouput = OutputVarprocodingseq(mtab, codingseq, ids, lablersid=TRUE)
write.table(a,'output',quote=F,col.name=T,row.names=F,sep='\t')
```
