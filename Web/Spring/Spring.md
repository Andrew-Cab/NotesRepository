# 一.Spring

## 1.Spring入门

###1.1.Spring组件注册

1. 导入jar包

   ```xml
   <dependency>
       <groupId>org.springframework</groupId>
       <artifactId>spring-context</artifactId>
       <version>5.0.2.RELEASE</version>
   </dependency>
   ```
   
2. 配置applicationContext.xml

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xmlns="http://www.springframework.org/schema/beans"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
       
       <!--把对象交给Spring管理,默认使用类中的无参构造函数，没有则无法创建
           Spring的IOC容器是一个map结构，key是bean标签的id，value是根据class属性创建出的对象
       -->
       <bean id="person" class="com.zbvc.domain.Person"></bean>
   </beans>
   ```

3. 编写测试类

   ```java
   public class Test{
       @Test
       poublic void test(){
           ApplicationConext ac = ClassPathXmlApplicationContext("applicationContext.xml");
           Person person = (Person) ac.getBean("person");
       }
   }
   ```
   
   ### 1.2.SPEL
   
   JSP中的EL表达式：**${}**
   
   SPEL：**#{}**，支持运算，引用其它bean、其它bean的某个属性值，调用非静态方法、静态方法获取它们的返回值
   
   调用静态方法语法：==#{T(全类名).静态方法名(参数列表)}==
   
   调用非静态方法语法：==#{bean对象id.非静态方法名(参数列表)}==
   
   ```xml
   <bean id="person" class="com.zbvc.domain.Person">
   	<property name="salary" value="#{14+42}"/>
       <property name="name" value="#{student.name}"/>
       <property name="car" value="#{car}"/>
       <property name="email" value="#{T(java.util.UUID).randomUUID().toString().substring(0,5)}"/>
       <property name="gender" value="#{student.getGender()}"/>
   </bean>
   <bean id="student" class="com.zbvc.domain.Student">
   	<property name="name" value="zs"/>
   </bean>
   <bean id="car" class="com.zbvc.domain.Car"/>
   ```

## 2.applicationContext.xml文件

> **1.bean标签：**bean标签的配置顺序并不是Spring的加载顺序，位置不分先后

1. *name*：为bean对象定义别名，可以定义多别名，别名之间用“,”分割，可通过别名在IOC容器中获取对象。如：<bean name="name1,name2"></bean>

2. *parent*：如果一个bean对象和另一个bean对象相似，可通过该属性将已存在的bean对象属性继承下来，只需为值不同的属性再次赋值即可

3. *abstract*：将该值设置为**true**后表示该bean只能用于其它bean的继承，不能从容器中获取

4. *autowire*：在不显示的为bean对象的引用类型属性赋值时，该*autowire*可以根据一定规则为该引用类型赋值，而不是赋为**null**
   取值：

   - **default：**不自动装配
   - **byName：**按照bean对象中的属性名去IOC容器中查找id相同的bean对象进行赋值。找不则赋为**null**
   - **byType：**按照bean对象中属性的类型去容器中查找，如果有多个同类型bean则报异常。如果bean对象是集合类型的属性，则会将容器中符合集合泛型的所有bean装配到集合中
   - **constructor：**按照bean对象的构造器为赋值。先找到bean对象的构造器，根据构造器参数类型去容器中查找相同类型的bean对象赋给构造器参数，如果IOC容器有多个同类型bean则进一步按照参数名去容器中查找并赋值给构造器参数，然后调用构造器为这些引用类型变量赋值

5. **bean的作用范围：**bean的scope属性
   取值：

   -  ==singleton：单例（默认值）==
   - ==prototype：多例模式==
   - request：作用于web应用的请求范围，同一次请求只创建一个对象
   - session：作用于web应用的会话范围，同一个session创建一个对象
   - global-session：作用于集群（多台服务器）环境的会话范围（全局会话范围），当不是集群环境时，他就是session

6. **bean对象的生命周期：**

   - 单例对象：
     1. 容器创建时，对象产生
     2. 只要容器还在，对象就一直存活者
     3. 容器销毁，对象消失
     
     **若想要使单例对象也实现懒加载，则需要在bean标签中配置==lazy-init="true"==**
   - 多例对象：
     
     1. 当使用对象时，Spring创建对象，即getBean时创建
     2. 对象只要被使用着就会一直存在
     3. 当对象长时间不使用或没有被变量所引用便会被java垃圾回收

> **2.\<util:map/>**

用于创建集合类型的Bean，方便其它Bean对象的Map类型属性引用

```xml
<beans>
    <bean id="user" class="com.zbvc.domain.User">
    	<property name="map" ref="myMap"/>
    </bean>
	<util:map id="myMap">
    	<entry key="name" value="zs"/>
    </util:map>
</beans>
```


> **3. bean对象的初始化和销毁**

- **初始化**

  - 方法一：编写bean对象时，实现**InitializingBean**接口，重写*afterPropertiesSet*方法

    ```java
    package com.zbvc.bean;
    
    public class Bean implements InitializingBean{
        public Bean(){
            System.out.println("bean对象被创建");
        }
        
        @Override
        public void afterPropertiesSet() throws Exception(){
            System.out.println("初始化方法被执行了");
        }
    }
    ```

    这样在xml中配置bean对象后，当Spring工厂为我们创建该对象时，就会自动调用初始化方法

  - 方法二：编写bean对象时，随便编写一个方法，然后在<bean>中使用**init-method**属性指定

    ```java
    package com.zbvc.bean;
    
    public class Bean {
        public Bean(){
            System.out.println("bean对象被创建");
        }
        
        public void initMethod() {
            System.out.println("初始化方法被执行了");
        }
    }
    ```

    ```xml
    <beans>
    	<bean id="bean" class="com.zbvc.Bean" init-method="initMethod"></bean>
    </beans>
    ```

    ==当一个bean即实现了**InitializingBean**接口，又配置了*init-method*属性，那么会先执行接口配置的初始化方法，再执行属性指定的初始化方法。此外，当为该bean对象的属性注入值时，注入永远发生在初始化方法之前==

- **销毁**

  - 方法一：实现**DisposableBean**接口，重写*destory*方法

    ```java
    package com.zbvc.bean;
    
    public class Bean implements DisposableBean{
        public Bean(){
            System.out.println("bean对象被创建");
        }
        
        @Override
        public void destory() throws Exception(){
            System.out.println("销毁方法被执行了");
        }
    }
    ```

  - 方法二：编写bean对象时，随便编写一个方法，然后在<bean>中使用**destory-method**属性指定

    ```java
    package com.zbvc.bean;
    
    public class Bean {
        public Bean(){
            System.out.println("bean对象被创建");
        }
        
        public void destoryMethod() {
            System.out.println("销毁方法被执行了");
        }
    }
    ```

    ```xml
    <beans>
    	<bean id="bean" class="com.zbvc.Bean" destory-method="destoryMethod"></bean>
    </beans>
    ```

  ==手动关闭容器才会调用销毁方法，因为main方法先结束，容器才会关闭，这样会导致销毁方法没机会执行，需要注意的是关闭容器的方法（close()）被定义在容器的子类中，在关闭时需要将接口类型的容器引用便令强转为实现类的类型（ClassPathXmlApplicationContext）。**销毁方法只对单例对象有效**==



> **4.import标签**

- resource：用于引入其它spring配置文件

> **5.依赖注入**

能注入的数据：

1. 基本类型和String
2.  其它bean类型（在配置文件中或者注解配置过的bean）
3.  数组/集合类型

注入方式：

1. 使用构造函数提供：在bean标签内部使用**constructor-arg**标签指定对象的构造函数
   **constructor-arg的属性：**

   -  *type*：参数的数据类型
   - *index*：构造函数中参数的索引
   - *name*：构造函数中参数的名字

   **\=\=\=\=\=\=\=\=\=\==以上三种参数都是用来确定要为哪个参数赋值\=\=\=\=\=\=\=====**

   - *value*：为参数提供基本类型和String类型的数据
   - *ref*：为参数指定其它bean类型数据，它指的是Spring的IOC核心容器中有的bean对象

   >  例如：调用两个参数的构造器创建对象

   ```xml
   <bean id="user" class="com.zbvc.domain.User">
       <constructor-arg name="age" value="11"></constructor-arg>
       <constructor-arg name="birthday" ref="now"></constructor-arg>
   </bean>
   <bean id="now" class="java.util.Date"></bean>
   ```

2. 使用set方法提供：在bean标签中使用property标签
   **property的属性：**

   - *name*：要注入的值所使用的set方法名称去掉set，第一个字母改小写（即属性值），如果要访问属性的属性则通过**"属性名.属性名"**即可
   - *value*：要注入的基本数据类型和String类型的值
   - *ref*：要注入的bean对象

   > 例如：

   ```xml
   <bean id="user" class="com.zbvc.domain.User">
       <property name="age" value="11">
           <!--<value>"11"</value>-->
       </property>
       <property name="birthday" ref="now">
       	<!--<ref bean="now"/> 也可以将<bean>直接定义在此处来注入-->
       </property>
       <!--也可以使用命名空间p来简化注入，在“p:”后直接加属性名即可注入；对于引用类型，只需在属性名后加“-ref”然后指明bean对象的id。需要导入相关约束
   	<bean id="user" class="com.zbvc.domain.User" p:age="11" p:birthday-ref="now"/>-->
   </bean>
   <bean id="now" class="java.util.Date"></bean>
   ```

   >  对于集合和数组类型值的注入采用以上两种方法都可以，且结构相同的数据其子标签可以互换使用
   >
   >  如：List，Set，Array的标签可互换，Map和Property同一级的标签可互换

   ```xml
   <bean id="User" class="com.zbvc.domain.User">
       <property name="list">
           <array/list/set>
               <value>AAA</value>
               <value>BBB</value>
           </array/list/set>
       </property>
   
       <property name="map">
           <map>
               <entry>
          			<key><value>"A"</value></key>
       			<value>"aaa"</value>
               </entry>
               <entry key="B" value="bbb"></entry>
               <entry key="c">
               	<bean class="com.zbvc.domain.User">
                   	<proerty name="name" value="zs"/>
                   </bean>
               </entry>
           </map>
        </property>
   
   	<property name="property">
   		<props>
   			<prop key="A">aaa</prop>
               <prop key="B">bbb</prop>
   		</props>
   	</property>
   </bean>
   ```
   
   > 为属性赋null值
   
   ```xml
   <bean id="user" class="com.zbvc.domain.User">
   	<property name="name">
       	<null/>
       </property>
   </bean>
   ```
   
   


> **6.纯xml开发的配置文件编写**

```xml
<beans xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns="http://www.springframework.org/schema/beans"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">
    
    <bean id="studentService" class="com.zbvc.service.imp.StudentServiceImp">
        <!--注入Dao-->
        <property name="studentDao" ref="studentDao"></property>
    </bean>
    <!--配置Dao对象-->
    <bean id="studentDao" class="com.zbvc.dao.imp.StudentDaoImp">
        <!--注入QueryRunner-->
        <property name="runner" ref="runner"></property>
    </bean>
    <!--配置QueryRunner对象-->
    <bean id="runner" class="org.apache.commons.dbutils.QueryRunner" scope="prototype">
        <!--注入数据源对象-->
        <constructor-arg name="ds" ref="dataSource"></constructor-arg>
    </bean>
    <!--配置数据源对象-->
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <!--注入链接数据库信息-->
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/db"></property>
        <property name="user" value="root"></property>
        <property name="password" value="root"></property>
    </bean>
