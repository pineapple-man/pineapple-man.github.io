---
title: pip
toc: true
clearReading: true
thumbnailImagePosition: right
metaAlignment: center
categories: Python
tags: Python
keywords: pip
excerpt: 本文记录平时在使用 pip 时遇到的问题
date: 2021-12-07 09:42:58
thumbnailImage:
---
<!-- toc -->


## Retrying (Retry(total=4, connect=None, read=None, redirect=None, status=None))
https://blog.csdn.net/qq_25964837/article/details/80295041

## WARNING: Ignoring invalid distribution -xpython
https://pythonmana.com/2021/05/20210519215130131G.html

## ModuleNotFoundError: No module named 'pip'
https://blog.csdn.net/wwangfabei1989/article/details/80107147

## 修改 pip 源为国内源
https://zhuanlan.zhihu.com/p/109939711

## 升级 pip 所有包
pip 当前内建命令并不支持升级所有已安装的Python模块，可以使用另一种这种的方式进行[^1]
```python
pip list 			#列出当前安装的包
pip list --outdata 	#列出可升级的包
pip install --upgrade requests  # 升级一个包
# 升级所有可升级的包
pip freeze --local | grep -v '^-e' | cut -d = -f 1  | xargs -n1 pip install -U
```

## 附录
[^1]:https://blog.csdn.net/kl28978113/article/details/77980778 