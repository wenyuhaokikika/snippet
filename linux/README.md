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
-P: 等同于 --partial --progress
--partial: 保留那些因故没有完全传输的文件,以是加快随后的再次传输(即断点续传)
--progress: 在传输过程中显示进度
-r: 对子目录以递归模式处理
-z: 在传输过程中进行压缩
-t, --times: 保留修改时间
```
示例：
```
rsync -avuP root@192.168.1.100:/opt/a.tar.gz ./a.tar.gz
```


例子
```sh
# 找到文件夹下面的所有带/pairs的文件名，并根据行数降序排序
find . -type f | grep '/pairs' |  xargs wc -l | awk -v FS="\t" '{print $0}' | sort -n -r -k 1
```