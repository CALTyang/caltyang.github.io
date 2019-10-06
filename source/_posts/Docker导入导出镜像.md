---
title: Docker导入到处镜像
date: 2019-06-14 22:18:23
tags: Docker
---

[TOC]

## 导出镜像
命令
```bash
docker save -o <path>/1.tar demo/java:1
```

## 导入镜像
命令
```bash
docker load -i <path>/1.tar
```

## Reference
* [Docker镜像的导入导出](https://blog.csdn.net/ncdx111/article/details/79878098)