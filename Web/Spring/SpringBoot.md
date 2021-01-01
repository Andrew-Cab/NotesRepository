#一.SpringBoot入门

> pom文件

```xml
<parent>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.9.RELEASE</version>
</parent>

<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
    	<artifactId>spring-boot-starter-web</artifactId>
	</dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

<parent>标签用于导入SpringBoot版本控制中心，以后导入依赖默认使用父工程的版本

**spring-boot-starter-web：**spring-boot场景启动器，帮我们导入了web模块正常运行所依赖的组件，SpringBoot将所有的功能场景都抽取出来，做成一个个**starters（启动器）**，只需要在项目中引入这些starter，相关场景的所有依赖都会导入进来。要用什么功能就导入什么启动器

> 编写controller

```java
package com.zbvc.controller;

@Controller
public class HelloWorld{
    @ResponseBody
    @RequestMapping("/hello")
    pubilc String hello(){
        return "HelloWorld";
    }
}
```

> 主程序类（入口类）

```java
package com.zbvc;

@SpringBootApplication
public class HelloWorldApplication{
    public static void main(String[] args){
        SpringApplication.run(HelloWorldApplication.class, args);
    }
}
```

直接运行主程序即可启动工程



# 二.SpringBoot配置

## 1.YAML语法

**k: v**：表示一组键值对，==冒号和值之间必须有空格==，属性和值的大小写敏感

> **值的写法**

- 数字，字符串，布尔

  ```yaml
  key: value
  ```

  值的字符串默认不需要加引号，如果加引号，单双引号有区别

  - **字符串加单引号：**特殊字符会作为字符串，如**'\n'**只表示\n这个字符
  - **字符串加双引号：**特殊字符会作为特殊字符，如**"\n"**表示换行

- 对象，Map

  ```yaml
  对象名/Map名:
  	属性名/key: 属性值/value
  	属性名/key: 属性值/value
  ```

  行内写法：

  ```yaml
  对象名: {属性名/key: 属性值/value,属性名/key: 属性值/value}
  ```

- 数组(List，Set)
  用==-==表示数组中的一个元素，元素与==-==之间必须有空格

  ```yaml
  pets:
  	- cat
  	- dog
  ```

  行内写法：

  ```yaml
  pets: [cat,dog]
  ```

##2.获取yaml文件中配置的值

> 在pom文件中添加配置文件处理器依赖，编写yaml文件时可以有提示

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-configuration-processor</artifactId>
    <optional>true</optional>
</dependency>
```

> 定义Bean对象

`@ConfigurationProperties`注解用于将yaml文件中配置的值注入到Bean对象中，*prefix*属性用于指定将yaml文件中的哪一个键所表示的对象属性与Bean对象的属性进行映射。SpringBoot会自动加载application.properties/yaml文件

```java
@Component
@ConfigurationProperties(prefix="person")
@Validated
public class Person{
    @Email
    private String emial;
    private Integer age;
    private Date birthday;
    private Map<String,Object> map;
    private List<Object> list;
    private Dog dog;
    //getter和setter
}
```

`@ConfigurationProperties`和Spring中的`@Value`注解都可以用来获取yaml或properties文件中的值

| **注解**              | `@ConfigurationProperties`                 | `@Value`     |
| --------------------- | ------------------------------------------ | ------------ |
| 功能                  | 批量注入配置文件中的属性值                 | 一个一个指定 |
| SpEL表达式（**${}**） | 不支持                                     | 支持         |
| JSR303数据校验        | 支持，使用时在Bean对象上添加@Validated注解 | 不支持       |
| 复杂类型封装          | 支持                                       | 不支持       |

> 定义yaml文件

```yaml
person:
	email: lisi@qq.com
	age: 18
	birthday: 2000/04/18
	map: {k1: v1,k2: v2}
	list:
		- lisi
		- wangwu
	dog:
		name: 小狗
		gae: 4
```



## 3.SpringBoot配置文件

SpringBoot有**application.properties**和**application.yml**两种配置文件，可以通过这两种配置文件修改SprintBoot的默认配置

> 配置文件中的占位符

1. 获取随机数

   ```markd
   ${random.uuid}
   ${random.int}
   ```

2. 获取之前配置的属性值，如果key在之前没有定义，可以使用 **:** 来指定默认值

   ```markd
   ${key:defaultValue}
   ```

例：

```properties
person.name=张三${random.uuid}
person.age=${random.int}
person.dog.name=${person.name:乐乐}_dog
```

```java
@Component
@ConfigurationProperties(prefix="person")
public class Person{
    private String name;
    private Integer age;
    private Dog dog;
    //getter和setter
}
```

> **多环境支持**

SpringBoot默认加载**application.properties**文件，SpringBoot提供多环境支持，在编写配置文件时，可以定义多个配置文件，命名为**application-xxx.properties**，在**application.properties**配置文件中使用==spring.profiles.active=xxx==来激活某个配置文件

例：

- application.properties

  ```prop
  spring.profiles.active=dev
  server.port=8080
  ```

- application-dev.properties

  ```proper
  server.port=8081
  ```

  启动SpingBoot后使用的tomcat端口号是**8081**

对于yaml文件，可以使用==---==将一个配置文件分为多文档快，通过在yaml中配置**spring**对象来配置不同的环境，默认加载第一文档快的环境，可在第一文档块配置**spring**对象，通过其**profiles**属性的**active**属性指定要激活的环境

例：

- application.yaml

  ```yaml
  server:
  	port: 8081
  spring:
  	profiles:
  		active: prod
  ---
  server:
  	port: 8083
  spring:
  	profiles: dev
  ---
  server:
  	port: 8084
  spring:
  	profiles: prod
  ```

在配置了多环境文件时，也可以通过**Run/Debug Configurations--->在program arguments中添加--spring.profiles.active=xxx**指定运行环境，该配置优先级最高，通用两种配置文件

> 配置文件的加载位置

SpringBoot会扫描以下位置所有的application.properties或application.yaml文件作为默认配置文件，配置文件中的配置优先级从高到低，高优先级位置的配置会优先被采用，低优先级位置的配置如果在高优先级位置的没有配置，则也会被采用

1. 类路径下的config目录
2. 类路径下

> 外部配置加载顺序

1. 项目打包成jar后，可通过命令行修改项目配置

   ```markd
   #修改端口号和项目虚拟目录
   java -jar jar位置 --server.port=8087 --server.context-path=/HelloWorld
   ```

2. 与打包好的jar包同一目录下的外部配置文件的配置优先于jar包内部的配置文件。在项目打包好后，可通过在项目jar包同级目录下添加外部配置文件修改项目的配置

> **自动配置原理**

SpringBoot启动时加载主程序类，主程序上类上的注解`@SpringBootApplication`由多个注解组合而成：

- `@SpringBootConfiguration`标记了配置类，底层使用Spring中的`@Configuration`注解进行标记
- `@EnableAutoConfiguration`用于开启自动配置功能，同样由多个注解组合而成
  - `@AutoConfigurationPackage`为自动配置包，使用Spring中的`@Import`导入子配置类，==该子配置类将主程序类所在包及其下面所有子包的所有组件扫描到了Spring容器中==

