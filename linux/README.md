<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-11 15:11:59
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-12 23:50:55
-->
# linux

- [git](./git.md)
- [并行](./并行.md)

例子
```sh
# 找到文件夹下面的所有带/pairs的文件名，并根据行数降序排序
find . -type f | grep '/pairs' |  xargs wc -l | awk -v FS="\t" '{print $0}' | sort -n -r -k 1

# 计算所有findSimilarPosPairLarge.py进程占的内存数，单位为KB，后面做一个补充，把/proc/pid/status的所有信息都做一个输出
ps -aux | grep 'findSimilarPosPairLarge.py' | sed '1d' | awk '{print $2}' | xargs -I {} grep 'VmRSS' /proc/{}/status | awk '{sum+=$2;} END{print sum" kb";}'
```
