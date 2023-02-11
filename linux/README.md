<!--
 * @Description: 
 * @version: 
 * @Author: wenyuhao
 * @Date: 2023-02-11 15:11:59
 * @LastEditors: wenyuhao
 * @LastEditTime: 2023-02-11 17:56:21
-->
# linux

## git
### 标准流程
1,新建一个仓库
```sh
mkdir bioInfo
git init
echo '# bioinformatics snippet' > bioInfo/README.md
git add bioInfo/README.md
git commit -m "add bioinfo"
git push -u origin main
```

### 添加配置
```
git config --global user.email "925201392@qq.com" #添加邮箱
git config --global user.name "wenyuhao"
git config --global url."https://hub.fastgit.xyz/".insteadOf https://github.com/  #由于国内限制
```

### 碰到的问题解决
1，如果改变了仓库，但是还没有commit，要恢复到pull之后的状态
先看看状态```git status```,
返回了状态
```
On branch main
Your branch is up to date with 'origin/main'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   bioInfo/README.md
```
恢复状态```git restore bioInfo/README.md```

1,远程分支和本地分支不一致。
如果本地分支没有变，远程分支改变了，则需要把远程分支pull到本地
```
git push -u origin main
```
origin是远程分支的名称，main是本地分支的名称


如果本地分支变化了，远程分支没有变化了，则需要把本地分支push到远程
```
git push -u origin main
```

如果本地分支和远程分支都变化了，则需要把远程分支和本地分支merge 远程分支没有变化了，则需要把本地分支push到远程
```
#两种方法
#1,直接pull，意味着先fetch 远程，再和本地分支merge
git pull origin master
git push -u origin main
#2, 先fetch 远程，再和本地分支merge
git fetch origin main
git merge origin/main
git push -u origin main
```