</beans>
```

> **7. context:property-placeholder标签**

- *location*：用于引入properties配置文件，在填写配置文件的路径时，添加**classpath:**关键字可以代表**类路径下**。使用**"${键名}"**获取到配置文件中该键所对应的值

**properties配置文件中的键名不能写为`username`，此为Spring中的一个关键字，表示当前系统的用户名，使用${}会取出Spring关键字所表示的值**

## 3.IOC容器

**控制反转（Inverse of Control）：**将对成员变量赋值的控制权从代码中转移到Spring的配置文件，交给Spring工厂完成以达到解耦的目的。控制反转是一种思想

使用完整的IOC容器需要导入4个jar包

- spring-beans
- spring-core
- spring-context
- spring-expression

> **IOC容器的两个接口**

1. **BeanFactory:**
   采用延迟加载，用于生产Bean对象
2. **ApplicationContext:**
   **BeanFactory**的子接口，是容器接口

> **ApplicationContext的常用实现类**

1. **用于非web环境（main方法，junit单元测试）：**
   - *ClassPathXmlApplicationContext* 只能加载src路径下的配置文件，文件名可以使用“*”进行通配，进行多配置文件的整合
   - *FileSystemXmlApplicationContext* 可以加载磁盘任意路径下的配置文件，但必须写全路径
   - *AnnotationConfigApplicationContext*  用于读取注解创建容器，参数可以是配置类的class，也可以是包，表示包下的所有配置类，用于多配置类的整合
2. **用于web环境下:**
   - *WebXmlApplicationContext*   需要导入==spring-webmvc.jar==

> **容器的常用方法**

1. `Object getBean(String id)`    获取容器中指定id的bean对象，具有重载，若再传入一个该对象的class对象，则会由Spring完成强制类型转换；只传class对象，则根据类型从容器中获取对象，且不需要类型转换
2. `String[] getBeanDefinitionNames()`  获取所有<bean>的id值
3. `String[] getBeanNamesForType(Class class)`   获取指定类型的bean对象的<bean>的id值
4. `boolean containBeanDefinition(String beanId)`    判断容器中是否含有指定id的bean对象
5. `boolean containBean(String beanName)`   可用来判断容器是否包含指定name / id的对象

## 4.复杂对象的创建

> **1. 实现FactoryBean接口**

**BeanFactory**接口用于生产多个对象，而**FactoryBean**用于创建一个对象

```java
package com.zbvc.factorybean;

//该接口的泛型表示要创建的对象的类型，这里以创建数据库连接对象为例
public class ConnectionFactoryBean implements FactoryBean<Conneciton>{
    //用于书写创建复杂对象的代码，并把复杂对象作为方法的返回值返回
    @Override
    public Connection getObject() throws Exception{
        Class.forName("com.mysql.jdbc.Driver");
        //useSSL参数是MySQL为保证获取连接时的安全性，需要提供一个SSL证书，不写该参数会产生警告，不影响使用
        Connection conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db?useSSL=false","root","root");
        return conn;
    }
    //返回所创建对象的Class对象
    @Override
    public Class<?> getObjectType(){
        return Connection.class;
    }
    //返回true表示返回一个单例对象，false表示返回一个多例对象
    @Override
    public boolean isSingleton(){
        return false;
    }
}
```

```xml
<!--实现了FactoryBean接口的对象在使用getBean()方法从容器中获取对象时，会直接获取该对象的返回值，也就是Connection，若想要获取该工厂对象时，只需在Bean对象ID前使用“&”即可。如：getBean("&conn")-->
<bean id="conn" class="com.zbvc.factorybean.ConnectionFactoryBean"></bean>
```

==运行原理：当使用getBean()获取对象时，Spring框架会根据传入的id值在配置文件中找到对应的\<bean>，通过instanceof判断其是否为FactoryBean的子类，再通过接口回调，找到接口中所规定的方法的实现，调用getObject()方法返回对象。该工厂的getBean()方法永远是在使用对象时被调用的，不管对象是不是单例对象==



> **2. 实例工厂**

```markdown
# 使用实例工厂的好处：
1. 避免Spring框架的侵入
	实现了Spring所提供的接口FactoryBean以后，如果不再使用Spring框架，则该工厂就没有任何意义
2. 整合遗留系统
	若工程中已经有了可以获取某个对象的方法，在获取时可以使用实例工厂来获取。实例工厂主要需要编写的是applicationContext.xml文件
```

- 编写工厂类

```java
package com.zbvc.factorybean;

public class ConnectionFactory{
    public Connection getConnection(){
        Connection conn;
        try{
             Class.forName("com.mysql.jdbc.Driver");
        	 conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db?useSSL=false","root","root");
        }catch(ClassNotFoundException e){
            e.printStackTrace();
        }catch(SQLException e){
            e.printStackTrace();
        }
       return conn;
    }
}
```

- xml配置

```xml
<beans>
	<bean id="instanceFactory" class="com.zbvc.factorybean.ConnectionFactory"></bean>
    <bean id="conn" factory-bean="instanceFactory" factory-method="getConnection"></bean>
</beans>
```



> **3. 静态工厂**

- 编写静态工厂

```java
package com.zbvc.factorybean;

public class StaticConnectionFactory{
	public static Connection getConnection(){
        Connection conn;
    	try{
        	Class.forName("com.mysql.jdbc.Driver");
        	conn = DriverManager.getConnection("jdbc:mysql://localhost:3306/db?useSSL=false","root","root");
    	}catch(ClassNotFoundException e){
        	e.printStackTrace();
    	}finally(SQLException e){
			e.printStackTrace();
    	}
    	return conn;
    }
}
```

- 配置xml
  **如果工厂方法有参数可以在\<bean>中使用\<constructor-arg>来指定**

```xml
<bean>
	<bean id="conn" class="com.zbvc.factorybean.StaticConnectionFactory" factory-method="getConnection"></bean>
</bean>
```

## 5.Spring整合日志框架

Spring5.x默认整合的日志框架为**logback**和**log4j2**

> **log4j整合**

1. **导入log4j坐标**

   ```xml
   <dependency>
   	<groupId>org.slf4j</groupId>
       <artifactId>slf4j-log4j12</artifactId>
       <version>1.7.25</version>
   </dependency>
   
   <dependency>
   	<groupId>log4j</groupId>
       <artifactId>log4j</artifactId>
       <version>1.2.17</version>
   </dependency>
   ```

2. **定义log4j.properties**

   ```properties
   #Resource文件夹根目录下
   # 配置根
   log4j.rootLogger = debug,console
   
   #日志输出到控制台显示
   log4j.appender.console=org.apache.log4j.ConsoleAppender
   log4j.appender.console.Target=System.out
   log4j.appender.console.layout=org.apache.log4j.PatternLayout
   log4j.appender.console.layout.ConversionPattern=%d{yyyy-MM-dd HH:mm:ss} %-5p %c{1}:%L - %m%n
   ```

> **logback整合**

1. **导入坐标**

   ```xml
   <dependency>
   	<groupId>org.slf4j</groupId>
       <artifactId>slf4j-api</artifactId>
       <version>1.7.25</version>
   </dependency>
   <dependency>
   	<groupId>org.slf4j</groupId>
       <artifactId>jcl-over-slf4j</artifactId>
       <version>1.7.25</version>
   </dependency>
   <dependency>
   	<groupId>ch.qos.logback</groupId>
       <artifactId>logback-classic</artifactId>
       <version>1.2.3</version>
   </dependency>
   <dependency>
   	<groupId>ch.qos.logback</groupId>
       <artifactId>logback-core</artifactId>
       <version>1.2.3</version>
   </dependency>
   <dependency>
   	<groupId>org.logback-extensions</groupId>
       <artifactId>logback-ext-spring</artifactId>
       <version>0.1.4</version>
   </dependency>
   ```

2. **编写logback.xml**

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <configuration>
   	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
       	<encoder>
               <!--格式化输出，%d表示日期，%thread表示线程名，%5level表示级别从左显示5个字符宽度，%msg表示日志消息，%n表示换行符-->
           	<pattern>%d{yyyy-MM-dd HH:mm:ss.SSSS} [%thread] %-5level %logger{50} - %msg%n</pattern>
           </encoder>
       </appender>
       
       <root level="DEBUG">
       	<appender-ref ref="STDOUT"/>
       </root>
   </configuration>
   
   ```

   

## 6. 类型转换器

在为bean对象注入数据时，当我们为bean对象中一个int型数据注入String型数据时，Spring会通过类型转换器为我们完成类型转换

> 自定义类型转换器

- 定义javaBean

```java
package com.zbvc.domain;

public class Person{
    private String name;
    private Date birthday;
    
    public String getName(){
        
    public Date getBirthday(){
        return this.birthday;
    }
    public void setName(String name){
        this.name = name;
    }
    public void setBirthday(Date birthday){
        this.birthday = birthday;
    }
}
```

- 编写类型转换器：实现**Converter<s,t>**接口，两个泛型分别表示==待转换类型==和==转换为什么类型==。Spring会将==待转换的数据的值==从xml中获取到，然后注入到*convert*方法中的参数**source**中，将该方法的返回值注入到bean对象中

```java
package com.zbvc.converter;

public class DateConverter implements Converter<String,Date> {
    private String pattern;
    public String getPattern(){
        return this.pattern;
    }
    public void setPattern(String pattern){
        this.pattern = pattern;
    }
    @Override
    public Date convert(String source){
        Date date = null;
        try{
        	SimpleDateFormat sdf = new SimpleDateFormat(pattern);
        	Date date = sdf.parse(source);   
        }catch(ParseException e){
            e.printStackTrace();
        }
        return date;
    }
}
```

- 配置类型转换器：由Spring创建编写好的类型转换器和Spring提供的**ConversionServiceFactoryBean（创建该bean对象时的id必须为"conversionService"）**，为该类中的==converters==属性注入自定义类型转换器的bean对象，该属性是一个==Set集合==。这样当Spring创建对象时，发现xml中的数据类型与bean对象属性的类型不匹配时，会自动调用对应的类型转换器进行类型转换

```xml
<beans>
	<bean id="person" class="com.zbvc.domain.Person">
    	<property name="name" value="wujianqi"></property>
        <property name="birthday" value="2000-04-18"></property>
    </bean>
    
    <bean id="dateConverter" class="com.zbvc.converter.DateConverter">
    	<property name="pattern" value="yyyy-MM-dd"/>
    </bean>
    <bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
    	<property name="converters">
        	<set>
            	<ref bean="dateConverter"/>
            </set>
        </property>
    </bean>
</beans>
```

==Spring中内部也有一个Date的类型转换器，若要使用Spring中的日期转换器，在对bean对象中Date类型的变量注入值时，该值的格式必须**用斜线做分隔，如：2000/04/18**==

