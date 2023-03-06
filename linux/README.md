<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-11 15:11:59
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-17 15:30:55
-->
# linux

- [git](./git.md)
- [并行](./并行.md)

## 传文件
- cp
- scp 
- rsync
参数解释
```
-a: 以archive模式操作，复制目录、符号连接，等价于 -rlptgoD 。
-v: 打印详情
-u: 只进行更新，防止本地新文件被重写，注意两者机器的时钟的同时
-P: 等同于两个参数 --partial --progress
--partial: 保留那些因故没有完全传输的文件,以是加快随后的再次传输(即断点续传)
--progress: 在传输过程中显示进度
-r: 对子目录以递归模式处理
-z: 在传输过程中进行压缩
-t, --times: 保留修改时间
--progress 展示进度条
```
示例：
```
rsync -avuP root@192.168.1.100:/opt/a.tar.gz ./a.tar.gz
```


例子
```sh
# 找到文件夹下面的所有带/pairs的文件名，并根据行数降序排序
find . -type f | grep '/pairs' |  xargs wc -l | awk -v FS="\t" '{print $0}' | sort -n -r -k 1

# 计算所有findSimilarPosPairLarge.py进程占的内存数，单位为KB，后面做一个补充，把/proc/pid/status的所有信息都做一个输出
ps -aux | grep 'findSimilarPosPairLarge.py' | sed '1d' | awk '{print $2}' | xargs -I {} grep 'VmRSS' /proc/{}/status | awk '{sum+=$2;} END{print sum" kb";}'

# 实时监控一个文件的行数并输出到log文件
watch 'wc -l record_CVCL_0291_negWithPosSimilarPair_0.4_0.5| xargs -I {} echo "{}   $(date +%T)" >> log'

交并补差
sort a.txt b.txt | uniq -d #交 将a.txt b.txt文件进行排序，uniq使得两个文件中的内容为唯一的，使用-d输出两个文件中次数大于1的内容，即是得到交集
sort a.txt b.txt | uniq #并 将a.txt b.txt文件进行排序，uniq使得两个文件中的内容为唯一的，即可得到两个文件的并集
sort a.txt b.txt b.txt | uniq -u #差 将两个文件排序，最后输出a.txt b.txt b.txt文件中只出现过一次的内容，因为有两个b.txt所以只会输出只在a.txt出现过一次的内容，即是a.txt-b.txt差集

```

## 挂载文件
windows 挂载nfs文件系统```mount xx.xx.xx.xx:/path  B:```,这个命令只能在cmd中运行，powershell和cygwin中用不了，希望后面可以用。

