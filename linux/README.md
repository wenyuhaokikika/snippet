<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-11 15:11:59
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-03-06 13:35:04
-->
- [linux](#linux)
	- [自定义一些命令行函数](#自定义一些命令行函数)
	- [传输文件](#传输文件)
	- [挂载文件](#挂载文件)
	- [翻墙](#翻墙)
	- [进程守护](#进程守护)
	- [处理文件的一些脚本](#处理文件的一些脚本)

# linux

- [git](./git.md)
- [并行](./并行.md)


## 自定义一些命令行函数
```sh
cs() {cd "${1}";ls;}  #cd & ls
cl() {printf "\033c"} #清空屏幕
full() {echo `pwd`"/$1"} #输出文件的绝对路径
server(){/sbin/ifconfig  | grep 10.20 | sed 's/netmask.*//g;s/inet//g' | sed -e 's/[[:space:]]*$//' | sed -e 's/^[[:space:]]*//'} #查看服务器IP
sdms(){curl https://sctapi.ftqq.com/SCT177539TQu99ZcZ74QSVReEXeJHEDxry.send\?title\=GPUserver\&desp\=$1} #给收集客户端发送一个信息
```
这些函数写在~/.bashrc或者~/.zshrc中。

## 传输文件
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
```
示例：
```
rsync -avuP root@192.168.1.100:/opt/a.tar.gz ./a.tar.gz
```

## 挂载文件
windows 挂载nfs文件系统```mount xx.xx.xx.xx:/path  B:```,这个命令只能在cmd中运行，powershell和cygwin中用不了，希望后面可以用。

linux挂载smb```sudo mount -t cifs //xx.xxx.xxx.xx/path /mnt/cifs52disk -o username=jon,password=password,acl,dir_mode=0777,file_mode=077```

linux挂载nfs```mount -t nfs xx.xx.xx.xx:/path /path```

开机自动挂载，打开```/etc/fstab```，添加下面几行。
```sh
xx.xx.xxx.xx:/path     path                   nfs     defaults        0 0   #挂载nfs
//xxx.xxx.xx.xx/path     /mnt/cifs52disk                    cifs     defaults,username=xxxx,password="xxxxxx"        0 0 #挂载smb
```

## 翻墙
linux有v2ray，但是不能切换线路，之前试过[clash-dashboard](https://github.com/Dreamacro/clash-dashboard)和[clash-for-linux](https://github.com/wanhebin/clash-for-linux)但是都会有不同的bug，不好使，我想只要局域网中有一个代理，其他的走他的代理就行，局域网中的windows电脑就是个代理。
在windows客户端，点击参数设置-->v2ray设置-->允许来自局域网的连接
![image.png](https://upload-images.jianshu.io/upload_images/13330053-a7b8e5a2d7cb2f98.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


再查看内网中的windows的IP地址，用ipconfig查看
在linux客户端输入
```sh
export http_proxy="socks5://10.20.213.56:10808"
export https_proxy="socks5://10.20.213.56:10808"
```
如果不想走代理，```unset http_proxy;unset https_proxy```.

注：成功与否用```curl https://www.google.com/```,不要使用ping和wget，ping走的是icmp协议，wget不支持sock协议的代理。

## 进程守护
- nohup:一般nohup直接挂上去1,```nohup command &```,2,```nohup command > /dev/null &```不输出，3,```nohup command > output 2>&1 & ```输出到output文件.
- supervisor
- screen

## 使用nfs在内网中搭建共享文件夹

## 使用frp搭建内网穿透

## 定时任务
crontab可以设定定时任务，su到某个用户下使用```crontab -e```,打开一个类似vim的编辑器，就可以设定定时任务了。
Crontab 表达式规则：
```
*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 6) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12)
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```
注：
 - Linux中没有second，最小是miniute。
 - crontab不支持两个命令写在一行中的，比如```cd && ls```在shell中可以运行在crontab中不可以，最好的方法是写到一个run.sh脚本中，运行这个脚本.

例子：
```
30 21 * * * #表示每晚21:30分执
0 0 1-7 * 6 #每个月的第一个周六
0 12 1-7 * * #每个月的第一个星期天
@reboot #每次重启之后
```
## centos开启防火墙
```sh
firewall-cmd --list-ports#查看所有打开的端口
firewall-cmd --add-port=23333/tcp#开启端口
firewall-cmd --permanent --add-port=23333/tcp#永久开启端口
firewall-cmd --reload#重新reload规则
firewall-cmd    --state#查看防火墙的规则
#停止运行
systemctl stop firewalld
systemctl mask firewalld
#如果打开之后服务依然不能访问，大概率是缓存问题，清除缓存。
```

## 进程的持续和挂起
[reptyr](https://github.com/nelhage/reptyr)有解释到进程的一些用法
```sh
* Start a long running process, e.g. `top`
* Background the process with CTRL-Z
* Resume the process in the background: `bg`
* Display your running background jobs with `jobs -l`, this should look like this:
  * `[1]+  4711 Stopped (signal)        top`
  * (The `-l` in `jobs -l` makes sure you'll get the PID)
* Disown the jobs from the current parent with `disown top`. After that, `jobs` will not show the job any more, but `ps -a` will.
* Start your terminal multiplexer of choice, e.g. `tmux`
* Reattach to the backgrounded process: `reptyr 4711`
* Detach your terminal multiplexer (e.g. CTRL-A D) and close ssh
* Reconnect ssh, attach to your multiplexer (e.g. `tmux attach`), rejoice!
```
centos上安装```yum install reptyr```,发现安装的0.5版本，实际上0.9版本都已经出来了，添加res都不管用，直接下载rpm文件，使用rpm安装
```sh
wget https://download-ib01.fedoraproject.org/pub/epel/8/Everything/x86_64/Packages/r/reptyr-0.7.0-1.el8.x86_64.rpm
rpm -i reptyr-0.7.0-1.el8.x86_64.rpm
reptyr -v  #0.7.0
```
使用案例，我有一个rsync进程在传输文件，我想关电脑走人了，但是我不想kill再一次传输，所以我想找个screen把它挂起来。
首先ctrl+z将任务，他会返回一个pid，是rsync的pid，使用```screen -S rsync```新建一个screen，再用reptyr 20296将session接过来。


## 处理文件的一些脚本
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