## 7.BeanPostProcessor

用于对工厂创建出的对象初始化前后进行再加工。使用方法：实现**BeanPostProcessor**接口，重写*postProcessBeforeInitialization*和*postProcessAfterInitialization*，方法的两个参数分别为==Spring工厂创建出的对象==和==该对象在IOC容器的id值==。在xml中进行注册

**注：BeanPostProcessor会对IOC容器中的所有对象进行加工操作**

> **示例**

```java
package com.zbvc.beanpostprocessor;

public class MyBeanPostProcessor implements BeanPostProcessor{
    @Override
    public Object postProcessBeforeInitialization(Object bean,String beanName) throws BeansException{
        return bean;
    }
    @Override
    public Object postProcessAfterInitializaiton(Object bean,String beanName) throws BeansException{
        if(bean instanceof Person){
        	Person person = (Person) bean;
        	//对该对象的nam进行修改
        	person.setName("wujianyang");
        }
		return bean;
    }
}
```

```xml
<beans>
	<bean id="person" class="com.zbvc.domain.Person">
            <property name="name" value="wujianqi"></property>
            <property name="birthday" value="2000-04-18"></property>
    </bean>
    
    <bean id="myBeanProcessor" class="com.zbvc.beanpostprocessor.MyBeanPostProcessor"></bean>
</beans>
```



> **Spring创建bean对象的全部流程**

**Spring读取配置文件 --> 获取\<bean> --> 调用构造方法创建对象 --> 为对象属性注入值--> 将创建出的对象传递给*postProcessBeforeInitialization*方法进行加工并返回 --> 执行初始化方法 --> 将bean对象传递给*postProcessAfterInitialization*方法进行加工并返回-->执行销毁方法，Spring的动态代理增强对象方法就发生在此时。单例对象创建时，会先从单例池中获取，如果获取不到再创建新的**

> 细节

通过**BeanDefinitionReader**接口的不同实现类分别对通过不同方式定义的Bean信息进行读取，根据这些信息创建出一个**BeanDefinition**对象，由**BeanFactory**根据这些Bean信息通过反射创建出对象存入Map容器

## 8.Spring中多配置文件使用

```markdown
为方便管理，通常需要将配置信息分别放入多个配置文件中，如：
1. Dao配置信息 ----->	applicationContext-dao.xml
2. Service配置信息	----->	applicationContext-service.xml
```

> 加载方式

使用通配符：

1. 非web环境下
   ApplicationContext ac = new ClassPathXmlApplicationContext("application-*.xml");

2. web环境下

   ```xml
   <context-param>
   	<param-name>contextConfigLocation</param-name>
       <param-value>classpath:applicationContext-*.xml</param-value>
   </context-param>
   ```

**import标签：**

创建主配置文件**applicationContext.xml**进行整合

```xml
<beans>
	<import resource="applicationContext-dao.xml"/>
    <import resource="applicationContext-service.xml"/>
</beans>
```



# 二.SpringAOP

```mark
AOP(Aspect Oriented Programing)		面向切面编程
OOP(Object Oriented Programing)		面向对象编程
POP(Producer Oriented Programing)	面向过程编程
```

**1.Joinpoint（连接点）：**被拦截的点，在Spring中指的是方法，因为Spring中只支持方法类型的连接点。也就是在动态代理中，被代理类的所有可以被增强的方法都是连接点。因为在动态代理生成的对象中，被代理类中的所有方法都会进行重写用于和代理类产生联系。

**2.Pointcut（切入点）：**指我们要对哪些**Joinpoint**进行增强，在InvocationHandler中若该方法被进行了增强，那么该方法就是切入点。也就是在使用动态代理时，在InvacationHandler接口的invoke方法中的==method.invoke()==语句里，这个method对象就是切入点，method就是通过反射封装的代理前的类的方法

**3.Advice（通知）：**增强**Joinpoint**时要进行的操作，例如对该方法进行事务的添加。通知分为：==前置通知（Before）、正常返回通知（AfterThrowing）、异常通知（AfterThrowing）、后置通知（After）、环绕通知（Around）。==分别对应的操作为：==开启事务、提交事务、回滚事务（写于catche代码块中）、归还连接（写于finally代码块中）、环绕通知是指前面所有通知+切入点（也就是动态代理InvocationHandler的invoke方法中的所有代码）==，正常返回通知只能在业务正常执行才会执行，也就是说正常返回通知和异常通知不会同时执行

**4.Introduction（引介）：**引介是一种特殊的通知，在不修改类代码的前提下，Introduction可以在运行期为类动态添加一些方法或field

**5.Target（目标对象）：**被代理的对象

**6.Weaving（织入）：**指把增强代码加入到目标对象来创建新的代理对象的过程

**7.Aspect（切面）：**是所有切入点和通知的结合，通知像面一样插入多个切入点

## 1.Spring动态代理开发

> 环境搭建

```xml
<dependency>
	<groupId>org.springframework</groupId>
    <artifactId>spring-aop</artifactId>
    <version>5.1.14.RELEASE</version>
</dependency>
<dependency>
	<groupId>org.aspectj</groupId>
    <artifactId>aspectjrt</artifactId>
    <version>1.8.8</version>
</dependency>
<dependency>
	<groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.8.3</version>
</dependency>
```

> **通过实现接口配置通知**

1. 创建原始对象

   ```java
   package com.zbvc.service;
   
   public Interface UserService{
       void register();
       boolean login();
   }
   ```

   ```java
   package com.zbvc.service.imp;
   
   public class UserServiceImp implements UserService{
       @Override
       public void register(User user){
           System.out.println("UserServiceImp.register 业务运算+DAO");
       }
       @Override
       public boolean login(String name,String password){
           System.out.println("UserServiceImp.login");
           return true;
       }
   }
   ```

2. 编写通知类：定义一个类，实现接口

   - **MethodBeforeAdvice**接口，重写*before*方法。经过配置后，该方法会作为**前置通知**执行
   - **MethodInterceptor**接口，重写*invoke*方法，参数MethodInvocation为**切入点**。参数提供了*proceed*方法可调用切入点方法，该方法会返回**切入点方法的返回值**，接收该返回值由*invoke*方法返回，当调用了被增强的方法时，如果*invoke*方法不返回**切入点的返回值**则获取到的返回值是*invoke*方法的返回值。定义在该方法调用语句前为**前置通知**，后为**后置通知**，写在`catch`代码块中就是**异常通知**，`finally`块中就是**最终通知**

   ```java
   package com.zbvc.dynamic;
   
   public class Before implements MethodBeforeAdvice{
       @Override
       public void before(Method method,Object[] args,Object target) throws Throwable{
           System.out.println("前置通知增强方法");
       }
   }
   ```

   ```java
   package com.zbvc.dynamic;
   
   import org.aopalliance.intercept.MethodInterceptor;
   
   public class Arround implements MethodInterceptor{
       @Override
       public Object invoke (MethodInvacation invocation) throws Throable{
           System.out.println("MethodInterceptor接口的前置通知");
           Object value = invocation.proceed();
           System.out.println("MethodInterceptor接口的后置通知");
           return value;
       }
   }
   ```

3. 配置xml

   ```xml
   <bean id="userService" class="com.zbvc.service.imp.UserServiceImp"></bean>
   <bean id="beforeAdvice" class="com.zbvc.dynamic.Before"></bean>
   
   <aop:config>
       <!--定义切入点-->
   	<aop:pointcut id="pointcut" expression="execution(* *..*.*(..))"/>
       <!--将切入点和通知组装成切面-->
       <aop:advisor advice-ref="beforeAdvice" pointcut-ref="pointcut"/>
   </aop:config>
   ```

   ```xml
   <bean id="userService" class="com.zbvc.service.imp.UserServiceImp"></bean>
   <bean id="arround" class="com.zbvc.dynamic.Arround"></bean>
   
   <aop:config>
       <!--定义切入点-->
   	<aop:pointcut id="pointcut" expression="execution(* *..*.*(..))"/>
       <!--将切入点和通知组装成切面-->
       <aop:advisor advice-ref="arround" pointcut-ref="pointcut"/>
   </aop:config>
   ```

4. 调用

   ```java
   @Test
   public void test(){
       ApplicationContext ac = ClassPathXmlApplicationContext("applicationContext.xml");
       UserService userService = (UserService) ac.getBean("userService");
       boolean flag = userService.login("wujianqi","123");
   }
   ```

## 2.AOP开发

1. 创建原始对象：同上

2. 编写通知类：一个普通的类

   ```java
   package com.zbvc.utils;
   
   import org.aspectj.lang.ProceedingJoinPoint;
   
   public class Logger {
       public void beforePrintLog(){
           System.out.println("前置通知：Logger类中的beforePrintLog方法开始执行");
       }
       public void afterReturnPrintLog(){
           System.out.println("正常返回：Logger类中的afterReturnPrintLog方法开始执行");
       }
       public void afterThrowingPrintLog(){
           System.out.println("异常通知：Logger类中的afterThrowingPrintLog方法开始执行");
       }
       public void afterPrintLog(){
           System.out.println("后置通知：Logger类中的afterPrintLog方法开始执行");
       }
   
       /* 若配置了环绕通知，则执行时不会执行业务代码，Spring框架为我们提供了一个接口，ProceedingJoinPoint，该接口有 一个方法proceed(),此方法就相当于明确调用切入点方法。该接口可作为通知方法的参数，在程序执行时，spring 会将正在执行的切点封装到该接口的实现类对象并传入。增强代码写在切入点方法的什么位置，该位置代码就会产生与对应通知相同的作用环绕通知就是通过代码来控制通知的类型，而不是xml配置
        */
       public Object aroundPrintLog(ProceedingJoinPoint pjp){
           Object value = null;
           try {
               Object[] args = pjp.getArgs();//得到方法执行所需要的参数
               System.out.println("环绕通知（前置）：Logger类中的aroundPrintLog方法开始执行");
               value = pjp.proceed(args);//调用业务层方法（切入点方法）
               System.out.println("环绕通知（正常）：Logger类中的aroundPrintLog方法开始执行");
           } catch (Throwable throwable) {
               System.out.println("环绕通知（异常）：Logger类中的aroundPrintLog方法开始执行");
               throwable.printStackTrace();
           }finally {
               System.out.println("环绕通知（后置）：Logger类中的aroundPrintLog方法开始执行");
           }
           return value;
       }
   }
   ```
   
