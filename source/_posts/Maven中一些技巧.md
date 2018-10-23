---
title: Maven中一些技巧
date: 2016-09-01 14:28:19
tags: Maven
---
。本文记录使用Maven过程中遇到的一些Tip

<!--more-->
## 0x01 在pom.xml中使用占位符
***
比如在配置 Spring 时，可能有很多版本相同的配置，可以使用占位符来统一设置
```
<properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <spring.version>4.3.2.RELEASE</spring.version>
</properties>

<dependencies>
    <!--Spring -->
    <dependency>
        <groupId>org.springframework</groupId>
        <artifactId>spring-context</artifactId>
        <version>${spring.version}</version>
    </dependency>
</dependencies>
```

## 0x02 指定仓库下载
***
Maven下载依赖默认是去中央仓库下载，速度比较慢，我们可以在pom.xml中配置指定的仓库地址，如公司私服之类，来加速依赖的下载速度
```
<repositories>
    <repository>
        <id>mvnrepository</id>
        <name>mvnrepository</name>
        <url>http://www.mvnrepository.com/</url>
        <layout>default</layout>
        <releases>
            <enabled>true</enabled>
        </releases>
        <snapshots>
            <enabled>false</enabled>
        </snapshots>
   </repository>
</repositories>
```
或者直接在 ~/.m2/settings.xml 中配置全局的
```
<settings>    
  ...    
  <profiles>    
    <profile>    
      <id>dev</id>    
      <!-- repositories and pluginRepositories here-->    
    </profile>    
  </profiles>    
  <activeProfiles>    
    <activeProfile>dev</activeProfile>    
  </activeProfiles>    
  ...    
</settings>    
```
。参考：http://blog.csdn.net/dongnan591172113/article/details/7685164

## 0x03 执行Maven target时使用不同的配置
***
使用 settings 参数
```
mvn --settings ~/.m2/settings-customer1.xml clean install
```
或者用缩写
```
mvn –s ~/.m2/settings-customer1.xml clean install
```

## 0x04 测试时指定类
***
使用代码
```
mvn test -Dtest=[ClassName]
mvn test -Dtest=[ClassName]#[MethodName]
```
同时支持通配符
```
mvn test -Dtest=MyClassTest#*test*
```
。参考：https://www.oschina.net/code/snippet_157514_37277

## 0x05 mvn compile时报错
***
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20160901/mvn_compile_err.png)
可能是因为compiler的版本和实际使用的版本不同造成，在pom.xml中手工指定版本即可
```
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <source>1.8</source>
        <target>1.8</target>
    </configuration>
</plugin>
```
当然，更好是配置一个property，通过占位符配置


## 0x06 关闭 package 等 phase 时自动test
***
。在pom.xml中增加配置变量
```
<properties>
    <maven.test.skip>true</maven.test.skip>
</properties>
```
。或者也可以手工调用的时候传参
```
    mvn clean package -Dmaven.test.skip=true
```
。参考：http://stackoverflow.com/questions/7456006/maven-packaging-without-test-skip-tests
