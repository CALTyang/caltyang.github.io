---
title: Geo常用数据格式
date: 2018-11-29 22:47:16
tags: 工作相关
---

[TOC]

| 格式 | 类型 |
|---|---|
| KML | |
| GeoJson | 矢量 |
| Shapefile | 矢量 |
| CAD | 矢量 |
| WKT | 矢量 |
| TIFF | 栅格 |
| JPEG、GIF、BMP、PNG | 栅格 |

<!--more-->
主要类型分为2类
* 矢量数据：通过语言来描述几何图形，故不会失帧
* 栅格数据：通过格点来存储数据，可能失帧

## Shapefile
全称 ESRI Shapefile，是一种基于文件方式存储GIS数据的被广泛使用的数据格式。
一个Shapefile文件至少由shp、dbf、shx三个文件组成
* .shp：包含图形数据
* .dbf：属性数据文件，实际为dbase的数据文件，内部为当前图层中每条数据的属性值
* .shx：索引文件
开源扩展后缀文件：
* .prj：坐标系描述文件
* .qix：QuadTree空间索引文件
* .fix：FeatureID索引
* .sld：StyleLayerDescriptor样式xml文件

ESRI扩展后缀文件：
* .sbn：属性索引
* .sbx：空间索引
* .lyr：ArcMap-Only样式对象
* .avl：ArcView样式对象
* .shp.xml：fgdc元数据


## GeoJSON
基于JSON数据格式用于表示空间实体的标记语言

## WKT、WKB
一种文本标记语言，用于表示矢量几何对象，二进制形式是WKB，由OGC制定，可以表示几何对象
* POINT
* LINESTRING
* PLOYGON
* MULTIPOINT
* MULTILINESTRING
* MULTIPOLYGON
* GEOMETRYCOLLECTION
* POINT ZM
* POINT M
* POINT EMPTY
* MULTIPOLYGON EMPTY

## 栅格数据（Raster）
常见栅格数据格式如下
* GeoTIFF：Geographic Tagged Image File Format，GIS和卫星遥感应用的行业图像标准文件（.tif或者.tiff）
* ERDASImagine:ERDAS软件的专用数据格式（.img）
* ENVI：ENVI使用一个二进制文件（.dat或者.bin），一个文本头文件存储栅格数据（.hdr）
* HDF：Hierarchical Data Format，美国国家高级计算机应用中心研发，MODIS数据就采用该格式，当前版本HDF5
* NetCDF：Network Common Data Format，由美国大学打钱研究协会研发，广泛应用于大气领域（.nc）NetCDF4基于HDF5

    
## Reference
* [WKT格式](https://blog.csdn.net/shixiaoguo90/article/details/33720167)