3. 注册service和通知类的bean对象，通过标签将通知织入到**切点表达式**指定的方法中

   ```xml
   <bean id="userService" class="com.zbvc.service.imp.UserServiceImp"></bean>
   <bean id="logger" class="com.zbvc.utils.Logger"></bean>
   <aop:config>
   	<aop:aspect id="logAdvice" ref="logger">
   		<aop:before method="beforePrintLog" pointcut="execution(public void com.zbvc.service.imp.UserServiceImp.register())"/>
           <aop:after-returning method="afterReturnPrintLog" pointcut="execution(* com.zbvc.service.imp.*.*(..))"></aop:after-returning>
           <aop:after-throwing method="afterThrowingPrintLog" pointcut="execution(* com.zbvc.service.imp.*.*(..))"></aop:after-throwing>
           <aop:after method="afterPrintLog" pointcut-ref="pt"></aop:after>
           
           <!--配置通用切点表达式，使用引用的方式使用切点表达式，若该配置写在aop:aspect标签内，则只有该切面可以引用，若写在aop:aspect标签外则必须写在aop:aspect标签上方，可在其它切面中使用-->
           <aop:pointcut id="pt" expression="execution(* com.zbvc.service.imp.*.*(..))"/>
           
           <aop:around method="aroundPrintLog" pointcut-ref="pt"></aop:around>
       </aop:aspect>
   </aop:config>
   ```

   > 配置xml步骤说明

   1. 使用**aop:config**标签表明开始AOP配置

      - *proxy-target-class*：Spring底层创建代理对象默认使用JDK的方法，若要将其变为cglib的方式，只需将该属性设为"true"，默认值为"false"，即使用JDK的方式

   2. 使用**aop:aspect**标签表明开始切面配置
      - *id*：切面的唯一标识
      - *ref*：为切面指定通知的bean的id

   3. 在**aop:aspect**标签内使用如下标签配置通知类型

      - **aop:before**：前置通知
      - **aop:after-returning**：后置通知
      - **aop:after-throwing**：异常通知
      - **aop:after**：最终通知
      - **aop:around**：环绕通知

      标签属性：

      - *method*属性：用于指定要将通知类的什么方法作为该类型通知
      - *pointcut*属性：使用切入点表达式指定业务层中哪些方法增强
   - *pointcut-ref*属性：使用切入的表达式的id来引用公共切入点表达式
   
> **切点表达式配置说明**

**execution(访问修饰符 返回值 包名.类名.方法名(参数列表))**，当切点表达式中的方法被执行时spring就会使用代理机制动态创建出代理对象，将对该方法配置的通知织入并执行。

**切入点表达式：**

   - 访问修饰符可省，返回值可使用通配符“*”表示任意返回值
   - 包可以使用通配符表示任意包，但是有几级包就要写几个“\*”，eg：==* \*.\*.\*.\*.UserServiceImp.register()==
   - 包名可以使用“..”表示当前包及其子包。eg：==* *..UserService.register()==
   - 类名和方法名都可以使用通配符表示任意，eg：==* \*..*.\*()==
   - 基本数据类型参数直接写类型，引用数据类型参数写全类名，可用通配符“*”代表任意类型，但必须有与“\*”数量相等的参数
   - 可使用“(..)”表示任意类型参数且有无参数均可。eg：==* com.zbvc.service.imp.\*.*(..)==
- 全通配写法：==* \*..*.\*(..)==
  

**切入点函数：**

   - ==execution("切入点表达式")==
   - ==args(String , String)==：表示所有参数数量为两个，且类型为String的方法
- ==within(*..UserServiceImp)==：表示UserServiceImp类下的所有方法
  

**切入点函数的逻辑运算：**指多个切入点函数一起配合工作，完成更复杂的需求。

   - and（“与”操作）：表示**同时满足**两个切入点表达式所表述的方法才会被认为是切入点
     ==<aop:pointcut id="pc" expression="execution(* com.zbvc.service.imp.\*.*(..)) and args(String , String)"/>==
   - or（“或”操作）：表示**任意满足两个切入点表达式其中一个**所表述的方法才会被认为是切入点
     ==<aop:point id="pc" expression="execution(* \*..\*.register(..)) or execution(\* \*..*.login(..))"/>==



## 3.半注解的AOP开发

> **常用注解**

- `@Aspect`：被该注解修饰的类会被视为切面，相当于实现了**MethodInterceptor**接口

- `@Arround`：被该注解修饰的方法会作为环绕通知，该注解的参数为切点表达式；可使用**value**属性指定切点表达式方法，**需要带上方法的"()"**。被修饰的方法中可传入**ProceedingJoinPoint**类型的参数，用于接收原始方法

- `@AfterReturning`：被该注解修饰的方法会作为后置通知，可以使用*returning*属性配置通知方法的参数名，Spring会将切入点方法的返回值封装到该属性配置的参数中，在通知方法中，如果有多个参数，**ProceedingJoinPoint**类型的参数必须放在第一位；如果返回值类型与接受返回值的参数类型不匹配，该通知就不会被调用

  ```java
  @AfterReturning(value="* com.zbvc.service.imp.*.*(..)", returning="result")
  public void afterReturning(ProceedingJoinPoint joinPoint, Object result){
      System.out.println("切入点方法正常返回，放回结果："+result);
  }
  ```

- `@AfterThrowing`：被该注解修饰的方法会作为异常通知，通过*throwing*属性指定配置通知方法的参数名，当切入点方法发生异常时，Spring会将异常对象封装到该属性配置的参数中；如果发生的异常类型与接受异常对象的参数类型不匹配，该通知就不会被调用

  ```java
  @AfterThrowing(value="* com.zbvc.service.imp.*.*(..)", throwing="exception")
  public void afterThrowing(Exception exception){
      System.out.println("切入点方法发生异常，异常结果："+exception);
  }
  ```

- `@Pointcut`：定义一个空方法，该方法必须使用**public**修饰，使用该注解即可对切点表达式进行抽取，其它切面要引用该表达式则需要写方法的全限定名

- `@Order`：定义在类上，表示多个切面的执行顺序，数值越小，优先级越高

> **开发步骤**

1. 创建原始对象，同上

2. 定义切面类

   ```java
   package com.zbvc.aspect.MyAspect;
   
   @Aspect
   public class MyAspect{
       
       @Pointcut("execution(* *..*.*(..))")
       public void pointCut(){}
       
       //@Arround("execution(* *..*.*(..))")
       @Arrount(value="pointCut()")
       public Object arround(ProceedingJoinPoint jointPoint)throws Throwable{
           System.out.pritnln("半注解实现前置通知");
           Object o = jointPoint.proceed();
           return o;
       }
   }
   ```

3. 将切面类加入IOC容器

   ```xml
   <bean id="userService" class="com.zbvc.service.imp.UserServiceImp"></bean>
   <bean id="myAspect" class="com.zbvc.aspect.MyAspect"></bean>
   
   <!--告知Spring基于注解开发，可定义proxy-target-class属性切换代理实现方式-->
   <aop:aspectj-autoproxy/>
   ```

> 通知的执行顺序

**@Before--->@After--->@AfterReturning/@AfterThrowing**

## 4.纯注解AOP开发

在配置类上使用`@EnableAspectJAutoProxy`注解代替**< aop:aspectj-autoproxy/>**标签，可设置该标签的*ProxyTargetClass=true*来切换代理创建的方式

1. 创建原始对象

   ```java
   package com.zbvc.service.imp;
   
   @Service
   public class UserServiceImp implements UserService{
       @Override
       public void register(User user){
           System.out.println("UserServiceImp.register 业务运算+DAO");
       }
       @Override
       public boolean login(String name,String password){
           System.out.println("UserServiceImp.login");
           return true;
       }
   }
   ```

2. 编写切面类

   ```java
   package com.zbvc.aspect.MyAspect;
   
   @Aspect
   @Component
   public class MyAspect{
       
       @Pointcut("execution(* *..*.*(..))")
       public void pointCut(){}
       
       @Arrount(value="pointCut()")
       public Object arround(ProceedingJoinPoint jointPoint)throws Throwable{
           System.out.pritnln("纯注解实现前置通知");
           Object o = jointPoint.proceed();
           return o;
       }
   }
   ```

3. 编写配置类

   ```java
   package com.zbvc.aop;
   
   @Configuration
   @ComponentScan(basePackage="com.zbvc")
   @EnableAspectJAutoProxy
   public class AppConfig{
   }
   ```

4. 测试

   ```java
   package com.zbvc.test;
   
   public class Test{
       @Test
       public void test(){
           ApplicationContext ac = new AnnotationConfigApplicationContext(AppConfig.class);
           UserService service = ac.getBean("userServiceImp");
           service.register(new User());
       }
   }
   ```



## 5.AOP开发常见错误

###5.1.问题一

**当service层出现同一个类中的代码相互调用，且这两个方法都通过AOP进行了方法的增强，可能会产生只最外层方法会被SpringAOP进行增强**

> 例如：

```java
package com.zbvc.service.imp;

public class UserServiceImp implements UserService{
    @Override
    //调用register方法只有register方法会进行增强
    public void register(User user){
        System.out.println("UserServiceImp.register 业务运算+DAO");
        this.login("wujianqi","123");
    }
    @Override
    public boolean login(String name,String password){
        System.out.println("UserServiceImp.login");
        return true;
    }
}
```

>  **产生原因：**

在获取测试时是通过IOC容器获得的，是Spring经过**BeanPostProcessor**处理后的动态代理对象，通过该对象调用*register*方法实际调用的是代理对象的*invoke*方法；而我们在service层嵌套调用的方法是**this（UserServiceImp）**的，该对象中的方法并**没有增强**，所以会出现*login*的增强代码没有执行的情况

> **解决方法：**

因为IOC容器是一种重量级资源，一个应用中只能创建一个，所以我们需要在service层的类实现**ApplicationContextAware**接口，重写*setApplicationContext(ApplicationContext applicationContext)*方法，Spring会将我们创建好的容器传入到该方法的参数中，将该容器保存在本类中，通过容器获取代理对象，调用增强方法。

```java
package com.zbvc.service.imp;

public class UserServiceImp implements UserService,ApplicationContextAware{
    private ApplicationContext ac;
    @Override
    public void setApplicationContext(ApplicationContext applicationContext){
        this.ac = applicationContext;
    }
    @Override
    public void register(User user){
        System.out.println("UserServiceImp.register 业务运算+DAO");
        UserService userService = ac.getBean("userService",UserService.class);
        userService.login("wujianqi","123");
    }
    @Override
    public boolean login(String name,String password){
        System.out.println("UserServiceImp.login");
        return true;
    }
}
```

> 实现原理

获取IOC容器的功能由**ApplicationContextAwareProcessor**实现，该类实现了**BeanPostProcessor**接口，因此所有Bean对象在初始化之前都会先被传入*postProcessBeforeInitialization*方法，在该方法中判断bean对象是否是**ApplicationContextAware**的子类，如果是就调用*invokeAwareInterfaces*，传入Bean对象，将Bean对象转为**ApplicationContextAware**类型，调用**setApplicationContext**方法，传入IOC容器。除此之外Spring还内置了许多BeanPostProcessor，如**InitDestoryAnnotationBeanPostProcessor**，这个后置处理器就是用来处理初始化相关的两个注解；**AutoWiredAnnotationBeanPostProcessor**，这个后置处理器用来处理`@Autowired`注解。每一个xxxAware都有自己对应的BeanPostProcessor

###5.2.问题二

Spring默认使用JDK的动态代理，在IOC容器中保存的是代理对象，而代理对象是接口类型。如果要通过类型获取到一个被增强的对象，则需要通过这个对象的接口类型来获取，否则拿到的就只是一个普通对象；通过ID获取到的对象也只能转为接口类型，因为代理对象只和接口有关

如果使用cglib进行动态代理，则可以通过本对象的类型获取到代理对象。Spring会通过被代理对象是否实现了接口来自动切换两种代理方法

