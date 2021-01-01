#一.SpringMVC入门

**SpringMVC和Struts2的优劣势：**

**1.共同点：**

1. 它们都是表现层框架，都是基于MVC模型编写的。
2. 它们底层都离不开原始的Servlet
3. 他们处理请求的机制都是一个核心控制器

**2.区别：**

1. SpringMVC的入口时Servlet，而Struts2使Filter
2. SpringMVC是基于方法设计的，而Struts2是基于类，Struts2每次执行都会创建一个动作类。所以SpringMVC会稍微比Struts2快一些
3. SpringMVC使用更加简洁，同时还支持JSR303，处理ajax的请求更方便
   JSR303是一套JavaBean参数校验的标准，它定义了很多常用的校验注解，我们可以直接将这些注解加载我们的JavaBean的属性上面，就可以在需要校验的时候进行校验了
4. Struts2的OGNL表达式使页面的开发效率相比SpringMVC更高些，但执行效率没有比JSTL提升，尤其是Struts2的表单标签，远没有html执行效率高



Tomcat引擎接收客户端请求，封装代表请求的resq和代表响应的resp，从web应用中调用请求资源（Servlet），Servlet分为==共有行为==和==特有行为==，而SpringMVC将共有行为封装为==前端控制器==，对于前端控制器，所有web层框架都有该功能，不同框架使用的前端控制器技术不同，SpringMVC使用的是**Servlet**，Struts2使用的是**Filter**

**使用步骤：**

1. 导入spring-webmvc包

2. 在web.xml中配置SpringMVC前端控制器DispathcerServlet

   ```xml
   <servlet>
       <servlet-name>dispatcherServlet</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--配置springmvc文件的加载，如果不指定，则默认加载"/WEB-INF/前端控制器名-servlet.xml"的配置文件-->
       <init-param>
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springmvc.xml</param-value>
       </init-param>
       <!--配置在服务器启动时创建该servlet对象加载配置文件-->
       <load-on-startup>1</load-on-startup>
   </servlet>
   <servlet-mapping>
       <servlet-name>dispatcherServlet</servlet-name>
       <!--配置为“/”让所有请求来时都先执行该Servlet，不包括jsp，“/*”包括jsp-->
       <url-pattern>/</url-pattern>
   </servlet-mapping>
   ```

3. 创建Controller类（只是一个普通的类即可）和视图页面

4. 将Controller使用`@Controller`注解配置到Spring容器中

5. 配置springmvc.xml来配置组件扫描及内部视图解析器，内部视图解析器是框架中的类，可以设置访问路径的属性

   ```XML
   <context:cmoponent-scan base-package="com.zbvc.controller"/>
   <bean id="viewResolver" class="org.springframework.web.servlet.view.InternalResourceViewResolver">
   	<property name="prefix">/WEB-INF/view/</property>
       <property name="suffix">.jsp</property>
   </bean>
   ```

   

> ==**SpringMVC的执行流程：**==

