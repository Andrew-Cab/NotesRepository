# Maven

Maven项目标准目录结构：

- src/main/java	核心代码部分
- src/main/resources     配置文件部分
- src/test/java      测试代码部分
- src/test/resource    测试配置文件
- src/main/webapp    页面资源，js，css，图片等等
- target/classes  编译后的class文件目录
- target/test/classes   编译后的测试class文件目录
- pom.xml  maven工程配置文件

创建Web工程时选择使用骨架创建出的项目结构更完善，创建java工程时不选择使用骨架创建出的结构更完善

Maven常用命令：执行命令时需要进入pom.xml所在的目录

- mvn clean      删除target目录，target目录存放编译的文件，导入其他人的项目前要先删除他编译的文件，因为环境不同编译出的文件可能运行不了
- mvn compile   编译src中的源码，生成target目录
- mvn test   编译测试代码，同时也会编译核心代码，分别放在target目录下的classes和test-classes
- mvn package   编译核心代码，测试代码。只将项目的核心代码打成war包，放在target目录下，而测试代码不参与打包
- mvn install    除了做package命令的操作外，还会将war包放在本地仓库中
- mvn deploy   将项目打包，并部署到私服

> 安装第三方jar包到本地仓库，以fastjson-1.1.37.jar为例：

在待安装jar包的目录下进入cmd，执行**mvn install:install-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 -Dfile=fastjson-1.1.37.jar -Dpackaging=jar**

> **IDEA集成Maven**

settings--->搜索maven--->在Maven home directory项指定maven安装目录--->在User Settings file中指定maven的settings.xml位置---->在Runner页面中的VM Options中填写参数**-DarchetypeCatalog=internal**可以在不联网的情况下使用之前使用过的骨架

##1.Maven配置

> 配置阿里云镜像

1. 找到maven的settings.xml文件

2. 配置本地仓库：<localRepository>仓库路径</localRepository>，默认为==用户家目录/.m2/repository==

3. 找到<mirrors>标签添加阿里云镜像：

   ```xml
   <mirror>
   	<id>alimaven</id>
       <name>aliyun maven</name>
       <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
       <mirrorOf>central</mirrorOf>
   </mirror>
   ```

> 在settings.xml中配置编译器

```xml
<profile>
	<id>jdk-1.8</id>
    <activation>
    	<activeByDefault>true</activeByDefault>
        <jdk>1.8</jdk>
    </activation>
    <properties>
    	<maven.compiler.source>1.8</maven.compiler.source>
    	<maven.compiler.target>1.8</maven.compiler.target>
        <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>
    </properties>
</profile>
```



##2.Maven生命周期

> ==Maven生命周期其实就是描述了一个项目从源代码到部署的整个周期==

- 清洁（clean）：为执行下面工作所做的必要清理，就是删除out文件夹
- 默认（default）：真正进行项目编译打包等工作的阶段
- 站点（site）：生成项目报告，站点，发布站点

> ==默认（default）的生命周期==

1. 验证（validate）：验证该项目是否正确，所有必要的信息可用
2. 编译（compile）：编译项目的源代码
3. 测试（test）：使用合适的单元测试框架测试编译的源代码，这些测试不应该要求代码被打包或部署
4. 打包（package）：采用编译的代码，并以其可分配的格式（如jar）进行打包
5. 验证（verify）：对集成测试的结果执行任何检查，以确保满足质量标准
6. 安装（install）：将软件包安装到本地存储库中，用作本地其他项目的依赖项
7. 部署（deploy）：在构建环境中完成，将最终的包复制到远成存储库以与他开发人员和项目共享（私服）

## 3.Maven依赖范围

maven工程会将**src/main/java**和**src/main/resources**文件夹下的文件全部打包在**classpath**中，运行时它们两个的文件夹下的文件会被放在一个文件夹下，类加载器会从classpath中加载配置文件和class文件。web项目会将src打包放在WEB-INF目录下的classes文件夹中。普通java项目会在META-INF文件夹下的文件中告诉jvm执行的时候去哪个类中找main方法