# 三.Spring整合MyBatis

**整合MyBatis的本质就是将MyBatis创建出的dao代理对象放入IOC容器中，需要添加mybatis-spring的jar包**

##1.xml版

> **1. SqlSessionFactoryBean**

**主配置文件主要有如下功能：**

- 配置数据源（dataSource）
- 为自定义数据类型起别名（typeAliases）
- 注册mapper文件

在IOC容器中创建**SqlSessionFactoryBean**可取代**mybatis-config.xml**主配置文件及以下代码：

```java
String resource = "mybatis-config.xml";
//读取主配置文件
inputStream = Resources.getResourceAsStream(resource);
//创建sqlSession工厂
SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
```

> Spring配置文件取代

将对象加入IOC容器：

**< bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">< /bean>**

使用<property>标签通过**SqlSessionFactoryBean**对象的属性为其注入数据：

- 指定数据源的属性
  - *name：*dataSource
  - *ref：***applicationContext.xml**文件中配置的数据源==id==
- 为类配置别名，别名就是类名
  - *name：*typeAliasesPackage
  - *value：*数据类（实体类）所在的包
- 注册mapper文件
  - *name：*mapperLocations，此变量为数组类型，设置mapper文件所在路径时，mapper文件名可用***Mapper.xml**进行通配
- 某些环境配置，如懒加载，依然可以配置在主配置文件下，使用该属性来扫描MyBatis的主配置文件
  - *name：*configLocation
  - *value：*配置文件所在路径，可使用**classpath:**表示类路径下

> **2. MapperSacnnerConfigurer**

在IOC容器中配置该bean对象可取代以下代码：

```java
sqlSession = sqlSessionFactory.openSession();
UserDao dao = sqlSession.getMapper(AccountDao.class);
```

> Spring配置文件取代

**< bean id="scanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">< /bean>**

使用<property>标签通过**MapperSacnnerConfig**对象的属性为其注入数据：

- 注入IOC容器中的**SqlSessionFactoryBean**对象：
  - *name*：sqlSessionFactoryBeanName
  - *value：*IOC容器中**SqlSessionFactoryBean**对象的==id==，这里使用value属性赋值是因为**sqlSessionFactoryBeanName**是一个字符串数据

- 扫描dao包下所有Dao接口，创建出接口的代理对象存入IOC容器：

  - *name：*basePackage

  - *value：*指定Dao接口所在的包

  **注：IOC容器中接口的代理对象id为接口名的第一个字母小写**

  

> **3.整合**

**applicationContext.xml：**

```xml
<beans>
    <!--配置连接池-->
	<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
    	<property name="url" value="jdbc:mysql:///ssm?useSSL=false"/>
    	<property name="username" value="root"/>
    	<property name="password" value="root"/>
    </bean>
    
    <!--创建SqlSessionFactory，指定数据源-->
    <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
    	<property name="dataSource" ref="dataSource"/>
        <property name="typeAliasesPackage" value="com.zbvc.domain"/>
        <property name="mapperLocations">
        	<list>
            	<value>classpath:com/zbvc/mapper/*Mapper.xml</value>
            </list>
        </property>
    </bean>
    
    <!--创建Dao对象-->
    <bean id="scanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    	<property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBean"/>
        <property name="basePackage" value="com.zbvc.dao"/>
    </bean>
</beans>
```

**测试：**

```java
package com.zbvc.test;

public class Test{
    @Test
    public void test(){
        ApplicationContext ac = new ClassPathXmlApplicationContext("applicationContext.xml");
        IUserDao userDao = (IUserDao) getBean("iUserDao");
        userDao.selectAll();
    }
}
```

> Spring与MyBatis整合细节

在MyBatis中使用的是该框架提过的数据源，需要**手动提交事务**，而Spring与MyBatis整合后则不需要，因为整合后的数据源是外部数据源，从该数据源中获取的连接都保持了默认的**自动提交事务**

## 2.纯注解版

在配置类上使用`@MapperScan`注解，配置属性*basePackages="com.zbvc.dap"*指明创建什么接口的dao代理对象并放入IOC容器

1. 定义配置文件

   ```properties
   mybatis.driverClassName=com.mysql.jdbc.Driver
   mybatis.url=jdbc:mysql://localhost:3306/db?useSSL=false
   mybatis.username=root
   mybatis.password=root
   mybatis.typeAliasesPackages=com.zbvc.domain
   mybatis.mapperLoactions=com.zbvc.mappers/*Mapper.xml
   ```

2. 定义存储配置信息的对象

   ```java
   package com.zbvc.domain;
   
   @Component
   @PropertySource("classpath:mybatis.properties")
   public class MyBatisProperties{
       @Value("${mybatis.driverClassName}")
       private String driverClassName;
       @Value("${mybatis.url}")
       private String url;
       @Value("${mybatis.username}")
       private String username;
       @Value("${mybatis.password}")
       private String password;
       @Value("${mybatis.typeAliasesPackages}")
       private String typeAliasesPackages;
       @Value("${mybatis.mapperLocations}")
       private String mapperLocations;
       //getter和setter
   }
   ```

3. 定义MyBatis的配置类

   ```java
   package com.zbvc.config;
   
   @Configuration
   @ComponentScan(basePackages="com.zbvc.domain")
   @MapperScan(basePackages="com.zbvc.dao")
   public class MyBatisConfig{
       @Autowired
       private MybatisProperties myBatisProperties;
       
       //创建数据源并放入IOC容器
       @Bean
       public DataSource dataSource(){
           DruidDataSource dataSource = new DruidDataSource();
           dataSource.setDriverClassName(myBatisProperties.getDriverClassName());
           dataSource.setUrl(myBatisProperties.getUrl());
           dataSource.setUserName(myBatisProperties.getUsername());
           dataSource.setPassword(myBatisProperties.getPassword());
           return dataSource;
       }
       //创建SqlSessionFactoryBean并放入IOC容器
       @Bean
       public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource){
           SqlSessionFactoryBean sqlSessionFactoryBean = new SqlSessionFactoryBean();
           sqlSessionFactoryBean.setDataSource(dataSource);
           //为pojo类起别名
           sqlSessionFactoryBean.setTypeAliasesPackage(myBatisProperties.getTypeAliasesPackages());
           //指定mapper文件路径
          try{
              ResourcePatternResolver resolver = new PathMatchingResourcePatternResolver();
              Resource[] resources = resolver.getResources(myBatisProperties.getMapperLocations());
              sqlSessionFactoryBean.setMapperLocation(resources);
          }catch(IOException e){
              e.printStackTrace();
          }
           return sqlSessionFactoryBean;
       }
   }
   ```

4. 编写mapper文件

   ```xml
   <mapper namespace="com.zbvc.dao.UserDao">
   	<insert id="save" parameterType="User">
       	INSERT INTO user (name,password) VALUES (#{name},#{password})
       </insert>
   </mapper>
   ```

5. 测试

   ```java
   @Test
   public void test(){
       ApplicationContext ac = new AnnotationConfigApplicationContext(MyBatisConfig.class);
       UserDao dao = ac.getBean("userDao",UserDao.class);
       User user = new User("wujianqi","12345");
       dao.save(user);
   }
   ```

   

# 四.Spring中的事务控制

==需要先导入spring-tx的jar包，或直接导入spring-jdbc的jar，根据传递依赖会自动导入spring-tx==

## 1.PlatformTransactionManager

该接口的实现类为：==DataSourceTransactionManager==,==HiberateTransactionManager==

由spring为我们提供的事务管理器，不需要我们在自己写事务管理器的类



## 2.TransactionDefinition

它是事务定义信息的对象，里面有如下方法：

**1.String getName()：**获取事务对象名称

**2.int getIsolationLevel()：**获取事务的隔离级别

**3.int getPropagationBehavior()：**获取事务传播行为

**4.int  getTimeout()：**获取事务超时时间

**5.boolean isReadOnly()：**获取事务是否为只读



### 2.1事务的隔离级别

**1.ISOLATION_DEFAULT：**默认级别，表示使用数据库的隔离级别

**2.ISOLATION_READ_UNCOMITTED：**可以读取未提交的数据

**3.ISOLATION_READ_COMMITTED：**只能读取已提交的数据，解决脏读问题（Oracle默认级别）

**4.ISOLATION_REPEATABLE_READ：**是否读取其它事务提交修改后的数据，解决不可重复读（MySQL默认级别，Oracle不支持，Oracle采用多版本比对避免不可重复读）

**5.ISOLATION_SERIALIZABLE：**是否读取其他事务提交添加后的数据，解决幻读问题



### 2.2事务的传播行为

**概念：**事务嵌套时决定使用哪一个事务，这种情况常出现于service层代码调用service层代码

**多事务嵌套产生的问题：**当一个大事务嵌套了许多小事务时，即若大事务中某些小事务产生异常发生了回滚，那么大事务也要回滚，而执行在异常小事务前的小事务已经正常提交了数据，大事务的多个操作并非同时成功或同时失败，导致大事务失去了原子性

**通过事务传播行为保证同时只存在一个事务：**

| 事务传播属性的值 |   外部方法不存在事务   |                   外部方法存在事务                   |                             备注                             |
| :--------------: | :--------------------: | :--------------------------------------------------: | :----------------------------------------------------------: |
|   **REQUIRED**   | 内部方法开启自己的事务 |                  加入到外部方法事务                  |             常用于**增删改**方法，该属性为默认值             |
|   **SUPPORTS**   |       不开启事务       |                  加入到外部方法事务                  |                      常用于**查询**方法                      |
| **REQUERS_NEW**  |       开启新事务       | 挂起外部事务，创建新事务，新事务执行完毕执行外部事务 | 用于插入日志记录到数据库方法中，由于日志不论业务方法是否执行成功都要执行，所以先挂起调用该方法的service方法的事务，先执行日志记录方法的事务，保证日志可以成功记录，然后开启service方法的事务保证service方法可以正常执行 |
|  NOT_SUPPORTED   |       不开启事务       |                     挂起外部事务                     |                                                              |
|      NEVER       |       不开启事务       |                       抛出异常                       |                                                              |
|    MANDATORY     |        抛出异常        |                    加入到外部事务                    |                                                              |



### 2.3.超时时间

**若当前事务所访问数据库中的数据被加了锁，则当前事务需要进行等待，可以设置超时时间，若等待事件超过该值，则抛出==TransactionTimedOutException==。默认值是-1，使用数据库的超时事件默认值。如果有则以秒为单位**



### 2.4.是否是只读事务

**针对于只进行查询的业务方法可以加入只读属性，提高运行效率，因为加入事务会为表中数据加上锁导致效率变低，设置只读属性该事务就不会加锁**



###2.5异常属性

```markdown
Spring事务默认处理过程中当方法抛出异常时：
对于RuntimeException及其子类采用的是回滚策略
对于Exception及其子类（编译期异常）采用的是提交策略

使用rollbackFor={NullPointerException.class,xxx,xxx}属性指定什么异常要回滚
使用noRollbackFor={RuntimeException.class,xxx,xxx}属性指定什么异常采用提交事务
```

> **总结**

