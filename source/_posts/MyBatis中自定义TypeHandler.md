---
title: MyBatis中自定义TypeHandler
date: 2016-07-31 22:21:34
tags: Java
---

## 自定义TypeHandler 
1. 实现 TypeHandler 接口 
2. 或者继承 BaseTypeHandler<LocalDate> 类 
3. 在配置文件中注册对应的 typeHandler 

在INSERT语句中使用TypeHandler，额外增加配置即可，例如
```java
<insert id="insertItem" parameterType="com.moji.tob.cproj.pingan.meteo.model.MetaDataProcessItem">
INSERT INTO tb_pingan_meta_process (data_type) VALUES (
    #{dataType, typeHandler=com.moji.tob.cproj.pingan.meteo.mapper.ProcessDataTypeHandler})
</insert>
```

## JDK8的LocalDateTime等兼容
默认不兼容LocalDateTime（早先版本，现在的版本不确定），可以直接使用别人实现好的type-handler [参考](https://github.com/mybatis/typehandlers-jsr310)
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20160731/mybatis-type-handler-ldt.png)

相关：[OSF问题](http://stackoverflow.com/questions/25113579/java-8-localdate-mapping-with-mybatis)

## 自定义TypeHandler支持BitSet
SpringBoot项目，源码如下
```java
@Slf4j
@MappedTypes({BitSet.class})
@MappedJdbcTypes({JdbcType.BLOB})
public class BitSetTypeHandler implements TypeHandler<BitSet> {

    @Override
    public void setParameter(PreparedStatement ps, int i, BitSet parameter, JdbcType jdbcType) throws SQLException {
        ps.setString(i, parameter.toString());
    }

    @Override
    public BitSet getResult(ResultSet rs, String columnName) throws SQLException {
        String content = rs.getString(columnName);
        return doConvertBitSet(content);
    }

    @Override
    public BitSet getResult(ResultSet rs, int columnIndex) throws SQLException {
        String content = rs.getString(columnIndex);
        return doConvertBitSet(content);
    }

    @Override
    public BitSet getResult(CallableStatement cs, int columnIndex) throws SQLException {
        String content = cs.getString(columnIndex);
        return doConvertBitSet(content);
    }

    public final static BitSet doConvertBitSet(String content) {
        if (StringUtils.isNotEmpty(content)) {
            content = content.replaceAll("\\{", "").replaceAll("}", "");
            Set<Integer> apiIds = Splitter.on(",")
                    .omitEmptyStrings()
                    .trimResults()
                    .splitToList(content)
                    .stream()
                    .filter(StringUtils::isNotEmpty)
                    .map(Integer::parseInt)
                    .collect(Collectors.toSet());
            if (CollectionUtils.isNotEmpty(apiIds)) {
                BitSet bitSet = new BitSet();
                for (Integer apiId : apiIds) {
                    bitSet.set(apiId);
                }
                return bitSet;
            }
        }
        return null;
    }
}
```
MyBatis中添加配置
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20160731/mybatis-type-handler-config.png)

## Reference
* [mybatis typeHandler自定义类型转换器](https://hussion.iteye.com/blog/1530696)
* [MyBatis自定义数据映射TypeHandler](http://my.oschina.net/amoshuang/blog/134199)
* [Mybatis_TypeHandler 的简单应用及源码分析](http://www.importnew.com/25617.html)
* [How to use TypeHandler for INSERT statements in MyBatis](https://stackoverflow.com/questions/26141913/how-to-use-typehandler-for-insert-statements-in-mybatis)



