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
## 鸭子类型和猴子补丁
鸭子类型是一种动态类型的概念，指当一个对象具有特定的方法和属性时，我们可以视其为该类型的对象，而无需考虑其真实的类型。这类似于鸭子测试，即如果它走起来像鸭子、叫起来像鸭子，那么它就是鸭子。

在鸭子类型中，我们并不需要显式地指定对象的类型，只需要关注对象是否有我们需要的方法和属性。这种方式使得代码更加灵活，可以处理各种类型的对象，而不仅仅局限于某个特定类型。同时，鸭子类型也增加了代码的可复用性和可扩展性，因为我们不需要关注对象的具体类型，只需要关注它所需要的属性和方法。

一个经典的例子是Python中的迭代器。Python中的迭代器对象并不要求继承某个特定的类或者实现特定的接口，只需要实现next()方法即可。因此，如果我们定义了一个对象，它具有next()方法，那么它就可以被视为一个迭代器对象，而无需显式地指定它的类型。这就是鸭子类型的思想。
鸭子类型和猴子补丁的例子
```python
class Duck:
    def quack(self):
        print("Quack!")
class Person:
    def quack(self):
        print("I'm quacking like a duck!")
def make_it_quack(thing):
    thing.quack()
def monkey_patch_dog():
    def new_bark(self):
        print("Meow!")
    Dog.bark = new_bark
```
使用的实例：在训练模型的时候我们有很多模型训练，不可能对于每一个model都写一个forward的过程，实际上我们应该对于所有的model定义有forward，这样无论model的class怎么变化，训练过程是不变的。

## 上下文管理
在Python中，上下文管理器（context manager）是一种对象，它定义了两个方法__enter__()和__exit__()，并且可以使用with语句来包装一个代码块。with语句会在代码块执行前调用__enter__()方法，当代码块执行完成后，无论执行是否成功，都会调用__exit__()方法。这样可以确保在进入和退出代码块时进行一些操作，比如资源的获取和释放。

python中上下文经常用到，如open file,torch.no_grad.

下面介绍如何定义一个上下文。

碰到的问题：使用一些很老的库，他会在运行过程中print一些东西，想要使这些东西不输出，比如python-igraph计算两个graph的union函数，这个函数计算两个图的并图，过程中会输出并的图。
```python
import sys
import os
from loguru import logger
class HiddenPrints:
	def __enter__(self):
		self._original_stdout = sys.stdout
		sys.stdout = open(os.devnull, 'w')
	def __exit__(self, exc_type, exc_val, exc_tb):
		sys.stdout.close()
		sys.stdout = self._original_stdout
	#@functools.wraps(self)
	def __call__(self,func):
		@functools.wraps(func)
		def wrapper(*args, **kwargs):
			with HiddenPrints():
				return func(*args, **kwargs)
		return wrapper
#测试，只有logger.info打印了
with HiddenPrints():
	print(123)
	logger.info(123)
@HiddenPrints()
def echo():
    print(123)
```
思考：这只是一个简单的上下文，但是其他上下文都有非常多的用法，如with open () as f,torch.no_grad还可以作为装饰器使用，这都是怎么实现的呢？

## biopython
写入写出
```python
from Bio import SeqIO
from Bio.Seq import Seq
from Bio.SeqRecord import SeqRecord
[(seq_record.id,seq_record) for seq_record in SeqIO.parse("test.fasta", "fasta")]
SeqIO.write([SeqRecord(Seq(r['sequence']),id=r['UniqueIdentifier'],description=r['ProteinName'],name=r['EntryName']) for i,r in oncoP.iterrows()]
            , "/data/wenyuhao/55/data/PPI/IR/oncoppi/proteins.fa", "fasta")
```

# Anaconda
1,新建环境
```sh
conda create --name myenv
conda create -n myenv python=3.9
conda create -n myenv scipy
conda create -n myenv scipy=0.17.3
conda create -n myenv python=3.9 scipy=0.17.3 astroid babel

conda env create -f environment.yml
conda env list

conda create --prefix ./envs jupyterlab=3.2 matplotlib=3.5 numpy=1.21 #在本地创建conda的环境

conda env update --prefix ./env --file environment.yml  --prune # update
conda create --name myclone --clone myenv  #clone env 克隆一份本地的环境

#导出环境
conda env export --name machine-learning-env --file environment.yml
```

## jupyter
安装kernel
```sh
python -m ipykernel install --name graphistry --display-name "graphistry" --user
pip3 install ipykernel

```