增删改操作：**@Transactional**

查询操作：**@Transactional(propagation=Propagation.SUPPORTS , readOnly=true)**

## 3.TransactionStatus

此接口提供的是事务具体的运行状态，有如下方法

**1.voic flush()：**刷新事务

**2.boolean hasSavepoint()：**获取是否存在存储点（回滚点）

**3.boolean isCompleted()：**获取事务是否完成

**4.boolean isNewTransaction()：**获取事务是否为新的事务

**5.boolean isRollbackOnly()：**获取事务是否回滚

**6.void setRoolbackOnly()：**设置事务回滚

##4.事务配置实例

###1.注解+xml配置事务

配置applicationContext.xml

```xml
<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
    <property name="dirverClassName" value="com.mysql.jdbc.Driver"/>
    <property name="url" value="jdbc:mysql://localhost:3306/db"/>
    <property name="username" value="root"/>
    <property name="password" value="root"/>
</bean>

<bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
    <property name="dataSource" ref="dataSource"/>
    <property name="typeAliasesPackage" value="com.zbvc.domain"/>
    <property name="mapperLocations">
        <list>
            <value>classpath:com/zbvc/mapper/*Mapper.xml</value>
        </list>
    </property>
</bean>

<bean id="scanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    <property name="sqlSessionFactoryBeanName" ref="sqlSessionFactoryBean"></property>
    <property name="basePackage" value="com.zbvc.dao"/>
</bean>

<!--将service层对象加入IOC，并注入由scanner创建到IOC容器的dao对象-->
<bean id="userService" class="com.zvvc.service.imp.UserServiceImp">
	<property name="userDao" ref="userDao"/>
</bean>

<!--配置事务管理器-->
<bean id="dataSourceTransactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
    <property name="dataSource" ref="dataSource"/>
</bean>

<!--此处必须选择导入http://www.springframework.org/schema/tx约束下的tx:annotation-driven标签，使用该标签来建立切入点和事务管理器之间的联系，由Spring自动创建对service层的增强对象（代理对象），实现事务的添加，该标签同样可以使用proxy-target-class来指定通过什么方式实现代理对象的创建，如果事务管理器的名字为transactionManager则可以省略transaction-manager属性-->
<tx:annotation-driven transaction-manager="dataSourceTransactionManager"/>
```

使用**@Transactional**注解：用于告诉Spring事务加入在什么位置，即告诉Spring切入点都有哪些，使用在类上表示为类中的所有方法加上事务；在方法上使用表示为该方法加上事务

**注解属性配置事务属性：**

- isloation：
  - **Isolation.READ_COMMITTED**
  - **isolation.REPEATABLE_READ**
- propagation：
  - **Propagation.REQUIRED**
    **........**
- readOnly：true，默认值为false
- timeout
- rollbackFor
- noRollBackFor

```Java
package com.zbvc.service.imp;

@Transactional
public class UserServiceImp implements UserService{
	private UserDao userDao;
    
    public UserDao getUserDao(){
		return userDao;
    }
    
    public void setUserDao(UserDao userDao){
        this.userDao = userDao;
    }
    
    @Override
    @Transacional(propagation=Propagation.SUPPORTS,readOnly=true)
    public void selectUserById(int id){
        userDao.selectUserById();
    }
}
```

通过以上配置，当我们从IOC容器中获取UserServiceImp对象时，Spring就会在**BeanPostProcessor**中为对该对象中的方法添加事务管理

