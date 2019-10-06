---
title: GeoJSON学习笔记
date: 2019-05-21 22:09:14
tags: 工作相关
---


[TOC]

基本结构类似

![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20190521/geojson-basic-structure.png)

<!--more-->
GeoJSON是一种对各种地理数据结构进行编码的格式。支持类型如下:

| 类型 | Type值 |
|---|---|
| 点 | Point |
| 线 | LineString |
| 面 | Polygon |
| 多点 | MultiPoint |
| 多线 | MultiLineString |
| 多面 | MultiPolygon |
| 几何集合 | GeometryCollection |

## 特征对象
类型为Feature的对象是特征对象：
* 必须有一个名为geometry的属性，值是下面几种集合对象，或者null
* 必须有一个名为properties的属性，值是一个对象（自定义信息放其中）
* 如果特征是常用的标识符，那么这个标识符需要包含名字为id的属性

### 点 Point
示例
```json
{
    "type": "Point", 
    "coordinates": [100.0, 0.0]
}
```
> 点坐标按照x、y顺序（投影坐标东向、北向，地理坐标长度、高度）

### 线 LineString
示例
```json
{
    "type": "LineString",
    "coordinates": [ 
        [100.0, 0.0], [101.0, 1.0] 
    ]
}
```

### 面 Polygon
示例
```json
{
    "type": "Polygon",
    "coordinates": [[
        [100.0, 0.0], 
        [101.0, 0.0], 
        [101.0, 1.0], 
        [100.0, 1.0], 
        [100.0, 0.0] 
    ]]
}
```
数字第一个元素为外部环，如果是有空的，则后续元素表示内部环，示例
```json
{
    "type": "Polygon",
    "coordinates": [[
        [100.0, 0.0], 
        [101.0, 0.0], 
        [101.0, 1.0], 
        [100.0, 1.0], 
        [100.0, 0.0] 
    ], [ 
        [100.2, 0.2], 
        [100.8, 0.2], 
        [100.8, 0.8], 
        [100.2, 0.8], 
        [100.2, 0.2] 
    ]]
}
```

### 多点 MultiPoint
示例
```json
{
    "type": "MultiPoint",
    "coordinates": [
        [100.0, 0.0], 
        [101.0, 1.0]
    ]
}
```

### 多线 MultiLineString
示例
```json
{
    "type": "MultiLineString",
    "coordinates": [
        [
            [100.0, 0.0], [101.0, 1.0] 
        ], [ 
            [102.0, 2.0], [103.0, 3.0] 
        ]
    ]
}
```

### 多面 MultiPolygon
示例
```json
{
    "type": "MultiPolygon",
    "coordinates": [
    [
        [
            [102.0, 2.0], 
            [103.0, 2.0], 
            [103.0, 3.0], 
            [102.0, 3.0], 
            [102.0, 2.0]
        ]
    ], [
        [
            [100.0, 0.0], 
            [101.0, 0.0], 
            [101.0, 1.0], 
            [100.0, 1.0], 
            [100.0, 0.0]
        ], [
            [100.2, 0.2], 
            [100.8, 0.2], 
            [100.8, 0.8], 
            [100.2, 0.8], 
            [100.2, 0.2]
        ]
    ]]
}
```

### 几何集合 GeometryCollection
示例
```json
{
    "type": "GeometryCollection",
    "geometries": [
        {
            "type": "Point",
            "coordinates": [100.0, 0.0] 
        }, {
            "type": "LineString", 
            "coordinates": [ 
                [101.0, 0.0], [102.0, 1.0] 
            ]
        }]
}
```

### 可选参数
可能有一个可选的crs成员，值必须是一个坐标参考系统的对象
可能有一个bbox成员，值是边界框数组，如
```json
{
    "type": "Feature",
    "bbox": [-180.0, -90.0, 180.0, 90.0], 
    "geometry": { 
        "type": "Polygon", 
        "coordinates": [[
            [-180.0, 10.0], [20.0, 90.0], [180.0, -5.0], [-30.0, -90.0]
        ]]
    } 
    ...
}
```

## Java操作GeoJSON
Maven依赖
```xml
<dependency>
  <groupId>org.geotools</groupId>
  <artifactId>gt-geojson</artifactId>
  <version>${geotools.version}</version>
</dependency>
```
### 读GeoJSON串
```java
GeometryJSON gjson = new GeometryJSON();
// be sure to strip whitespace
String json = "{'type':'Point','coordinates':[100.1,0.1]}";

Reader reader = new StringReader(json);
Point p = gjson.readPoint( reader );
```
### 写GeoJSON
```java
FeatureJSON fjson = new FeatureJSON();
StringWriter writer = new StringWriter();

fjson.writeFeature(feature(1), writer);

String json = writer.toString();
```

## 相关工具
* [geojson.io](http://geojson.io) 在线可视化、编辑工具

## Reference
* [官网](https://geojson.org/)
* [RFC7946](https://tools.ietf.org/html/rfc7946)
* [GeoTools-GeoJSON Plugin](http://docs.geotools.org/stable/userguide/unsupported/geojson.html)
* [GeoJSON示例](http://slblogimg.oss-cn-beijing.aliyuncs.com/files/geo-json-demo.json)
* [在线Lint工具](http://geojsonlint.com/)