1. tomcat启动创建Servlet容器，添加前端控制器，调用前端控制器的**init**方法加载springmvc.xml，创建IOC容器并将容器放在**ServletContext**域中，再将controller创建在IOC容器中。通过配置前端控制器Servlet的**url-pattern**为**“/”**，使得用户请求先发送至前端控制器DispatcherServlet。==若将url-pattern配置为“**/**”，则会将除jsp页面外的所有请求全部拦截，包括图片，外部js文件，外部css文件等，前端控制器没有处理静态资源的能力。==

   > 解决方法：

   **方法一：**

   可在配置文件中添加**<mvc:resource location="" mapping=""/>**来告诉前端控制器当收到某种类型的请求后去哪里找资源，同时添加注解驱动<mvc :annotation-driven/>

   - location：资源在项目中的位置，如：/static/表示项目根目录下的css目录下的所有资源
   - mapping：加载页面的静态资源时同样会根据引入资源时所配置的路径向服务器请求资源，该属性是用来配置静态资源url。如：/static/\*\*，表示所有**虚拟目录/static**的请求都去*location*属性配置的资源路径查找资源

   **方法二：**

   1. 在springmvc.xml中配置\<mvc:default-servlet-handler/>，该标签会注册**SimpleUrlHandlerMapping**组件，该组件会将所有请求都交给tomcat的默认Servlet处理，所以静态资源可以访问
   2. 添加注解驱动\<mvc:annotation-driven/>，该标签会注册**RequestMappingHandlerMapping**组件，这个组件的*handlerMethods*属性保存着Controller的信息，且当请求来时先由该处理器来处理请求，这个组件不能处理就交给**SimpleUrlHandlerMapping**组件来处理

   **满足url-pattern的请求都会由前端控制器处理，如果前端控制器没有找到可以处理对应请求的处理器，浏览器会抛出404，控制台会打印No mapping found警告**

2. DispatcherServlet收到请求调用`getHandler()`根据当前请求在HandlerMapping[^1]找到可以处理这个请求的Controller

3. DispatcherServlet调用`getHandlerAdapter()`根据当前Controller找到这个Controller的适配器

4. 调用适配器的`handle()`方法执行具体的Controller方法

5. 适配器执行完成Controller后返回ModelAndView，不管Controller返回什么，经过适配器执行后都会转为ModeAndView，对于返回`void`的Controller会指定一个默认的视图名

6. HandlerAdapter将controller执行结果ModelAndView返回给DispatcherServlet

7. DispatcherServlet将ModelAndView传给ViewReslover视图解析器

8. ViewResolver解析后返回具体的View

9. DispatcherServlet根据View进行渲染视图（即将模型数据填充之视图中）。DispatcherServelt响应用户



> SpringMVC九大组件（DispatcherServlet中的成员变量）

1. 文件上传解析器：`private MutilpartResolver multipartResolver`
2. 区域信息解析器：`private LocaleResolver localeResolver`
3. 主题解析器：`private ThemeResolver themeResolver`
4. Handler映射信息：`private List<HandlerMapping> handlerMappings`
5. Handler的适配器：`private List<HandlerAdapter> handlerAdapters`
6. 异常解析器：`private List<HandlerExceptionResolver> handlerExceptionResolvers`
7. `private RequestToViewNameTranslator viewTranslator`
8. 允许重定向携带数据的功能：`private FlashMapManager flashMapManager`
9. 视图解析器：`private List<ViewResolver> viewResolver`

九大组件由DispatcherServlet的`onRefresh()`在服务器启动时初始化

# 二.请求参数的绑定

==1.绑定机制：==
	表单提交数据是通过键值对的形式，SpringMVC的参数绑定过程是把表单提交的请求参数，为Controller中方法的参数进行对应赋值，因此要求表单的键名和方法的参数名必须相同。如果方法的参数是一个对象，则要求表单的键名与对象的**属性名**相同，框架会自动为我们封装，可以定义多个POJO类型的参数，框架会创建出每个POJO对象并根据表单的键名查找对象的**属性名**为其属性赋值。如果参数对象中还存在引用类型数据**user**，将表单的**name**命名为=="user.属性名"==即可将表单中该键所对应的value封装到**user**对象中。如果属性是集合类型，则将表单的**name**命名为**list[索引].集合中元素的属性名**或**map['键名'].集合中元素的属性名**。从页面获取到的数据都是String，框架在封装数据时会自动帮我们完成大多数数据类型的转化。

```html
<form>
    <input name="list[0].name"/><br/>
    <input name="list[0].age"/>
</form>
```

==2.post请求中封装出现的中文乱码问题：==

在web.xml中添加spring提供的编码过滤器（固定配置方法），编码过滤器一定要配在所有过滤器最前面。该过滤器对于响应只是设置了编码类型，若要正确响应数据到页面还需要通过`response.setContentType("text/html")`设置内容类型

```xml
<web-app>
	<filter>
    	<filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <!--设置项目使用的编码-->
        <init-param>
        	<param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <!--以下可配可不配-->
        <!--强制请求对象使用项目的编码-->
        <init-param>
        	<param-name>forceRequestEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
        <!--强制响应对象使用项目的编码格式-->
        <init-param>
        	<param-name>forceResponseEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
        <!--
		直接配置forceEncoding也可以设置请求编码和响应编码为项目编码
		<init-param>
        	<param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
		-->
    </filter>
    
    <filter-mapping>
    	<filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
</web-app>
```

==3.自定义类型转化器：==
对于普通数据类型，SpringMVC可以完成自动类型转换，但日期型等特殊类型需要自定义类型转换器

1. 编写类型转化器的类

```java
/*
	必须实现Converter接口，第一个泛型是指待转换的数据类型，第二个是转换为什么数据类型
*/
public class StringToDate implements Converter<String,Date>{
    public Date convert(String source){
        if(source == null){
            throw new RuntimeException("请传入数据");
        }
        DateFormat df = new SimpleDateFormat("yyyy-MM-dd");
        try{
            return df.parse(source);
        }catch(ParseException e){
            throw new RuntimeException(e.printStackTrace());
        }
    }
}
```

2. 在springmvc.xml配置文件中配置自定义类型转换器，**Converter**是**ConversionService**中的组件，而**ConversionService**是由**ConversionServiceFactoryBean**创建的，在这个FactoryBean中有`Set<?> converters`属性用于创建**ConversionService**对象。指定框架使用我们自己创建的**ConversionService**组件，而不是框架默认的组件。

```xml
<bean id="conversionService" class="org.springframework.context.support.ConversionServiceFactoryBean">
	<property name="converters">
    	<set>
            <!--注入自定义类型转换器-->
        	<bean class="com.zbvc.utils.StringToDate"/>
        </set>
    </property>