```java
Manifest-Version:1.0
Main-Class: com.xinzhi.HelloWorld
```

maven项目在不同阶段引入的依赖是不同的，例如：

- 编译时，maven会将与编译时相关的依赖引入classpath中
- 测试时，maven会将与测试时相关的依赖引入classpath中
- 运行时，maven会将与运行时相关的依赖引入classpath中

而依赖范围就是用来控制依赖于这三种classpath的关系

> scope标签就是依赖范围的配置

该项默认配置compile，可选配置还有==test、provided、runtime==、system、import。

- servlet-api.jar是运行时不需要的，因为tomcat里有，但编译时是需要的，因为编译时没有tomcat环境
- junit.jar是运行时不需要的，只有在测试时才会用到
- jdbc驱动jar包是测试时必须要有的，编译时不需要，编译时用的都是jdk中的接口，运行时才会使用反射注册驱动

###3.1编译范围（compile）

该范围就是默认依赖范围，此依赖范围对于编译、测试、运行三种**classpath**都有效，例如：项目中由fastjson的依赖，那么fastjson不管是在编译、测试、运行时都会被用到，因此fastjson必须是编译范围，该范围的jar包会参与项目打包

```xml
<dependency>
	<groupId>com.alibaba</groupId>
    <artifactId>fastjson</artifactId>
    <version>1.2.68</version>
</dependency>
```

### 3.2测试依赖范围（test）

使用此范围的依赖，只对测试classpath有效，在编译主代码和项目运行时，都将无法使用该依赖，最典型的就是==junit==，构建在测试时才需要，因此它的依赖范围需要显示指定为<scope>test</scope>，当然不指定也不会报错，但是该依赖会被加入到编译和运行的classpath中，造成不必要的浪费

```xml
<dependency>
	<groupId>junit</groupId>
    <artifactId>junit</artifactId>
    <version>4.7</version>
    <scope>test</scope>
</dependency>
```

### 3.3已提供依赖范围（provided）

使用该范围的依赖，只对编译和测试的classpath生效，对运行的classpath无效，最典型的例子就是==servlet-api==和==jsp-api==，编译和测试该项目的时候都需要提供该依赖，但运行时，web容器已经提供了该依赖，所以运行时就不需要此依赖，如果不显示指定该依赖范围，并且容器依赖的版本不一致的话，可能会引起版本冲突，造成不良影响，该依赖范围的jar会在打包的时候被忽略

```xml
<dependency>
	<groupId>javax.servlet</groupId>
    <artifactId>javax.servlet-api</artifactId>
    <version>4.0.1</version>
    <scope>provided</scope>
</dependency>
```

### 3.4运行时依赖范围（runtime）

使用该依赖范围只对测试和运行的classpath有效，对编译的classpth无效，最典型的就是==JDBC驱动==，项目主代码编译的时候只需要用到JDK中提供的JDBC接口，只有在测试和运行的时候才需要实现上述接口的具体的JDBC驱动

```xml
<dependency>
	<groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>5.1.25</version>
    <scope>runtime</scope>
</dependency>
```

## 4.依赖的传递

jar其实就是别人写的工程，他也会依赖其他的jar包，传递性让我们可以不用关心我们所依赖的jar包依赖了哪些jar，只要我们添加了依赖，他就会自动将他所依赖的jar全部依赖进来，所以非compile范围的依赖不能传递

> 依赖传递原则

- **最短路径优先原则**：如果A依赖于B，B依赖于C，在B和C中同时有log4j的依赖，并且这两个版本不一致，那么A会根据最短路径优先原则，在A中会传递来B的log4j版本

- **路径相同先声明原则**：如果我们的工程同时依赖于B和A，A和B没有依赖关系，但都有D的依赖，其版本不一致，那么会引入在pom.xml中先声明依赖D的版本

  ```xml
  <dependency>
  	<groupId>com.xinzi</groupId>
      <artifactId>B</artifactId>
      <version>1.5.3</version>
  </dependency>
  <dependency>
  	<groupId>com.xinzi</groupId>
      <artifactId>A</artifactId>
      <version>1.12.5</version>
  </dependency>
  ```

  这样B所依赖的D的jar就会被传递依赖到项目中，但若A.jar所依赖的D.jar版本高与B.jar所依赖的D.jar，由于项目中导入的D.jar版本过低，可能会出现A.jar不能正常使用的情况

