---
title: JupyterNotebook上手
date: 2019-06-19 22:42:41
tags: Python
---

[TOC]

运行使用命令
```bash
jupyter notebook
```
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20190619/jn-overview.png)

<!--more-->
## 魔法函数
有如下类别

| 名称 | 含义 |
|---|---|
| % | 行魔法函数，只对本行代码生效 |
| %% | Cell魔法函数，在整个Cell中生效，必须放在Cell首行 |
| %lsmagic | 列出所有魔法函数 |
| %magic | 查看各个魔法函数的说明 |
| ?<func_name> | 查看该函数的说明 |

常用魔法函数

| 魔法函数 | 作用 |
|---|---|
| %%writefile | 写入文件 |
| %run | 调用外部python脚本 |
| %timeit | 测试单行语句的执行时间 |
| %%timeit | 测试整个单元中代码的执行时间 |
| %matplotlib | 显示matplotlib包生成的图形 |
| %pdb | 调试程序 |
| %pwd | 查看当前工作目录 |
| %ls | 查看目录文件列表 |
| %reset | 清除全部变量 |
| %who | 查看所有全局变量的名称，若给定类型参数，只返回该类型的变量列表 |
| %whos | 显示所有的全局变量名称、类型、值/信息 |
| %xmode Plain | 设置为当异常发生时只展示简单的异常信息 |
| %xmode Verbose | 设置为当异常发生时展示详细的异常信息 |
| %debug | bug调试，输入quit退出调试 |
| %env | 列出全部环境变量 |

## 一些问题

## Tip
### 使用SHELL
可以使用!<shell_command>来执行命令

### 换主题
```bash
# 安装插件
pip install jupyterthemes
# 查看所有主题
jt -l
# 选择主题
jt -t <theme_name>
# 切换为原来的主题
jt -r
```

### 插件
代码自动补全[参考hintland](https://www.jianshu.com/p/0ab80f63af8a)

### 嵌入PDF
代码示例
```python
from IPython.display import IFrame
IFrame('https://arxiv.org/pdf/1406.2661.pdf', width=800, height=450)
```

### 格式化打印Numpy矩阵
代码
```python
from IPython.display import display,Latex,Math
%matplotlib inline

from IPython.core.interactiveshell import InteractiveShell
sh = InteractiveShell.instance()

def number_to_str(n,cut=5):
    ns=str(n)
    format_='{0:.'+str(cut)+'f}'
    if 'e' in ns or ('.' in ns and len(ns)>cut+1):
        return format_.format(n)
    else:
        return str(n)

def matrix_to_latex(mat,style='bmatrix'):
    if type(mat)==np.matrixlib.defmatrix.matrix:
        mat=mat.A
    head=r'\begin{'+style+'}'
    tail=r'\end{'+style+'}'
    if len(mat.shape)==1:
        body=r'\\'.join([str(el) for el in mat])
        return head+body+tail
    elif len(mat.shape)==2:
        lines=[]
        for row in mat:
            lines.append('&'.join([number_to_str(el)  for el in row])+r'\\')
        s=head+' '.join(lines)+tail
        return s
    return None

sh.display_formatter.formatters['text/latex'].type_printers[np.ndarray]=matrix_to_latex
```
参考: [在jupyter notebook中美观显示矩阵](https://zhuanlan.zhihu.com/p/20855276)

### 指定绘图输出矢量图
配置代码示例
```python
import matplotlib
import matplotlib.pyplot as plt
%matplotlib inline
%config InlineBackend.figure_format = 'svg'
```

### nbextensions
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20190619/nbextension.png)

安装
```bash
conda install -c conda-forge jupyter_contrib_nbextensions
# 好像可以不用安装，自带已经有图形配置界面
conda install -c conda-forge jupyter_nbextensions_configurator
# 用途不详，enable可以手工开启插件，但是最好使用图形界面配置
jupyter contrib nbextension install --user
jupyter nbextension enable execute_time/ExecuteTime
```

#### Snippets
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20190619/jn-snippets.png)


## Reference
* [最详尽使用指南：超快上手Jupyter Notebook](https://zhuanlan.zhihu.com/p/32320214)
* [Jupyter Notebook 有哪些奇技淫巧？](https://www.zhihu.com/question/266988943/answer/632279672)
* [我知道你会用Jupyter Notebook，但这些插件你都会了吗？](https://zhuanlan.zhihu.com/p/52890101)
* [让你编程效率倍增的5个 jupyter notebook 扩展工具](https://zhuanlan.zhihu.com/p/36389916)
* [如何优雅地使用 Jupyter？](https://www.zhihu.com/question/59392251)
* [如何通过Jupyter Notebooks扩展提高效率](https://zhuanlan.zhihu.com/p/58798200)