</bean>
<!--使自定义转换器生效-->
<mvc:annotation-driven conversion-service="conversionService"/>
```

==4.获取Servlet原生API：==

我们编写的Controller不需要实现Servlet接口，只是一个普通的方法。当我们需要获取到**request**和**response**对象时，只需要在该方法中加上**HttpServletRequest**和**HttpServletResponse**两个参数，框架会自动将这两个对象传入该方法。**HttpSession**、**java.security.Principal**、**Locale**、**InputStream**、**OutputStream**、**Reader**、**Writer**都可以由框架来传入

==5.日期格式化：==

使用`@DateTimeFormat`注解修饰类的成员变量并指定日期格式，将这个类作为Controller的参数，当框架对请求传递的参数进行自动封装时会验证请求参数值的格式是否与对应成员变量`@DateTimeFromat`注解所声明的格式一致，如果一致则进行封装，否则抛出404异常。

**注：**如果在springmvc.xml中注册自定义类型转换器时使用的是**ConversionServiceFactoryBean**创建的组件时，该组件默认没有格式化器，无法进行格式化处理。如果要使用格式化处理还要自定义转换器，则创建**ConversionService**组件时改用**org.springframework.format.support.FormattingConversionServiceFactoryBean**



# 三.常用注解

##1.@RequestMapping

==作用：==

用于建立请求URL和处理请求方法（Controller类中的方法）之间的对应关系

==位置：==

- 类上。请求URL的第一级资源目录
- 方法上。请求URL的第二级资源目录，与类上的`@RequestMapping`注解所配置的一级目录共同构成资源路径

==属性：==用于限制什么请求可以被该Servlet处理

- ==value：==用于指定请求资源路径，它和*path*属性的作用一样，类型为数组，可以一个方法处理多个请求。URL支持使用*****表示任意多个字符,******表示任意多级目录,**?**任意一个字符，如：/*/test??/**/user。如果请求与模糊地址和精确地址同时匹配，则精确地址优先；同时匹配多个模糊地址，则较精确的地址优先
- ==method：==用于指定请求方式，通过枚举类**RequestMehod**进行指定。例如：**method = RequestMehod.GET**
- ==params：==用于指定请求参数的限制条件。它支持简单的表达式。要求请求的参数的key和value必须和参数配置的一摸一样
  **例如：**
  **params={"accountName"}**，表示请求参数必须有accountName
  **params={"money!=100"}**，表示请求参数中money不能是100
  **params={"!account" , "username=admin"}**，表示请求参数必须不能有account属性，必须有username属性，且username必须等于admin

## 2.@RequestParam

==作用：==

把请求中指定名称的参数给控制器中的形参赋值，可以解决表单提交的键名和参数名不同而不能传入的情况

==位置：==

Controller方法参数前

==属性：==

- ==value：==请求参数中所提供的键名
- ==defaultValue：==为方法参数设置默认值
- ==required：==请求参数中是否必须提供此参数。默认值：**true**，表示必须提供，不提供将报错，若设置了**value**属性而不指定该属性值为false，则**参数中必须有和value属性值相同名称的请求参数（键名）**



## 3.@RequestBody

==作用：==

用于获取请求体内容，直接使用得到的是**key=value**结构的字符串数据，**get请求方式可能会报错**。也可以导入jackson相关jar包，接收请求体的参数使用POJO对象来接收，框架可以通过jackson提供的相关方法将获取到的字符串数据的键名匹配对象的属性来封装到接收请求体的参数对象中

==位置：==

Controller方法参数前

==属性：==

- ==required==：是否必须有请求体。默认值：**true**，表示必须有请求体，get请求会报错；如果取值为**false**，get请求会得到**null**



## 4.@RequestHeader

==作用：==

用于获取指定请求头消息

==位置：==

要把请求头消息封装到方法的哪个参数，就写在哪个参数前

==属性：==

- ==value：==提供消息头名称
- ==required：==是否必须有此消息头
- ==defaultValue：==指定默认值



## 5.@CookieValue

==作用：==

用于把指定的cookie名称的值传入控制器方法参数

==属性：==

- ==value：==指定的cookie名称
- ==required：==是否必须有此cookie
- ==defaultValue==



##6.@ModelAttribute

==作用：==

用于修饰controller层中的方法，被**@RequestMapping**注解修饰的方法会被视为一个Servlet，而被**@ModelAttribute**注解修饰的方法会在所有Servlet执行前执行。该注解可以修饰方法也可以修饰参数。

- 出现在方法上，表示当前方法会在所有控制器的方法执行之前执行，它可以修饰没有返回值的方法，也可以修饰有具体返回值的方法。当**有具体的返回值**时，可以使用`ModelAttribute`为该返回值指定一个key，返回值作为值保存在**BindingAwareModeMap**中，在controller方法可以使用该注解指定key修饰一个参数来将值赋给这个参数。如果在存键值对到**BindingAwareModeMap**时，没有指定key，则默认将返回值类型第一个字母小写作为key

  ```java
  @Controller
  public class Test{
      @ModeAttribute("book")
      public Book getBook(){
          Book book = new Book("java核心技术");
          return book;
      }
      
      @RequestMapping("/test")
      public String test(@ModelAttribute("book") Book book){
          System.out.prinltn(book);
          return "success";
      }
  }
  ```

- 出现在参数前，表示获取指定的数据给参数赋值。当被该注解修饰的方法**没有具体返回值**时，可以在被该注解修饰的方法中添加一个Map类型的参数，存入该集合的参数在接下来执行的controller方法中通过使用该注解添加**value属性**修饰某个参数，就可以将Map里指定key的值赋给该参数。或者在controller中也定义一个Map参数，框架会将`@ModelAttribute`注解修饰的方法的参数Map传入controller方法中Map类型参数，因为Map类型的参数框架会在底层创建一个**BindingAwareModeMap**类型对象来存储数据

  ```java
  @Controller
  public class Test{
      @ModeAttribute
      public void getBook(Map<String, Object> map){
          Book book = new Book("java核心技术");
          map.put("book",book);
      }
      
      @RequestMapping("/test")
      public String test(@ModelAttribute("book") Book book){
          System.out.prinltn(book);
          return "success";
      }
  }
  ```

==属性：==

- ==value：==用于获取数据的key。key可以是POJO的属性名，也可以是map结构的key



## 7.@SessionAttributes

==作用：==

用于多次执行控制器方法间的参数共享。在controller方法中添加一个**Model**类型的形参，框架会自动为该参数赋予一个对象，调用该对象的`addAttribute(String key,Object value)`方法设置键值对，底层会帮我们将该键值对存入**request域**中。然后在该方法的类上写该注解指定*value*属性（数组型，可指定多个值），即可将属性值所对应的request域中的键值对在**session域中再存一份**，这样其它方法就可以通过在参数中添加**ModelMap（Model的子类）**型参数，调用其`get(String key)`方法来获取到session域中该键所对的值。若在方法中添加**SessionStatus**型的形参，调用其`setComplete()`方法即可清除session中的键值对

==位置：==

类上

==属性：==

- ==value：==用于指定要将request域中**哪个键名**的键值对复制到session域中
- ==type：==用于指定要将request域中**哪个类型的值**的键值对复制到session域中

```java
@Controller
@SessionAttributes(value={"msg1","msg2"})
public class Controller{
    @RequestMapping("/Test")
    public ModeAndView test(ModeAndView modeAndView){
        modeAndView.addObject("msg1","msg1");
        modeAndView.addObject("msg2","msg2");
        modeAndView.setViewName("success");
        return modeAndView;
    }
}
```



## 8.@PathVariable

==作用：==用于获取REST风格URL的请求值

==位置：==controller方法参数前

**普通URL写法：**localhost:8080/springMVC/test?id=100&username=admin

**REST风格URL写法：**localhost:8080/springMVC/test/100/admin

```java
@RequestMapping("/test/{id}/{username}")
public String testREST(@PathVariable("id")Integer id, @PathVariable("username")String username){
    System.out.println("id:"+id+",username:"+username);
}
```

# 四.响应

##1.页面跳转

==通过设置Controller方法的返回值来指定要跳转的资源类，无论Controller写什么返回值，经过Adapter处理后都返回的是ModelAndView==

1. **返回值为String类型：**
   用于指定执行完毕该Controller方法后需要跳转的资源，该字符串写的就是资源的路径，还可以在springmvc.xml的配置文件中添加视图解析器，指定跳转时资源路径的前后缀，避免多次书写相同的资源目录和文件后缀名。还可以通过在资源路径前添加**forward:或redirect:**来指定是用什么方式跳转，默认为转发，但若添加该关键字则不会走视图解析器，路径写法与普通请求转发相同。而重定向只需要“/资源路径”即可，不需要写项目名，框架会自动拼接项目名
可以在方法中传入**Map**类型的参数，用于存储数据，该**Map**会被存放在Request域中转发到页面，在jsp页面使用**域名.Map中的key名**即可获取key所对应的值。还可以传入**Model**和**ModeMap**使用*addAttribute()*来存储键值对
   
2. **返回值为ModelAndView类型：**

   - *addObject()*：用于向ModelAndView对象中添加键值，最终是添加到了request域中并转发
   - *setViewName()*：用于设置视图名称，会根据视图解析器前后缀配置进行跳转，视图名同样可以使用**forward:**和**redirect:**关键字，如果使用**redirect:**关键字进行重定向时，框架会将**ModelAndView**中存放的简单数据作为字符串拼接到重定向页面的url请求后

   ```java
   @RequestMapping("/test")
   public ModelAndView test(){
       ModelAndView modelAndView = new ModelAndView();
   	modelAndView.addObject("username","wujianqi");
   	modelAndView.setViewNmae("success");
   	return modelAndView;
   }
   ```

3. **返回值是void类型：**
   默认将controller方法的`@RequestMapping`配置的url作为视图名进行跳转。通常用来处理ajax请求，通过**HttpServletResponse**响应jason数据。我们也可以通过接收**HttpServletRequest**对象来自己转发或重定向，==自己转发和重定向的路径不会经过springmvc配置文件的视图解析器，路径要写对==

4. **\<mvc:view-controller>：**当一个请求只是需要访问一个位于WEN-INF目录下的页面时，除了写一个Controller进行跳转外，还可以在springmvc配置文件中添加该标签直接跳转WEB-INF目录下的页面

   - *path*：指定哪个请求
   - *view-name*：指定跳转到哪个视图，该页面跳转也会走视图解析器进行前后缀拼接，所以路径写法和返回值为String的Controller相同

   ```xml
   <mvc:view-controller path="/login" view-name="login"/>
   <!--开启mvc注解驱动模式，不配置该标签会导致除了/login请求外的普通请求无法正常执行-->
   <mvc:annotation-driven/>
   ```

##2.回写数据

- **在方法中接收HttpServletResponse参数，回写方法与Servlet中相同**

- **@ResponseBody：**
  ==作用：==位于方法上或方法返回值声明前。将方法返回值作为响应体响应到浏览器，如果返回的是字符串就只返回这个字符串，而不会进行页面跳转。如果是对象，则将Java对象转为JSON数据进行响应通常用于处理AJAX请求

  ==属性：==*produce*：指定响应的MIME类型“**text/plain;charset=utf-8**”，默认使用“text/plain;charset=ISO-8859-1”，响应字符串到浏览器时会出现乱码
  将`@RestController`注解写在类上相当于写了`@ResponseBody`和`Controller`两个注解

  > 使用步骤

  1. 编写controller

     ```java
     @RequestMapping("/ajaxTest")
     public @ResponseBody Student ajaxTest(String name, int age){
         Student student = new Student(name, age);
         return student;
     }
     ```
     
  2. 在springmvc.xml中配置注解驱动

     ```xml
     <mvc:annotation-driven/>
     <!--该注解会创建HttpMessageConverter接口的八个实现类对象，包括MappingJackson2HttpMessageConverter，该实现类使用了jackson类库实现java对象与jason的转换-->
     ```
  
- **HttpEntity<String>：**可以声明为Controller方法的参数，会自动封装此次请求的请求头和请求体

- **ResponseEntity<String>：**可以声明为Controller方法的返回值，该类型为自定义的响应体，可以设置响应状态码、响应头、响应体等，泛型为响应体中的数据类型

  ```java
  @RequestMapping("/test")
  public ResponseEntity<String> test(){
      String body = "ResponseEntity返回值测试";
      MultiValueMap<String, String> headers = new HttpHeaders();
      headers.add("set-cookie","username=zs");
      return new ResponseEntity<String>(body, headers, HttpStatus.OK);
  }
  ```

  

##3.数据流转

1. 请求经过适配器的`handle()`方法后会创建一个**BindingAwareModelMap**类型的隐含模型，并且会首先将当前Controller上`@SessionAttribute`设置的所有键值对存入这个隐含模型中
2. 其次获取到所有标注`@ModelAttribute`的方法，判断这个方法的参数类型，如果是Map、Model类型或其子类型，则将之前创建的**BindingAwareModelMap**赋值给参数；如果是不是，则判断参数前是否有`@RequestMappin,@RequestHeader`等参数绑定相关注解，如果有就根据注解进行参数绑定。然后调用`@ModeAttribute`修饰的方法，拿到方法的返回值，将`@ModelAttribute`注解的*value*属性值作为key，方法返回值作为value存入隐含模型，如果注解没有指定*value*属性，则将方法的返回值类型首字母小写作为key。如方法返回`Void`时：键为void，值为Null
3. 之后运行处理请求的Controller方法，同样先判断这个方法的参数类型。如果是Map、Model类型或其子类型，则将之前操作的隐含模型赋值给参数。如果是一个`@ModelAttribute`修饰的自定义类型参数，则拿到该注解*value*属性的值作为key；如果注解没有设置*value*属性，则拿到被注解修饰的参数类型的首字母小写作为key，从隐含模型中进行key的匹配，如果有则取出隐含模型中key所对应的value赋值给参数；没有则判断是否是该Controller上`@SessionAttributes`标注的属性，如果是则从session中取出，拿到的值为null就抛异常，因此不建议使用`@SessionAttributes`。
4. 如果步骤3中判断是不属于`@SessionAttributes`注解声明的属性或没有`@ModeAttribute`修饰的方法，对于自定义POJO类型的对象就是创建一个该POJO类型的对象根据请求参数的key与POJO类的属性名进行匹配封装

# 五.文件上传

==文件上传最终都是上传至服务器中部署的项目结构中，不是工作空间的项目，路径要写对==

> ==传统解析上传到服务器文件的方法==

1. 导入jar包

   ```xml
   <dependency>
   	<groupId>commons-fileupload</groupId>
       <artifactId>commons-fileupload</artifactId>
       <version>1.3.1</version>
   </dependency>
   <dependency>
   	<groupId>commons-io</groupId>
       <artifactId>commons-io</artifactId>
       <version>2.4</version>
   </dependency>
   ```

2. 编写上传文件的JSP页面

   ```html
   <h2>文件上传</h2>
   <form action="user/fileupload" method="post" enctype="multipart/form-data">
       选择文件:<input type="file" name="upload"/><br/>
       <input type = "submit" value="上传文件"/>
   </form>
   ```

3. 编写Controller类

   ```java
   @Controller
   @RequestMapping("/user")
   public class Controller{
       @RequestMappint("/fileUpload")
       public String fileUpload(HttpServletRequest request) throws Exception{
           //设置上传的位置，File.separator用于获取路径分隔符
           String path = request.getSession().getServletContext().getRealPath("upload") + File.separator;
           File file = new File(path);
           if(!file.exists()){
               file.mkdirs();
           }
           
           //解析request对象获取上传文件项
           DiskFileItemFactory factory = new DiskFileItemFactory();
           ServletFileUpload upload = new ServletFileUpload(factory);
           //该方法会将请求体提交的表单的每一个表单项封装为一个FileItem
           List<FileItem> items = upload.parseRequest(request);
           for(FileItem item : items){
               if(item.isFormFiled()){
                   //说明是普通表单项
                   System.out.println("表单提交的key："+item.getFileName()+",表单提交的值："+item.getString());
               }else{
                   //说明是上传文件项,获取上传文件的名称
                   String fileName = item.getName();
                   String uuid = UUID.randomUUID().toString().replace("-","");
                   fileName = uuid + "_" + fileName;
                   //完成文件上传
                   item.write(new File(path,fileName));
                   //删除临时文件
                   item.delete();
               }
           }
           return "success";
       }
   }
   ```

>  ==SpringMVC文件上传==

文件上传时是将文件封装到request对象中，经过前端控制器，调用**文件解析器**，返回一个上传文件对象给Controller中的方法，在Controller方法中提供==MultipartFile==类型参数接收，调用该对象中的方法完成文件上传，该参数的名字必须和表单中文件输入框的name相同。配置文件解析器时id也必须为**multipartResolver**，因为框架是根据Bean的ID获取Bean对象

1. 配置文件解析器

   ```xml
   <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
   	<!--设置文件最大大小，单位为字节-->
   	<property name="maxUploadSize" value="10485760"/>
       <!--设置默认编码，防止中文乱码-->
       <property name="defaultEncoding" value="utf-8"/>
   </bean>
   ```

2. 编写Controller
  
      ```java
      @Controller
      @RequestMapping("/user")
      public class Controller{
          @RequestMappint("/fileUpload")
          public String fileUpload(HttpServletRequest request, @RequestParam("upload")MultipartFile upload) throws Exception{
              //设置上传的位置
              String path = request.getSession().getServletContext().getRealPath("upload") + File.separater ;
              File file = new File(path);
              if(!file.exists()){
                  file.mkdirs();
              }
              System.out.println("文件项的名字为："+upload.getName());
              //获取文件名，文件名为"xxx.文件格式"
              String name = upload.getOriginalFilename();
              String fileName = name.substring(fileName.lastIndexOf("."))
              String uuid = UUID.randomUUID().toString().replace("-","");
              fileName = uuid + "_" + fileName;
              //完成文件上传
              upload.transferTo(new File(path,fileName));
              return "success";
          }
      }
      ```

> ==SpringMVC跨服务器上传文件==

1. 搭建文件上传服务器：新建web项目，在webapp目录下新建uploads目录，服务器端口号修改，部署项目到服务器，启动服务器

2. 导入jar包

   ```xml
   <dependency>
   	<groupId>com.sun.jersey</groupId>
       <artifactId>jersey-core</artifactId>
       <version>1.18.1</version>
   </dependency>
   <dependency>
   	<groupId>com.sun.jersey</groupId>
       <artifactId>jersey-client</artifactId>
       <version>1.18.1</version>
   </dependency>
   ```

3. 在springmvc.xml中配置文件解析器

   ```xml
   <!--id必须为multipartResolver-->
   <bean id="multipartResolver" class="org.springframework.web.multipart.commons.CommonsMultipartResolver">
       <!--设置文件解析的编码，要与页面的pageEncoding保持一致-->
   	<property name="defaultEncoding" value="UTF-8"/>
   </bean>
   ```

4. 编写Controller代码

   ```java
   @Controller
   @RequestMapping("/user")
   public class Controller{
       @RequestMappint("/fileUpload")
       public String fileUpload(MultipartFile upload) throws Exception{
           //定义上传的文件服务器地址
           String path = "http://localhost:文件服务器端口号/uploads/";
           String fileName = upload.getOriginalFilename();
           String uuid = UUID.randomUUID().toString().replace("-","");
           fileName = uuid + "_" + fileName;
           //创建客户端对象
           Client client = Client.create();
           //和图片服务器进行连接
           WebResource webResource = client.resource(path + fileName);
           //完成文件上传
           webResource.put(upload.getBytes());
       }
   }
   ```

# 六.SpringMVC异常处理

如果服务器底层代码异常通过向上抛出处理，那么当出现异常时，异常就会被一层一层抛到前端控制器，再由前端控制器抛给客户端，最终显示**500**服务器端异常。可以通过编写异常处理类，实现**HandlerExceptionResolver**类，配置跳转的错误页面，在springmvc.xml中配置==异常处理器组件==。当产生异常时前端控制器会调用异常处理类执行逻辑代码，这种方式叫统一全局异常处理

##1.自定义异常处理器

> 自定义异常类

```java
package com.zbvc.exception;