- **依赖的排除**：解决上一个原则中版本不兼容问题，只需将低版本的jar排除即可

  ```xml
  <dependencies>
  	<dependency>
  		<groupId>com.xinzi</groupId>
      	<artifactId>B</artifactId>
      	<version>1.5.3</version>
  		<exclusions>
              <exclusion>
              	<artifactId>com.xinzhi</artifactId>
              	<groupId>D</groupId>
              </exclusion>
          </exclusions>
      </dependency>
      
  	<dependency>
  		<groupId>com.xinzi</groupId>
      	<artifactId>A</artifactId>
      	<version>1.12.5</version>
  		</dependency>
  </dependencies>
  ```

##5.聚合和继承

> **聚合模块（父模块）的打包方式必须为pom，否则无法完成构建**

父模块通常不需要写代码，只是用来管理子模块，所以不需要使用骨架创建，并把src目录删去。在聚合多个项目时，如果这些被聚合的项目中需要引入相同的jar，那么可以将这些jar写入父pom中，各个子项目自动继承该pom。写在**<dependencyManagement>**标签中的**<dependencies>**标签，其中的**<dependency>**标签不会被子模块自动继承，需要在子模块中引用，引用时不加版本号，自动使用父类的版本。在父项目中，申明在**<dependencyManagement>**中的jar包不会被导入在父项目中，**<dependencyManagement>**标签只是起一个jar包版本统一管理的功能。在父项目的pom文件中添加标签<packaging>pom</packaging>。然后在父工程中创建新的maven子模块，想要对已经存在的模块指定一个父工程只需在这个模块中进行如下配置：

```xml
<parent>
    <!--父工程坐标-->
	<groupId>com.zbvc</groupId>
    <artifactId>Parent</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <!--以当前文件为基准的父工程pom.xml文件的相对路径-->
    <relativePath>../Parent/pom.xml</relativePath>
</parent>
```

每一个Module都是一个Artifact，不同的Module之间的代码需要相互调用时，只需要引入其Module的坐标即可导包调用代码。每一个Artifact就是jar包的本质，导入jar包的依赖后，该模块或项目在编译后的目录中会有被导入的jar包中的完成项目结构，虽然在IDEA内的项目结构中没有显示；在IDEA中创建的与jar包位于相同路径下的资源会最终打包在同一个目录下。模块化开发就是将为dao层，service层，web层都单独创建一个模块，在service模块中添加dao模块的坐标，在web模块添加service模块的依赖进行开发

由于Maven在构建父工程时会首先从本地仓库中查找父工程的pom文件中声明的所有依赖，如果本地仓库中没有则会去远程仓库中寻找，如果都没有找到则项目构建失败。所以启动父工程时必须首先保证父工程pom文件的所有依赖在本地仓库中可以找到，为了避免手动一个一个安装我们自己创建的子模块，可以在父工程中设置配置聚合，配置完毕后直接安装父工程即可自动将聚合的子工程安装到本地仓库

```xml
<models>
    <!--子模块相对于当前父工程的路径-->
	<model>../Son</model>
</models>
```



## 6.POM文件