###2.xml配置事务

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:tx="http://www.springframework.org/schema/tx"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/tx
        https://www.springframework.org/schema/tx/spring-tx.xsd
        http://www.springframework.org/schema/aop
        https://www.springframework.org/schema/aop/spring-aop.xsd">
    
	  <!--配置连接池，此处使用的数据源是spring的内置数据源，需要导入spring-jdbc的jar-->
	<bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
		<property name="dirverClassName" value="com.mysql.jdbc.Driver"/>
    	<property name="url" value="jdbc:mysql://localhost:3306/db"/>
    	<property name="username" value="root"/>
    	<property name="password" value="root"/>
	</bean>
    
    <!--创建SqlSessionFactory，指定数据源-->
    <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
    	<property name="dataSource" ref="dataSource"/>
        <property name="typeAliasesPackage" value="com.zbvc.domain"/>
        <property name="mapperLocations">
        	<list>
            	<value>classpath:com/zbvc/mapper/*Mapper.xml</value>
            </list>
        </property>
    </bean>
    
    <!--创建Dao对象-->
    <bean id="scanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    	<property name="sqlSessionFactoryBeanName" ref="sqlSessionFactoryBean"/>
        <property name="basePackage" value="com.zbvc.dao"/>
    </bean>
    
    <!--配置事务管理器-->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSrouceTransactionManager">
    	<property name="dataSource" ref="dataSource"/>
    </bean>
    
    <!--配置事务通知,id为通知的唯一标识名，随便起，transaciton-manager为事务管理器,如果事务管理器的id为transactionManager则可以省略该属性-->
    <tx:advice id="txAdvice" transaction-manager="transactionManager">
		<!--
		配置事物的属性:
			name：切入点方法名，对指定的切入点进行特定的事务配置，可使用通配符
			isolation：用于指定事物的隔离级别，默认值是DEFAULT
			propagation：用于指定事务的传播行为，默认为REQUIRED
			read-only：指定事务是否为只读，只有查询方法才能设置为true，默认为false
			timeout：事物的超时时间，默认为-1
			rollback-for：用于指定一个异常，当产生该异常时，事务回滚，产生其他异常时不回滚。没有默认值，表示任何异常都回滚
			no-rollback-for：用于指定一个异常，当产生该异常时，事务不回滚；产生其他异常时事务回滚，没有默认值，表示任何异常都回滚，和rollback-for相同
		-->
        <tx:attributes>
            <tx:method name="modify*"/>
        	<tx:method name="*" propagation="SUPPORTS" read-only="true"/>
        	<!--通过以上两行配置即可实现对增删改切入点配置查询所需要的事务属性，对剩余全部方法配置所需要的事务属性，前提是增删改切入点必须以modify开头。因为相同name属性可以匹配多个方法时，name值精确度高的优先-->
        </tx:attributes>
    </tx:advice>
    
    <!--配置aop-->
    <aop:config>
    	<!--配置切入点表达式-->
        <aop:pointcut id="pt1" expression="execution(* com.zbvc.service.imp.*.*(..))"/>
        <!--建立切入点表达式和事务通知的对应关系-->
        <aop:advisor advice-ref="txAdvice" pointcut-ref="pt1"/>
    </aop:config>
</beans>
```

###3.纯注解配置事务

> **结合MyBatis纯注解版**

1. 创建原始对象

   ```java
   package com.zbvc.service;
   
   @Service
   @Transactional
   public class UserServiceImp implements IUserService{
       @Autowired
       private UserDao userDao;
       
       @Override
       public void register(User user){
           userDao.save(user):
       }
   }
   ```

2. 定义事务的配置类

   ```java
   package com.zbvc.config;
   
   @Configuration
   @EnableTransactionManagement
   public class TransactionConfig{
       @Autowired
       private DataSource dataSource;
       
       @Bean
       public DataSourceTransactionManager dataSourceTransactionManager(){
           DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
           dataSourceTransactionManager.setDataSource(dataSource);
           return dataSourceTransactionManager;
       }
   }
   ```

3. 测试

   ```java
   public void test(){
       ApplicationContext ac = new AnnotationConfigApplicationContext("com.zbvc.config");
       IUserService service = (UserServiceImp) ac.getBean("userServiceImp");
       service.register(new User("wujianqi","1234"));
   }
   ```

   

# 五.Spring整合Web

## 1.整合思路

在整合Spring整合web项目时，每个Servelt中都需要使用IOC容器，为了避免在每个Servlet中都写**new ClassPathXmlApplicationContext("applicationContext.xml")**导致每次访问Servlet都会创建一个IOC容器，可以使用**ServletContextListener监听器**，该监听器用于监听ServletContext的创建，可以在服务器开启时创建一个IOC容器将其存入==ServletContext域==中供所有Servlet使用。

> 创建监听器类

```java
package com.zbvc.listener;

public class ContextLoaderListener implents ServletContextListener{
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent){
        ApplicationContext app = new ClassPathXmlApplicationContext("applicationContext.xml");
        //将IOC容器存入ServletContext域中
        servletContextEvent.getServletContext().setAttribute("app",app);
    }
    @Override
    public void contextDestoryed(ServletContextEvent servletContextEvent){
        
    }
}
```

```xml
<!--在web.xml中添加监听器配置-->
<listener>
	<listener-class>com.zbvc.listener.ContextLoaderListener</listener-class>
</listener>
```

配置完毕后在Servlet中通过键名获取IOC容器对象即可

> 优化

上述代码中，创建IOC容器时需要传入的配置文件名可通过在web.xml中配置全局参数后在Servlet中获取参数值

```xml
<context-param>
	<param-name>contextConfigLocation</param-name>
    <param-value>applicaitonContext.xml</param-value>
</context-param>
```

因为将IOC容器对象存入了上下文域中，在Servlet中使用时需要通过键名获取到该对象，可以通过定义工具类，在工具类中获取该对象并返回，这样在Servlet中使用时只需调用该工具类的方法传入ServletContext对象，通过工具类即可获取到IOC容器的对象，不需要在记忆该属性的键名

```java
package com.zbvc.lisener;

public class WebApplicationContextUtils{
    public static ApplicationContext getWebApplicationContext(ServletContext servletContext){
        return (ApplicationContext) servletContext.getAttribute("app");
    }
}
```

## 2.整合

**以上代码不许手动实现，spring为我们提供了一个监听器==ContextLoaderListener==就是对上述功能的封装，该监听器内部加载Spring配置文件，创建应用上下文对象，并存储到ServeltContext域中，提供了一个客户端工具类==WebApplicationContextUtils==供使用者获取应用上下文对象。**

> 使用步骤

1. 在web.xml中配置==ContextLoaderListener==监听器，==需要导入spring-web坐标==
2. 使用==WebApplicationContextUtils==获取应用上下文对象==ApplicationContext==

```xml
<!--配置Spring配置文件的位置-->
<context-param>
	<param-name>contextConfigLocation</param-name>
    <param-value>classpath:applicationContext.xml</param-value>
</context-param>

<listener>
	<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

```java
public class UserServlet extends HttpServlet{
    @Override
    protected void doGet(HttpServletRequest req,HttpServletResponse resp) throws ServletException,IOException{
        ServletContext servletContext = this.getServletContext();
        //该WebApplicationContext就是ApplicationContext的子类
        WebApplicationContext app = WebApplicationContextUtils.getWebApplicationContext(servletContext);
    }
}
```



# 六.SSM整合

**两个框架整合后共有两个IOC容器，一个是Spring的，用来存放Service和Dao的对象，一个是SpringMVC的，用来存放Controller的对象。SpringMVC容器是Spring容器的子容器，在子容器中可以访问到父容器的Service和Dao对象**

> 配置web.xml

**Spring容器一定要先于SpringMVC容器加载，因为Controller中会使用`@Autowired`从IOC容器获取Service进行注入，SpringMVC容器在前端控制器Servlet创建时创建，所以Spring容器要在Listener中创建，Listener先于Servlet创建**

```xml
<web-app>      
    <!--Spring监听器-->
    <context-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:config/applicationContext.xml</param-value>
    </context-param>
    <listener>
        <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
    </listener>
    
    <!--前端控制器-->
    <servlet>
        <servlet-name>dispatcherServlet</serlvet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:config/springmvc.xml</param-value>
        </init-param>
        <load-on-startup>1</load-on-startup>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcherServlet</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    
    <!--编码过滤器-->
	<filter>
    	<filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
        	<param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
        	<param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
        <init-param>
        	<param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
    	<filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

> 配置jdbc.properties

```properties
jdbc.url=jdbc:mysql://localhost:3306/db
jdbc.driverClass=com.mysql.jdbc.Driver
jdbc.username=root
jdbc.password=root
```

> 配置applicationContext.xml

```xml
<beans>
	<context:property-placeholder location="classpath:config/jdbc.properties"/>
                                  
    <bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"
          init-method="init" destory-method="close">
        <property name="driverClassName" value="${jdbc.driverClass}"/>
    	<property name="url" value="${jdbc.url}"/>
        <property name="username" value="${jdbc.username}"/>
        <property name="password" value="${jdbc.password}"/>
    </bean>
    
    <bean id="sqlSessionFactoryBean" class="org.mybatis.spring.SqlSessionFactoryBean">
    	<proerty name="dataSource" ref="dataSource"/>
        <property name="mapperLocations">
        	<list>
            	<value>classpath:com/zbvc/mapper/*Mapper.xml</value>
            </list>
        </property>
    </bean>
    
    <!--添加Bean对象-->
    <bean id="scanner" class="org.mybatis.spring.mapper.MapperScannerConfigurer">
    	<property name="sqlSessionFactoryBeanName" value="sqlSessionFactoryBean"/>
        <property name="basePackage" value="com.zbvc.dao"/>
    </bean>
    
    <context:component-scan base-package="com.zbvc">
        <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <!--
		如果配置了全局异常处理的组件也交给SpringMVC的容器
		<context:exclude-filter type="annotation" expression = "org.springframework.web.bing.annotation.ControllerAdvice"/>
		-->
    </context:component-scan>
    
    <!--事物配置-->
</beans>
```

> 配置springmvc.xml

该配置文件只扫描`@Controller`注解，将被注解修饰的Bean加入SpringMVC的IOC中，而Bean中的其它注解由Spring来扫描，这样在为Controller注入Service层的Bean对象时就是经过Spring处理的Bean

```xml
<beans>
    <context:component-scan base-package="com.zbvc" use-default-filters="false">
        <context:include-filter type="annotation" expression="org.springframework.stereotype.Controller"/>
        <!--
		如果配置了全局异常处理的组件也交给SpringMVC的容器
		<context:exclude-filter type="annotation" expression = "org.springframework.web.bing.annotation.ControllerAdvice"/>
		-->
    </context:component-scan>
    
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    	<property name="prefix" value="/WEB-INF/jsp/"/>
        <property name="suffix" value=".jsp"/>
    </bean>
    
    <mvc:resource location="/static/" mapping="/static/**"/>
    <mvc:annotation-driven/>
</beans>
```



# 七.注解配置

##1.注解扫描

Spring基于注解配置后，如果对注解所配置的代码需要进行修改时，可以通过Spring配置文件进行覆盖，所以注解配置同样可以解耦

**使用注解开发后需要在xml中使用==<context:component-scan base-package="com.zbvc">< /context:component-scan>==来扫描注解**

> **细化扫描范围**

1. 排除方式：**< context:exclude-filter/>**，可同时定义多个标签进行排除

   - **type：**设置排除方式
     - assignable：排除特定的类不进行扫描
     - annotation：排除特定的注解不进行扫描
     - aspectj
     - regex
     - custom
   - **expression：**
     - ==type值是assignable时，该属性值为类的全类名==
     - ==type值是annotation时，该属性值为注解的全类名==
     - ==**type值是aspectj时，该属性值可以使用“..”和“*”进行包和类的通配，用法同切入点表达式**==
     - type值是regex时，该属性值为正则表达式
     - type值是custom时，该属性值为自定义排除策略

   ```xml
   <context:componenet-scan base-package="com.zbvc">
       <context:exclude-filter type="assignable" expression="com.zbvc.User"/>
       <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Service"/>
   	<context:exclude-filter type="aspectj" expression="*..User"/>
   </context:componenet-scan>
   ```

2. 包含方式：**< context:include-filter/>**，可同时定义多个标签进行包含

   属性和用法和排除方式的相同。因为配置了**< context:component-scan/>**标签后，Spring默认扫描策略为扫描*base-package*属性指定包下的所有类，所以需要先对该标签设置*use-default-filter="false"*属性，使默认扫描策略失效，再使用**< context:include-filter/>**指定要扫描什么注解

   ```xml
   <!--只扫描com.zbvc包下的Repository注解创建bean对象-->
   <context:component-span base-package="com.zbvc" use-default-filters="false">
   	<context:include-filter type="annotation" expression="org.springframewrok.stereotype.Repository"/>
   </context:component-span>
   ```

## 2.对象创建相关注解

- `@Component`：写在bean类的上方
- `@Controller`： 用于表现层
- `@Service`：用于业务层
- `@Repository`：用于持久层，因为持久层类通过MyBatis创建，所以该注解不需要使用

以上注解功能相同，可以通过**value**属性指定bean对象在IOC容器中的**id**，当不指定时，**默认为当前类名第一个字母小写**。

- `@Bean`：用于将被标记的方法的返回值作为Bean对象存入IOC容器中，如果方法有参数，Spring会根据类型匹配在IOC容器中查找并注入，主要用于创建复杂对象
      属性：*name*：用于指定bean的id，默认值为当前方法方名称

==被以上注解修饰的方法都是向IOC容器中添加组件，所以如果在配置类中重复调用被以上注解修饰的方法，不会创建新的Bean，而是从IOC容器中获取==

```java
@Configuration
public class Config{
    @Bean
    public DataSource dataSource() throws Exception{
        ComboPolledDataSource dataSource = new ComboPooledDataSource();
        dataSource.setUser("root");
        dataSource.setPassword("root");
        dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/db_dev");
        dataSource.setDriverClass("com.mysql.jdbc.Driver");
    }
    
    @Bean
    public JdbcTemplate() throws Exception{
        //此处调用dataSource方法作为参数不会新创建一个数据源，而是从IOC容器中获取
        JdbcTemplage jdbcTemplate = new JdbcTemplate(dataSource());
        return jdbcTemplate;
    }
}
```

## 3.注入数据

==集合类型数据不能通过注解注入==

- `@AutoWired`：基于类型注入，容器中与该注解标记的变量的类型相同的对象只有一个时，Spring会自动根据该变量类型匹配到容器中的对象，查看是否有对象可以存入该变量，有则直接注入到变量中；而如果有多个同类型的对象时，就会用该变量的变量名与容器中对象的id匹配来查找对象并注入。该注解可以写在成员变量上。写在成员变量上时不需要提供该变量的setter，Spring通过反射直接注入；也可以写在某个变量的setter上，构造器上，方法参数前，如果只有一个有参构造，则该注解可省略。可设置*required*属性为false，如果容器中没有对应类型的Bean就不注入，默认为true，如果容器中没有对应类型的Bean就报错。**除此之外，如果类有泛型，泛型匹配也是自动注入的参考条件**
- `@Qualifier`：在按照类型匹配的基础上，再按照名字注入。它给类成员注入时不能单独使用，必须加上AutoWired注解，在给方法参数注入时可以单独使用，属性value用于指定IOC容器中bean的id
- `@Primary`：当IOC容器中有多个同类型的Bean，被该注解修饰的Bean对象会被置为首选Bean。只使用`@Autowired`注解注入时会优先选择该Bean注入
- `@Resource`：直接按照bean的id注入，可独立使用。属性*name*用于指定bean的id，如果不设置*name*属性，则会按照类型注入。该注解是java定义的，扩展性更强，即使使用其它容器框架该注解也可以被识别
- `@PropertySource`：用于替换**context:property-placeholder**标签加载配置文件，写在类上
- `@Value`：用于注入基本数据类型和String类型。属性value用于指定数据的值，也可以使用**"${键名}"**获取配置文件中的值，==该注解不会为静态成员变量注入值==，可使用在方法的参数前

## 4.作用范围相关注解

- `@Scope`：写在类名上，属性value指定范围的取值，常用取值：singleton prototype
- `@Lazy`：写在类上，延迟创建Scope为singleton的对象

##5.初始化和销毁相关注解

- `@PreDestroy`：被标记的方法会在对象销毁前执行
- `@PostConstruct`：被标记的方法会在对象创建后执行

##6.Spring中的Junit

> 使用spring整合的junit配置，spring整合的main方法中会自动创建IOC容器：

1. 导入spring整合junit的jar包：spring-test

2. 使用Junit提供的注解`@Runwith`把原来Junit中提供的main方法替换为spring提供的

3. 通过`@ContextConfiguration`告知spring的ioc创建是基于注解还是xml，并说明位置
   **属性：**

   - location: 指定xml文件的位置，加上**classpath**关键字表示在类路径下
   - classes: 指定配置类 

   **注：**使用spring5.x版本的时候，要求junit的jar必须是4.12及以上

> **基于纯注解开发并整合Spring中的Junit**

**Spring中的新注解彻底取代xml文件**

`@Configuration`:
	被注解标记类会被视为配置类，相当于bean.xml，可以通过编写类加上该注释来取代xml
    当配置类作为**AnnotationConfigApplicationContext**的参数传入时，该注解可以不写

`@ComponentScan`：
    当xml由配置类取代时，在配置类上使用该注解修饰，用于告诉Spring扫描什么包下的注解来创建bean对象加入IOC容器
    属性：

- *basePackages*用于指定要扫描的包

- *excludeFilters*用于指定排除规则的过滤器。值为`@ComponentScan.Filter`注解的数组，通过该注解的*type*属性指定排除方法：

  - *FilterType.ANNOTATION：*注解排除
  - *FilterType.ASSIGNABLE_TYPE：*给定类型排除
  - *FilterType.ASPECTJ*
  - *FilterType.REGEX*
  - *FilterType.CUSTOM*

  ```markdow
  @ComponentScan(basePackages="com.zbvc", excludeFilters={
  	@ComponentScan.Filter(type = FilterType.ANNOTATION, classes={Controller.class}),
  	@ComponentScan.Filter(type = FilterType.ASPECTJ, pattern={"com.zbvc..*"})
  })
  ```

`@PropertySource`:
    用于指定properties文件的位置
    属性：value：指定文件的路径，可以只写文件名并在文件名前前加上关键字“classpath:”来表示该文件在类路径下

`ImportResource`:

用于将配置文件中的配置和配置类的配置结合在一起。eg：**@ImportResource("applicationContext.xml")**

1. **定义主配置类**

   ```java
   package config;
   
   @Configuration
   @ComponentScan(basePackages = "com.zbvc")
   @Import(JdbcConfig.class)
   @PropertySource("classpath:jdbcConfig.properties")
   public class SpringConfiguration {
   }
   ```

2. **定义jdbcConfig.properties**

   ```properties
   jdbc.driver=com.mysql.jdbc.Driver
   jdbc.url=jdbc:mysql://localhost:3306/db
   jdbc.user=root
   jdbc.password=root
   ```

3. **定义JDBC配置类，作为子配置类**

   ```java
   package config;
   
   public class JdbcConfig {
       @Value("${jdbc.driver}")//将配置文件中键所对应的值注入
       private String driver;
       @Value("${jdbc.url}")
       private String url;
       @Value("${jdbc.user}")
       private String user;
       @Value("${jdbc.password}")
       private String password;
       //用于创建sql语句执行对象的方法
       @Bean(name = "runner")
       @Scope(value = "prototype")
       public QueryRunner createQueryRunner(@Qualifier("dataSource")DataSource dataSource){
           return new QueryRunner(dataSource);
       }
   
       //创建数据源对象
       @Bean(name = "dataSource")
       public DataSource createDataSource(){
           try{
               ComboPooledDataSource ds = new ComboPooledDataSource();
               ds.setDriverClass(driver);
               ds.setJdbcUrl(url);
               ds.setUser(user);
               ds.setPassword(password);
               return ds;
           }catch(Exception e){
               e.printStackTrace();
           }
           return null;
       }
   }
   ```

4. **进行单元测试**

   ```java
   package com.zbvc.test;
   
   @RunWith(SpringJUnit4ClassRunner.class)
   @ContextConfiguration(classes = SpringConfiguration.class)
   public class StudentServiceTest {
       @Autowired
       private IStudentService service;
       
       @Test
       public void findStudentByIdTest(){
           Student student = service.findStudentById(3);
           System.out.println(student);
       }
   }
   ```

   ==**@Component**注解配置的对象可以使用**@Bean**对其进行覆盖，而**xml文件配置**的对象可以将前两个注解配置的对象进行覆盖==

## 7.Conditional注解

使用在方法和类上，值为数组，用于指定**Condition**的Class类。和`@Bean`注解搭配使用，根据不同的环境创建不同的Bean对象到IOC容器，**Condition**接口定义了*matches*方法，重写*matches*方法可自定义条件判断，当该方法的返回值为true时表示符合条件

- 当使用在方法上时，当*match*方法返回true时，当前被`@Bean`和`@Conditional`修饰的方法的返回值才会被作为Bean对象存入IOC容器
- 当使用在类上时，当match方法返回true时，才会将该类中被`@Bean`注解修饰的方法的返回值作为Bean对象存入IOC容器中

> 示例：实现在Windows和Linux系统下添加不同Bean对象到IOC容器

1. 创建Condition类，实现**Condition**接口，重写*match*方法，方法参数：
   - ConditionContext：判断环境能使用的上下文环境
     - getBeanFactory()：获取当前IOC使用的bean工厂
     - getClassLoader()：获取类加载器
     - getEnvironment()：通过传入key获取当前环境中该key所对应的环境信息value
     - getRegistry()：获取到bean定义的注册类
       - boolean containsBeanDefinition(String beanId)：判断IOC中是否包含某个Bean对象
   - AnnotatedTypeMetadata：注释信息

```java
package com.zbvc.condition;

public class WindowsCondition implements Condition{
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata){
        Environment environment = context.getEnvironment();
        String os = environment.getProperty("os.name");
        if(os.contains("Windows")){
            return true;
        }
        return false;
    }
}
```

```java
package com.zbvc.condition;

public class LinuxCondition implements Condition{
    @Override
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata){
    	Environment environment = context.getEnvironment();
        String os = environment.getProperty("os.name");
        if(os.contains("Linux")){
            return true;
        }
        return false;
    }
}
```

2. 使用`@Conditional`完成在不同操作系统环境下添加不同Bean对象到IOC容器

```java
public class {
    @Bean("bill")
    @Conditional({WindowsCondition.class})
    public Person person1(){
        System.out.println("在容器中添加了bill");
        return new Person("bill",25);
    }
    
    @Bean("Linus")
    @Conditional({LinuxCondition.class})
    public Person person2(){
        System.out.pritnln("在容器中添加了Linus")
        return new Person("Linus",18);
    }
}
```

**Run Configurations---->在VM options添加参数==-Dos.name=linux==**即可将当前环境的*os.name*参数改为Linux进行测试

## 8.Import注解

用于导入其它的配置类，使用该注解标识的配置类为父配置类，导入的为子配置类

>  属性

value：数组类型，用于指定要导入的配置类的Class。

- 若该配置类是配置类或被对象创建相关注解修饰的类，则会将该类中所有Bean对象创建到IOC容器中
- 若是普通类，没有任何注解修饰，则同样会创建到IOC容器中，只是Bean对象的id为该类的全类名
- 若导入的类是**ImportSelector**接口的子类，该类的*selectImports*方法的返回值是一个String数组，数组中的值为类的全类名，这些全类名会被作为Bean对象创建在IOC容器中，通过该方法可以自定义逻辑，返回要导入的组件
- 若导入的类是**ImportBeanDefinitionRegistrar**的子类，该类的*registerBeanDefinitions*方法可手动添加Bean对象到IOC中

> 示例

```java
public class MyImportSelector implements ImportSelector{
    @Override
    //AnnotationMetadata：封装了导入该类的@Import注解的所修饰类的所有注解信息。这个方法可以返回一个空数组，但不能返回一个null否则会报异常
    public String[] selectImports(AnnotationMetadata importingClassMetadata){
    	return new String[]{"com.zbvc.bean.Person","com.zbvc.bean.Student"};
    }
}
```

```java
public class MyImportBeanDefinitionRegistrar implements ImportBeanDefinitionRegistrar{
    @Override
    //BeanDefinitionRegistry：通过该类的方法可以注册Bean对象到IOC容器
    public void registerBeanDefinitions(AnnotationMetadata importingClassMetadata, BeanDefinitionRegistry registry){
        //判断IOC容器中是否有名为teacher的Bean对象
        boolean flag = registry.containsBeanDefinition("teacher");
        if(!flag){
           //指定Bean的定义信息，如Bean的类型，Bean的Scope等等
           RootBeanDefinition beanDefinition = new RootBeanDefinition(Teacher.class);
           //添加Bean对象到IOC
           registry.registerBeanDefinition("teacher", beanDefinition);
        }
    }
}
```

```java
@Configuration
@Import({MyImportSelector.class, MyImportBeanDefinitionRegistrar.class})
public class SpringConfiguration {}
//加载该配置类后，IOC容器中有Person，Student和Teacher的Bean对象
```

## 9.Profile注解

Spring提供的可以根据当前环境动态的激活和切换组件的功能，使用`@Profile`注解，为组件添加环境标识后，该组件就只有在这个环境被激活时才会被注册到IOC容器中，环境标识可随意起，如果标识为default，那么该组件就是默认启用的。可使用在方法上和类上

1. 定义配置文件**dbconfig.properties**

   ```properties
   db.user=root
   db.password=root
   db.driverClass=com.mysql.jdbc.Driver
   ```

2. 编写配置类

   ```java
   @Configuration
   @PropertySource("classpath:/dbconfig.properties")
   public class MainConfig implements EmbeddedValueResolverAware{
       @Value("${db.user}")
       private String user;
       @Value("${db.password}")
       private String password;
       private String driverClass;
       private StringValueResolver resolver;
       @Override
       public void setEmbeddedValueResolver(StringValueResolver resolver){
           this.resolver = resolver;
           driverClass = resolver.resolveStringValue("${db.driverClass}");
       }
       
       @Profile("test")
       @Bean("testDataSource")
       public DataSource dataSourceTest() throws Exception{
           ComboPolledDataSource dataSource = new ComboPooledDataSource();
           dataSource.setUser(user);
           dataSource.setPassword(password);
           dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/db_test");
           dataSource.setDriverClass(driverClass);
       }
       @Profile("prod")
       @Bean("prodDataSource")
       public DataSource dataSourceProd() throws Exception{
           ComboPolledDataSource dataSource = new ComboPooledDataSource();
           dataSource.setUser(user);
           dataSource.setPassword(password);
           dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/db_prod");
           dataSource.setDriverClass(driverClass);
       }
       @Profile("dev")
       @Bean("devDataSource")
       public DataSource dataSourceDev() throws Exception{
           ComboPolledDataSource dataSource = new ComboPooledDataSource();
           dataSource.setUser(user);
           dataSource.setPassword(password);
           dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/db_dev");
           dataSource.setDriverClass(driverClass);
       }
   }
   ```

> 配置环境参数，激活不同组件

**方法一：**

1. 配置环境参数，激活不同组件
   **Run Configuration-->VM options添加-Dspring.profiles.active=xxx**

2. 测试

   ```java
   @Test
   public void test(){
       AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext(MainConfig.class);
   	String[] names = applicationContext.getBeanNamesForType(DataSource.class);
       for(String name : names){
           System.out.println(name);
       }
   }
   ```

**方法二：**使用代码方式切换环境

```java
@Test
public void test(){
    AnnotationConfigApplicationContext applicationContext = new AnnotationConfigApplicationContext();
    //配置环境
    applicationContext.getEnvironment().setActiveProfiles("dev");
    //注册主配置类
    applicationContext.register(MainConfig.class);
    //刷新容器
    applicationContext.refresh();
    String[] names = applicationContext.getBeanNamesForType(DataSource.class);
    for(String name : names){
        System.out.println(name);
    }
}
```



## 10.FactoryBean

```java
public class PersonFactoryBean implements FactoryBean<Person>{
    @Override
    public Person getObject(){
        return new Person();
    }
    @Override
    public Class<?> getObjectType(){
        return Person.class;
    }
    @Override
    public boolean isSingleton(){
        return false;
    }
}
```

```java
@Configuration
public class SpringConfiguration {
    @Bean
    public PersonFactoryBean personFactoryBean(){
        return new PersonFactoryBean();
    }
}
```

==IOC容器中的Bean对象为Person类型，ID为personFactoryBean，因为@Bean注解默认的Bean对象名为当前方法的名称，而FactoryBean的子类只会将getObjet方法的返回值作为Bean对象==

## 11.在Spring的普通Bean对象中获取请求对象

1. 在web.xml中配置监听器

   ````xml
   <listener>
   	<listener-class>org.springframework.web.context.request.RequestContextListener</listener-class>
   </listener>
   ````

2. 通过自动注入将请求对象注入

   ```java
   @Component
   @Aspect
   public class Log{
   	@Autowired
       Private HttpServletRequest request;
   }
   ```
   

# 八.循环依赖

## 1.什么是循环依赖

```java
public class A{
    private B b;
    public B setB(B b) {this.b = b;}
    public B getB(A a) {return b;}
}
```

```java
public class B{
    private A a;
    public A setA(A a) {this.a = a;}
    public A getA() {return a;}
}
```

两个类中相互依赖着对方，Spring在创建其中任意一个类的Bean对象时都必须先有另一个类的Bean对象，导致两个Bean对象都无法创建