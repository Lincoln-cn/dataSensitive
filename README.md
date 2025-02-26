# 中文介绍 | [English Introduction](./README-EN.md)
# 项目介绍和使用手册

## 项目介绍

这个项目是一个数据敏感信息处理工具，主要用于在Java应用中对敏感数据进行掩码处理。它基于Spring Boot框架，提供了一系列注解和配置来简化敏感数据的处理。

### 序列化工具要求
**序列化框架需要使用spring boot 默认的 jackson 框架才能生效**

## 功能特性

- **注解标记**: 支持通过注解标记敏感字段，简化代码中的敏感信息处理。
- **自定义掩码规则**: 允许用户定义自己的掩码规则以满足不同的业务需求。
- 支持通过配置文件定义掩码规则
- 集成Jackson，自动对JSON序列化时进行数据掩码

## 使用手册

### 1. 引入依赖

在你的`pom.xml`文件中添加以下依赖：

JDK 1.8
```xml
<dependency>
    <groupId>io.github.lincoln-cn</groupId>
    <artifactId>sensitive-starter</artifactId>
    <version>1.0</version>
</dependency>
```


JDK 17
```xml
<dependency>
    <groupId>io.github.lincoln-cn</groupId>
    <artifactId>sensitive-starter</artifactId>
    <version>1.0</version>
    <classifier>jdk17</classifier>
</dependency>
```

### 2. 非侵入式配置

在`application.properties`或`application.yml`中添加掩码规则配置，不侵入式的将属性字段进行脱敏。该方法会在项目全局有效。
在示例中，任意类中返回属性名称为test的属性，都会通过正则匹配后脱敏返回。

```yaml
data-masking: # 掩码设置
  enabled: true # 开启   
  rules:  #规则 可以多条
    - field: "test" # 字段属性名称
      regex: "(.*?)(.{4})(.*)" # 正则表达式
      replacement: "$1****$3" # 替换后的结构
```

#### 如需特例

在代码中使用`@IgnoreSensitive`，注解注释该属性，则可以跳过全局脱敏配置

### 3. 使用注解

在你的Java类中使用`@Sensitive`注解标记需要掩码的字段：

```java
public class User {
    @Sensitive(strategy = SensitiveStrategy.EMAIL)
    private String email;

    @Sensitive(strategy = SensitiveStrategy.PHONE)
    private String phoneNumber;
    
    private String test;
}
```

## 示例代码
Data Object
``` java
import com.example.sensitive.Sensitive;
import com.example.sensitive.SensitiveType;
import com.fasterxml.jackson.databind.ObjectMapper;

public class User {
    @Sensitive(type = SensitiveType.EMAIL)
    private String email = "example@example.com";

    @Sensitive(type = SensitiveType.PHONE)
    private String phoneNumber = "18152484065";
    
    private String test = "testTest";

    public User(String email, String phoneNumber, String test) {
        this.email = email;
        this.phoneNumber = phoneNumber;
        this.test = test;
    }

    // Getters and Setters
    public String getEmail() {
        return email;
    }

    public void setEmail(String email) {
        this.email = email;
    }

    public String getPhoneNumber() {
        return phoneNumber;
    }

    public void setPhoneNumber(String phoneNumber) {
        this.phoneNumber = phoneNumber;
    }
    
    public String getTest() {
        this.test = test;
    }
    
    public void setTest(String test) {
        this.test = test;
    }
}
```
假设SensitiveStrategy.EMAIL和SensitiveStrategy.PHONE的掩码规则分别是将邮箱的域名部分和电话号码的中间四位掩码处理，预期的JSON输出可能如下：
``` json
{
    "email": "*******@example.com",
    "phoneNumber": "181****4065",
}

```
假设属性test是通过全局非侵入式配置，将脱敏规则配置在文件中，预期的JSON输出可能如下：
``` json
{
    "test": "****Test"
}

```

## springboot3.x 与 spring boot2.x

springboot3.x支持的jdk最低版本为JDK17

### springboot2.x

```angular2html
# spring boot2.x 使用
META-INF/spring.factories

#
org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
org.unreal.starter.sensitive.configuration.SensitiveAutoConfiguration


```

### springboot3.x

```angular2html
# 3.0后使用
META-INF/spring/org.springframework.boot.autoconfigure.AutoConfiguration.imports

```




## 常见问题

### 如何自定义掩码规则？

你可以在配置文件中定义自定义掩码规则，或者实现`SensitiveStrategy`枚举类中添加脱敏逻辑，并使用注解在属性上使用。

### 如何集成到现有项目？

方案一、通过全局非侵入式配置，将脱敏规则配置在文件中，即可默认实现脱敏。

方案二、少量配置或不配置脱敏规则，通过注解方式，进行属性脱敏

## 贡献

欢迎提交PR和Issue来帮助我们改进项目。

## 许可证

该项目使用MIT许可证，详情请查看LICENSE文件。