### 6.1基础配置

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
 xmlns:xsi="http://www.w3.org/2001/XML_Schema-instance"
 xsi:schemaLocation="http://maven.apache.org/POM/4.0.0http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<!--模型版本，maven2.0必须这样写，现在是maven2唯一支持的版本-->
    <modelVersion>4.0.0</modelVersion>
    <!--公司或组织的唯一标志，并且配置时生成的路径也是由此生成，如com.xinzhi，maven会将该项目打成的jar包放在本地路径：/com/xinzhi-->
    <groupId>com.xinzhi</groupId>
    <!--本项目的唯一ID，一个groupId下面可能由多个项目，就是靠artifactId来区分的-->
    <artifactId>test</artifactId>
    <!--本项目目前所处的版本号-->
    <version>1.0.0-SNAPSHOT</version>
    <!--打包机制，如pom、jar、war，默认为jar-->
    <packaging>jar</packaging>
    <!--为pom定义一些常量，在pom中的其它地方可以直接引用，使用方式如下：${file.encoding}-->
    <!--常量用来整体控制一些依赖的版本号，标签名称任意-->
    <properties>
    	<file.encoding>UTF-8</file.encoding>
        <junit-version>4.12</junit-version>
        <java.source.sersion>1.8</java.source.sersion>
        <java.target.version>1.8</java.target.version>
    </properties>
    <!--定义本项目的依赖关系，就是依赖的jar包-->
    <dependencies>
    	<!--每一个dependency都应该对应一个jar包-->
        <dependency>
        	<!--一般情况下，maven是通过groupId、artifactId、version三个标签值（俗称坐标）来检索该构件，然后引入你的工程，如果别人想引用你现在开发的这个项目（前提是以开发完毕并且发布到了远程仓库），那么就需要在他的pom文件中新建一个dependency标签，将本项目的groupId、artifactId、version写入，maven就会把你上传的jar包下载到他的本地-->
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>${junit-version}</version>
            <!--依赖范围-->
            <scope>complie</scope>
            <!--设置依赖是否可选，默认为false，即子项目默认都继承，如果为true，则子项目必须显示引入-->
            <optional>false</optional>
            <!--依赖排除-->
            <exclusions>
           	 	<exclusion>
            		<artifactId>org.slf4j</artifactId>
            		<groupId>slf4j-api</groupId>
            	</exclusion>
       		</exclusions>
        </dependency>
    </dependencies>
</project>
```

Maven在版本管理时可以使用几个特殊字符SNAPSHOT，LATEST，RELEASE。如”1.0.5-SNAPSHOT“

- SNAPSHOT：这个版本一般用于开发过程中，表示不稳定版本
- LATEST：只某个特定构件的最新发布，这个发布可能是一个发布版，也可能是一个SNAPSHOT版，具体看哪个时间最后
- RESEALE：指最终版本，稳定版

### 6.2构建配置

```xml
<build>
	<!--产生的构建的文件名，默认值是${artifactId}-${version}-->
    <finalName>myPorjectName</finalName>
    <!--构建产生的所有文件存放的目录，默认为${basedir}/target,即项目根目录下的target-->
    <directory>${basedir}/target</directory>
    <!--项目源码目录，当前构建项目的时候，构建系统会编译目录里的源码，该路径是相对于pom.xml的相对路径-->
    <sourceDirectory>${basedir}/src/main/java</sourceDirectory>
    <!--项目单元测试使用的源码目录，当前测试项目的时候，构建系统会编译目录里的源码，该路径是相对于pom.xml的相对路径-->
    <testSourceDirectory>${basedir}/src/test/java</testSourceDirectory>
    <!--被编译过的应用程序class文件存放的目录-->
    <testOutputDirectory>${basedir}/target/classes</testOutputDirectory>
    <!--以上配置都有默认值，就是约定好目录就这么建-->
    
    <!--加上此处配置可以使放在java下的配置文件也能生效-->
    <resources>
    	<resource>
        	<directory>src/main/java</directory>
            <includes>
            	<include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
        <resource>
        	<directory>src/main/resources</directory>
            <includes>
            	<include>**/*.properties</include>
                <include>**/*.xml</include>
            </includes>
            <filtering>false</filtering>
        </resource>
    </resources>
    
    <!--单元测试相关的所有资源路径，配置方法于resources类似-->
    <testResources>
    	<testResource>
        	<targetPath />
            <filtering />
            <directory />
            <includes />
            <excludes />
        </testResource>
    </testResources>
    
    <!--使用的插件列表-->
    <build>
    	<plugins>
    		<plugin></plugin>
    	</plugins>
    </build>
    
    <!--主要定义插件的共同元素，扩展元素集合，类似于dependencyManagement，所有继承于此项目的子项目都能使用，该插件配置直到被引用时才会被解析或绑定到生命周期，给定插件的任何本地配置都会覆盖这里的配置-->
    <pluginManagement>
    	<plugins></plugins>
    </pluginManagement>