public class SysException extends Exception{
	public SysException(){
        super();
    }
    public SysException(String message){
        super(message);
    }
}
```

> 编写controller

```java
@Controller
public class UserController {
    @RequestMapping(path="/hello")
    public String sayHello() throws SysException {
        System.out.println("Hello World");
        try {
            int a = 10/0;
        } catch (Exception e) {
            e.printStackTrace();
            //向上抛出异常，由前端控制器处理，跳转到错误页面
            throw new SysException("服务器崩溃");
        }
        return "success";
    }
}
```

> 编写异常处理类

- 方法一：继承**HandlerExceptionResolver**接口

  ```java
  package com.zbvc.handler;
  
  public class SysExceptionResolver implements HandlerExceptionResolver {
      /*
      参数：
      	Exception：表示项目的controller/service/dao抛出的异常
      */
      @Override
      public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
          SysException exception = null;
          if(e instanceof  SysException){
              exception = (SysException)e;
          }else{
               exception = new SysException("系统正在维护");
          }
          ModelAndView mv = new ModelAndView();
          mv.addObject("errorMsg",exception.getMessage());
          mv.setViewName("error");
          return mv;
      }
  }
  ```

- 方法二：使用注解

  - `@ControllerAdvice`：使用在类上，声明该类为全局异常处理类，类中方法的编写和controller中的相同
  - `@ExceptionHandler`：使用在方法上，*value*属性声明什么类型的异常要交给该方法处理，不写*value*表示其它异常，方法上设置**Exception**类型参数可以接收到此次异常对象。
  可写于某个Controller中的方法上，此时当这个Controller出现异常时，就会尝试使用这个Controller中的异常处理方法进行处理；如果修饰的方法位于被`@ControllerAdvice`修饰的类中，则当任何类出现异常时都可以尝试使用全局异常处理方法来处理。
    如果一个Controller方法出现异常时，优先使用局部异常处理方法。如果有多个方法可以处理此次异常，则异常声明精确度高的优先
  
  ```java
  package com.zbvc.handler;
  
  @ControllerAdvice
  public class SysExceptionResolver {
      
      @ExceptionHandler(value=SysException.class)
      public ModelAndView resolveException(Exception e) {
          SysException exception =  (SysException)e;
          ModelAndView mv = new ModelAndView();
          mv.addObject("errorMsg",exception.getMessage());
          mv.setViewName("error");
          return mv;
      }
      
      @ExceptionHandler
      public ModelAndView resolveException(Exception e) {
          ModelAndView mv = new ModelAndView();
          mv.addObject("errorMsg",e.getMessage());
          mv.setViewName("error");
          return mv;
      }
}
  ```

  > 注册异常处理类

  - 注册实现接口的处理类
  
    ```xml
    <beans>
    	<bean id="sysExceptionResolver" class="com.zbvc.handler.SysExceptionResolver"/>
        <!--配置该语句后默认配置了SpringMVC中的处理器映射器，处理器适配器，视图解析器三大组件-->
        <mvc:annotation-driven/>
  </beans>
    ```

  - 注册注解实现处理类
  
    ```xml
    <beans>
    	<context:component-scan base-package="com.zbvc.handler"/>
        <mvc:annotation-driven/>
    </beans>
    ```

## 2.SimpleMappingExceptionResolver

在springmvc.xml中配置该异常处理器，为*exceptionMappings*属性赋值，该属性为properties集合，key用于指定异常的全限定类名，value用于指定当出现该异常时跳转的页面，跳转页面时同样会走视图解析器，同时异常对象会以exception的名字存放在request域中

```xml
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
	<property name="exceptionMappings">
    	<props>
        	<prop key="java.lang.NullPointerException">error</prop>
        </props>
    </property>
