---
title: 使用mapStruct进行BeanMap
date: 2016-12-23 14:58:39
tags: Java
---

。MapStruct是一个BeanMap的方案，可以让我们很方便的进行Bean之间转换
![](http://slblogimg.oss-cn-beijing.aliyuncs.com/images/20161223/mapstruct.png)

## What and Why
***
MapStruct是一个可以让我们很方便进行BeanMap的工具，因为我们知道，在Java开发中，经常会遇到几种领域对象，参考博文：
。这时候，我们就有了经常需要将几个领域对象进行互转的需求了
<!--more-->

之前项目中其实考虑2种方案：

> Dozer 和 Apache Commons

。先说说问题吧，上述2个其实都能实现该功能，Dozer个人感觉比commons更强一些，因为其提供了XML配置的形式进行property的映射，相比通过代码（当然，你也可以写工具类）的形式，我感觉XML的形式比较直观，而且当属性有修改或者增删时，直接修改XML配置来的更加直观
。再说说问题，上述2种方式其实究其原理，都使用了反射
。而我们知道反射在Java中又是相对比较慢的一个操作，然而MapStruct则另辟蹊径，使用Maven插件的形式，在编译的过程中生成BeanMapper的实现，相对更快速，而且其针对Spring的整合也很好，也有很方便的注解的形式进行配置，所以最后选了MS

## 配环境
***
。上面我们说到，MS是使用Maven插件的形式进行生成实际的BeanMapperImpl，所以第一步，我们需要配置pom.XML
```xml
<properties>
    <org.mapstruct.version>1.1.0.Final</org.mapstruct.version>
</properties>
<dependencies>
    <dependency>
        <groupId>org.mapstruct</groupId>
        <artifactId>mapstruct</artifactId> <!-- use mapstruct-jdk8 for Java 8 or higher -->
        <version>${org.mapstruct.version}</version>
    </dependency>
</dependencies>
...
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifactId>
            <version>3.5.1</version>
            <configuration>
                <source>1.6</source> <!-- or higher, depending on your project -->
                <target>1.6</target> <!-- or higher, depending on your project -->
                <annotationProcessorPaths>
                    <path>
                        <groupId>org.mapstruct</groupId>
                        <artifactId>mapstruct-processor</artifactId>
                        <version>${org.mapstruct.version}</version>
                    </path>
                </annotationProcessorPaths>
            </configuration>
        </plugin>
    </plugins>
</build>
```

## 注解的形式配置映射
***
。官网文档上介绍了具体的使用方式，这里记录自己项目中的2种方法:
> 直接注解配置映射
> 结合 Spring

。这里我们准备2个领域对象，一个User表示用户，一个UserVo表示传递给前台页面的展示对象
```java
public class User {
    private Integer id;
    private String name;
    private LocalDateTime createTime;
    private Integer userType;
    private Integer status;

    /**
    * getter & setter 略过
    */
}
```
。VO对象
```java
public class UserVo {
    private Integer id;
    private String name;
    private String createTime;
    private String userType;

    /**
    * getter & setter 略过
    */
}
```
。编写BeanMapper，代码如下
```java
@Mapper
public interface UserMapper {
    UserMapper INSTANCE = Mappers.getMapper(UserMapper.class);
    @Mappings({
            @Mapping(target = "createTime", dateFormat = "YYYY-MM-DD HH:mm")
    })
    UserVo userToUserVo(User user);
}
```
。注意点

> 这里的 Mapper 都是mapstrcut包下的
> INSTANCE中根据不同的BeanMapper写不同的类
> 对应的 User 和 UserVo 方法名类似上面

。其中@Mappings可以配置2个类的字段映射，当字段名称不同时可以在这里做配置
。其一些内置变量有内置的映射关系，比如上面的 LocalDateTime 类型的createTime转到字符串型的createTime，可以直接配置 dateFormat
。其他内置规则参考官方文档

## 结合Spring
***
。MapStruct可以整合Spring一起使用，即在 Mapper 中注入Spring中的Bean
。具体可以参考官网文档，其实使用过程还是相对较简单的，即实现一个BeanMapperDecorator，实现上的UserMapper，代码如下
```java
public abstract class UserMapperDecorator implements UserMapper {
    @Autowired
    @Qualifier("delegate")
    private UserMapper delegate;
    @Override
    public UserVo userToUserVo(User user) {
        UserVo userVo = delegate.userToUserVo(user);
        userVo.setUserType(UserTypeEnum.getUserTypeByCode(user.getUserType()).getName());
        return userVo;
    }
}
```
。同时修改之前的接口，增加 @DecoratedWith，代码如下
```java
@Mapper(componentModel = "spring")
@DecoratedWith(UserMapperDecorator.class)
public interface UserMapper {
    UserMapper INSTANCE = Mappers.getMapper(UserMapper.class);
    @Mappings({
            @Mapping(target = "createTime", dateFormat = "YYYY-MM-DD HH:mm"),
            @Mapping(target = "userType", ignore = true)
    })
    UserVo userToUserVo(User user);
}
```
。比如我们之前的userType，因为是从code到String，而这个映射关系是在一个枚举中定义的，枚举代码如下
```java
public enum UserTypeEnum {
    USER_TYPE_ANONY(0, "anony"),
    USER_TYPE_MEMBER(0, "member"),
    USER_TYPE_NORMAL(0, "normal");
    private int code;
    private String name;
    public int getCode() {
        return code;
    }
    public void setCode(int code) {
        this.code = code;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    public static UserTypeEnum getUserTypeByCode(Integer code) {
        if (code == null) {
            return null;
        }
        UserTypeEnum[] types = UserTypeEnum.values();
        for (UserTypeEnum type : types) {
            if (type.getCode() == code) {
                return type;
            }
        }
        return null;
    }
    UserTypeEnum(int code, String name) {
        this.code = code;
        this.name = name;
    }
}
```
。因为是配置成了Spring的支持，所以不仅仅是可以做枚举的转换，同时，我们还可以在BeanMapperDecorator中注入其他Bean，比如Service等实现更复杂的转换
。不过需要注意的是，使用方式也有一些变动，比如原来使用 BeanMapper.INSTANCE 来调用beanMapping的方法，现在则是通过注入的形式进行
```java
@SpringBootApplication
public class DemoApplication implements CommandLineRunner {
    @Resource
    private UserMapper userMapper;
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
    @Override
    public void run(String... strings) throws Exception {
        User user = new User();
        user.setName("name");
        user.setCreateTime(LocalDateTime.now());
        user.setUserType(0);
        System.out.println(user);
//      UserVo userVo = UserMapper.INSTANCE.userToUserVo(user); // 原来方法
        UserVo userVo = userMapper.userToUserVo(user);
        System.out.println(userVo);
    }
}
```

## 遇到的问题
***
。经常遇到的一个问题就是，因为MapStruct是使用Maven插件的形式进行动态生成BeanMapper，所以经常会报找不到对应实现类的问题
。其实可能是因为项目编译过程不完全或者什么，重新编译一次就行
。Maven中我们可以使用命令，重新编译一次即可
```
mvn clean compile -Dmaven.test.skip=true
```

## Reference
***
[官网文档](http://mapstruct.org/documentation/1.1/reference/html/index.html)