</build>
```

### 6.3仓库配置

```xml
<repositories>
	<repository>
    	<id>alimaven</id>
        <name>aliyun maven</name>
        <url>http://maven.aliyun.com/nexus/content/groups/public</url>
        <release>
        	<enabled>true</enabled>
        </release>
        <snapshots>
        	<enabled>false</enabled>
        </snapshots>
    </repository>
</repositories>
```

在pom.xml里配置和在settings.xml中配置仓库的功能是相同的，主要区别是pom中的配置是对于本工程的

### 6.4项目配置

```xml
<!--项目名称，Maven产生的文档用-->
<name>baseon-maven</name>

<!--项目主页的URL，Maven产生的文档用-->
<url>http://www.clf.com</url>

<!--项目的描述信息，Maven产生的文档用，当这个元素能够用HTML格式描述时，（例如CDATA中的文本会被解析器忽略，就可以包含HTML信息），不鼓励使用纯文本描述。如果需要修改产生的web站点的索引页面，你应该修改你自己的索引页文件，而不是调整这里的文档-->
<description>A maven project to study maven</description>

<!--项目开发者列表-->
<developers>
	<developer>
        <!--开发者唯一标识符-->
    	<id></id>
        <!--开发者新名-->
        <name></name>
        <!--开发者邮件-->
        <email></email>
		<!--项目开发者主页的URL-->
        <url  />
        <!--项目开发者在项目中扮演的角色-->
        <roles>
        	<role>Project Manager</role>
        </roles>
        <!--开发者所属组织-->
        <organization></organization>
        <!--项目开发者所属组织url-->
        <organizationUrl></organizationUrl>
        <!--项目开发者属性-->
        <properties>
        	<dept></dept>
        </properties>
    </developer>
    
<!--项目的其他贡献者列表-->
<contributors>
   <!--子标签参考开发者列表--> 
</contributors>
</developers>
```

##7.maven插件

Maven实际上是一个依赖插件执行的框架，每个任务实际上是由插件完成的，Maven插件通常被用来：

- 打包jar文件
- 创建war文件
- 编译代码文件
- 代码单元测试
- 创建工程文档
- 创建工程报告

插件通常提供了一个目标的集合，并且可以使用下面的语法执行：

```java
mvn [plugin-name]:[goal-name]
```

例如，一个java工程可以使用maven-complier-plugin的compile-goal编译，使用以下命令

```java
mvn compoler:compile
```

###7.1.maven-compiler-plugin

设置maven编译器版本，如果settings.xml中没有指定的话，maven3默认用jdk1.5，maven2默认用jdk1.3

```xml
<plugins>
	<plugin>
		<groupId>org.apache.maven.plugins</groupId>
    	<artifactId>maven-compiler-plugin</artifactId>
    	<version>3.1</version>
    	<configuration>
    		<source>1.8</source><!--源代码使用的jdk版本-->
        	<target>1.8</target><!--需要生成的目标class文件的编译版本-->
        	<encoding>UTF-8</encoding><!--字符集编码-->
    	</configuration>
	</plugin>
</plugins>
```

### 7.2.tomcat

maven默认有tomcat6插件，可以再添加一个新的tomcat插件。使用**tomcat:run**可以运行tomcat6插件，执行**tomcat7:run**可以运行tomcat7插件

```xml
<plugins>
	<plugin>
    	<groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
        <configuration>
        	<port>8888</port>
        </configuration>
    </plugin>
</plugins>
```

###7.3.war包插件

```xml
<plugin>
	<groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-war-plugin</artifactId>
    <configuration>
    	<resource>
        	<directory>src/main/webapp/WEB-INF</directory>
            <filtering>true</filtering>
            <targetPath>WEB-INF</targetPath>
            <includes>
            	<include>web.xml</include>
            </includes>
        </resource>
    </configuration>
</plugin>
```

