<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-27 00:14:45
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-27 00:17:50
-->
snakemake --cores all

# 命令行选项

1，--wrapper-prefix # 不使用网络版本的wrapper
一般git clone到本地，例子```--wrapper-prefix file:///data/wenyuhao/tmp/runWGS/tmp/dna-seq-gatk-variant-calling/```,在dna-seq-gatk-variant-calling目录下```0.59.0,0.74.0```这种不同版本的wrapper，在smk文件中表示为:
```smk
    wrapper:
        "0.74.0/bio/vep/annotate"
```

2，--unlock
如果运行一般被结束了，snakemake会把文件lock，下次运行时先添加--unlock将文件取锁，再运行

3，--ignore-incomplete
运行的时候ctrl c了，会导致两次跑的文件的时间戳不相同，加入这个命令会使snakemake忽略时间戳。