</bean>
```



# 七.拦截器

**拦截器和过滤器的区别：**

**过滤器**是Servlet规范的一部分，任何javaweb工程都可以使用

==拦截器==是SpringMVC框架的，只有使用框架的工程才可以使用

**过滤器**在url-pattern中配置了“/*”之后，可以对所有要访问的资源拦截

==拦截器==只会拦截访问的控制器方法，如果请求没有被前端控制器接收则不会走拦截器；如果访问的是jsp，html，css，image或者js是不会进行拦截的

**过滤器**在请求从客户端发向前端控制器时生效

==拦截器==生效于前端控制器到controller方法之间

> 使用步骤

1. 编写拦截器，实现==HandlerInterceptor==接口，或继承==HandlerInterceptorAdapter==，该类对接口的三个抽象方法进行了空实现，可以只重写需要的方法

   ```java
   public class MyInterceptor implements HandlerInterceptor {
       /**
        * 预处理方法，在controller方法执行前执行
        * Object handler 被拦截的控制器对象
        * return true表示放行，执行下一个拦截器，没有则执行controller方法
        *		  false表示截断，拒绝访问controller，之后所有方法都不执行
        */
       @Override
       public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
           System.out.println("拦截器预处理方法执行了");
           return true;
       }
   
   	//后置处理方法，在Controller方法执行后，视图解析器执行前执行，如果preHandle返回false或执行途中出现异常该方法都不会执行，ModelAndView是controller方法的返回值
       @Override
       public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
           System.out.println("后置拦截方法执行");
       }
       
    	//最后要执行的方法，即视图解析器执行后（页面渲染完成之后）执行，只要这个拦截器的preHandle返回tue该方法就会执行，即使中途出现异常，页面渲染时异常也会执行，因为在源码中这个方法的执行写在catch中
       @Override
       public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
           System.out.println("最后的拦截方法执行");
       }
   }
   ```

2. 在springmvc.xml中配置拦截器

   ```xml
   <beans>
       <!--配置拦截器-->
       <mvc:interceptors>
           <mvc:interceptor>
               <!--要拦截的具体方法，/**表示全部拦截，/user/*表示只拦截一级请求是user的请求。注：一级请求是指资源路径，不包括虚拟目录-->
               <mvc:mapping path="/hello/**"/>
               <!--不拦截的方法：<mvc:exclude-mapping path=""/>-->
               <!--配置拦截器对象-->
               <bean class="com.zbvc.interceptor.MyInterceptor"/>
   <!--如果拦截器对象通过注解加入IOC容器中，在使用<ref bean="beanId">注册-->
           </mvc:interceptor>
           <!--配置多个拦截器时，拦截器由上至下组成拦截器链
   		当第一个返回false，第二个返回false时，只有第一个的preHandle会执行
   		当第一个返回true，第二个返回false时，preHandle都执行，postHandle都不执行，第一个的afterCompletion执行-->
       </mvc:interceptors>
   </beans>
   ```

**执行流程总结：**接收到请求后，依次执行拦截器链中的`preHandle()`并记录该拦截器，一旦其中一个拦截器的`preHandle()`返回了false，则马上执行该拦截器中的`afterCompletion()`，然后逆序执行被记录的拦截器的`afterCompletion()`。如果执行拦截器链、执行目标方法或页面渲染时出现异常也马上执行`afterCompletion()`

# 八.REST风格

```xml
<filter>
	<filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

**HttpMethodFilter**由框架提供。由于html中的标签只能发送GET和POST两种请求。所以针对html只能发送两种请求，该过滤器的*doFilterInternal*方法会先从请求中获取一个name为**"_method"**的参数的值，先判断此次请求是否是POST，且*_method*参数的值有长度，将参数的值转为大写后传入**HttpMethodRequestWrapper**的内部类的构造方法中，该内部类重写了*getMethod*方法，将*_method*参数的值返回，相当于用*_method*的值替换了原来的POST请求。所以html使用REST风格的请求必须为POST请求且有*_method*参数。而AJAX可以发送8种请求，所以不需要使用过滤器进行转换

通过`@RequestMapping`注解的*method*属性指定请求的方式，**HttpMethodFilter**可以将以不同请求方式发送的同一个请求分配给不同的controller方法，路径上的参数占位符通过`@PathVariable`来获取，请求体中的数据的绑定方式与普通请求参数的绑定方式相同

> 实例

```html
<body>
    <a href="testREST/100">测试GET</a><br/>
    
    <form action="testREST" method="POST">
        <input type="submit" value="测试POST"/>
    </form><br/>
    
    <form action="testREST" method="POST">
        <input type="hidden" name="_method" value="PUT"/>
        <input type="submit" value="测试PUT"/>
    </form><br/>
    
    <form action="testREST/100" method="POST">
        <input type="hidden" name="_method" value="DELETE"/>
        <input type="submit" value="测试DELETE"/>
    </form>
    
    <!--AJAX-->
    <script type="text/javascript" src="/static/js/jquery.js"></script>
    <script type="text/javascript">
    	function test(){
            $.ajax({
                url:"testAJAX_DELETE"
                type:"DELETE"
                data:"{id:1001}"
                dataType:"JSON"
                success:function(data){
                	alert(data);
            	}
            })
        }
    </script>
    <input type="button" value="测试AJAX" onclick="test()"/>
</body>
```

```java
public class RESTController{
    @RequestMapping(value="/testREST/{id}", method=RequestMethod.GET)
    public String query(@PathVariable("id")Integer id){
        System.out.println("query:"+id);
        return "success";
    }
    @RequestMapping(value="/testREST", method=RequestMethod.POST)
    public String insertUser(){
        System.out.println("insert");
        return "success";
    }
    @RequestMapping(value="/testREST", method=RequestMethod.PUT)
    public String updateUser(){
        System.out.println("update");
        return "success";
    }
   @RequestMapping(value="/testREST/{id}", method=RequestMethod.DELETE)
    public String deleteUser(@PathVariable("id")Integer id){
        System.out.println("delete:"+id);
        return "success";
    }
    
    //AJAX
   @RequestMapping(value="/testAJAX_DELETE/{id}",method=RequestMethod.DELETE)
    public void testAJAX(@PathVariable("id")Integer id){
        System.out.println("AJAX_DELETE"+id);
    }
}
```

**注：**tomcat8.0及以上对jsp页面有约束，不能接收POST和DELETE请求，需要在jsp页面的<%@ page %>指令中添加**isErrorPage=true**属性

# 九.JSR303

**JSR303**是java为bean数据合法性校验提供的标准框架，通过在类的成员变量上添加注解对数据合法性进行校验

|           注解            |                        功能                        |
| :-----------------------: | :------------------------------------------------: |
|           @Null           |                被注释变量必须为null                |
|         @NotNull          |                被注释变量不能为null                |
|        @AssertTrue        |                被注释变量必须为true                |
|        @AsserFalse        |               被注释变量必须为false                |
|        @Min(value)        | 被注释变量必须是一个数字，值必须大于等于注解中的值 |
|        @Max(value)        | 被注释变量必须是一个数字，值必须小于等于注解中的值 |
|    @DecimalMin(value)     | 被注释变量必须是一个数字，值必须大于等于注解中的值 |
|    @DecimalMax(value)     | 被注释变量必须是一个数字，值必须小于等于注解中的值 |
|      @Size(max,min)       |          被注释变量的大小必须在指定范围内          |
| @Digits(integer,fraction) |   被注释变量必须是一个数字，值必须在可接受范围内   |
|           @Past           |          被注释变量值必须是一个过去的日期          |
|          @Future          |          被注释变量值必须是一个将来的日期          |
|     @Pattern(regexp)      |           被注释变量值必须满足正则表达式           |

**Hiberate Validator**jar包是JSR303的一个参考实现，除了支持标准校验注解外还支持以下扩展注解：

|             注解             |              功能              |
| :--------------------------: | :----------------------------: |
|            @Email            |   被注释变量值必须是电子邮箱   |
| @Length(min=value,max=value) | 被注释字符串必须在在指定范围内 |
|          @NotEmpty           |      被注释字符串不能为空      |
|            @Range            | 备注是变量的值必须在合适范围内 |

所有注解都可以设置*message*属性来定制错误信息

> 使用步骤

1. 导入hibernate-validator的jar包
2. 在类成员变量上添加校验注解
3. 在Controller方法中该类型的参数前添加`@Valid`注解告知SpringMVC在封装该对象时进行数据校验
4. 在这个参数后紧跟一个**BindingResult**参数框架会将前一个参数的校验结果封装进去，所以如果有多个封装参数，就需要在每个参数后都加**BindingResult**参数

```java
public class Employee{
    @Email(message="邮箱格式不正确")
    @Length(min=6,max=12)
    private String email;
    //getter和setter
}
```

```java
@Controller
public String test(@Valid Employee employee,BindingResult result){
    System.out.println(employee);
    boolean hasErrors = result.hasErrors();
    System.out.println(hasErrors);
    if(hasErrors){
        List<FieldError> errors = result.getFieldErrors();
        for(FieldError fieldError : errors){
            System.out.println("错误的字段："+fieldError.getField());
            System.out.println("错误提示消息："+fieldError.getDefaultMessage());
        }
    }
}
```



[^1]: 该组件在IOC容器启动时扫描每个Controller类上的注解，使用LinkedHashMap保存了每个Controller类能处理哪些请求