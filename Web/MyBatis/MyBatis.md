# 一.主配置文件

> **mybatis-config**

```xml
<configuration>
    <!--
    <settings>
        <setting name="logImpl" value="LOG4J"/>
    </settings>
    导入log4j.jar在src定义log4j.properties的配置文件后，可定义该标签来打开MyBatis的log4j
    -->
    <properties resource="jdbcConfig.properties"></properties>
    <environments default="data_mysql">
        <environment id="data_mysql">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
        
		<environment id="data_oracle">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="${oracle.driver}"/>
                <property name="url" value="${oracle.url}"/>
                <property name="username" value="${oracle.username}"/>
                <property name="password" value="${oracle.password}"/>
            </dataSource>
        </environment>
    </environments>

    <mappers>
        <mapper resource="com/zbvc/mappers/StudentMapper.xml"/>
    </mappers>
</configuration>
```

> **标签顺序**

1. **properties**，**settings**
2. **typeAliases**，**typeHandlers**
3. **objectFactory**，**objectWrapperFactory**
4. **plugins**
5. **enviroments**，**databaseIdProvider**，**mappers**

> **标签作用**

- **<properties>：**引入外部properties配置文件，引入后可以使用**"${键名}"**来获取配置信息
  *resource：*引入配置文件
  *url：*该属性为引入网络资源或磁盘路径下的资源，路径需要为url格式，将文件拖入浏览器即可得到该文件的url：file:///D:/workspace/IDEA_workspace/MyBatisTest/src/main/resources/jdbcConfig.properties

- **<environments>：**配置多个环境信息
  *default：*指定使用什么环境

- **<environment>：**配置环境信息
  *id：*当前环境的唯一标识，例如测试环境（test），开发环境（develop），生产环境（product）

  - **<transactionManager>：**
    *type：*配置事务类型，可选值：
    1. JDBC：JDBC原生事务管理方式
    2. MANAGED：把事务管理转交给其他容器
  - **<dataSource>：**
    *type：*配置数据源，可选值：
    1. UNPOOLED：每次使用都获取一个新的连接
    2. POOLED：MyBatis实现的连接池共有两个：空闲池和活动池，当获取连接时会先判断空闲池是否还有多余链接，有则取出，没有则判断活动池中连接数是否达到最大，没有则创建一个链接进去并返回，否则返回池中最早的链接
    3. JNDI

- **<typeAliases>：**

  - **<typeAlias>：**为数据类型起别名，mapper.xml中可使用该数据类型的别名
    *type：*取值为数据的全类名
    *alias：*别名，默认为类名（不区分大小写）
  - **<package>：**
    *name：*包名，为当前包及子包的每一个类都起一个默认别名。如果子包下有同名的类，使用该方法会导致别名冲突，可以单独在其中一个类上使用`@Alias("别名")`指定新的别名

- **<typeHandler>：**注册类型处理器，用于将数据从java类型转换为数据库类型

- **<plugins>：**插件

- **\<settings>：**用于配置MyBatis运行时行为

  - **<setting>：**
  *name：*属性名
    *value：*属性值
    
    |         属性          |          值           | 作用                                                         |
    | :-------------------: | :-------------------: | ------------------------------------------------------------ |
  |  lazyLoadingEnabled   | true/false，默认false | 使用分步查询后，设置该属性开启懒加载                         |
    | aggressiveLazyLoading | true/false，默认true  | 该属性为true时，所有具备可懒加载的属性只要其中某个被需要时就会全部加载，否则，属性就会按需加载。==将该属性置为false，开启懒加载后实现真正的懒加载== |
    
    

- **<databaseIdProvider>：**配置MyBatis可以支持的数据库类型
  *type：*==DB_VENDOR==

  - **<property>：**
    *name：*数据库厂商名
    *value：*为数据库厂商起别名

  ```xml
  <databaseIdProvider type="DB_VENDOR">
  	<property name="MySql" value="mysql"/>
      <property name="Oracle" value="oracle"/>
      <property name="SQL Server" value="sqlserver"/>
  </databaseIdProvider>
  ```

  配置好后，在mapper.xml文件中的sql语句标签中添加*databaseId*属性，设置值为数据库厂商的别名，该语句就只会在MyBatis的数据源是该厂商提供的数据源时执行； 如果同时找到带有 `databaseId` 和不带 `databaseId` 的相同语句，则后者会被舍弃。

- **\<mappers>：**注册映射文件

  - **<mapper>：**
    *resource：*mapper文件的路径
    *class：*若使用注解配置dao的mapper，通过该属性指定接口的全类名
  - **<package>：**批量注册mapper文件，要求mapper文件在**产品中（即out文件夹）**必须和dao接口在同一文件夹下
    *name：*包路径

# 二.Mapper文件

```xml
<mapper namespace="com.zbvc.dao.IStudentDao">
    <select id="selectAll" resultType="com.zbvc.domain.User" databaseId="mysql">
    	SELECT * FROM users WHERE id = #{id}
    </select>
    
    <insert id="saveStudent" parameterType="com.zbvc.domain.Student" uesGeneratedKeys="true" keyProperty="id">
        INSERT INTO student VALUES(null,#{number},#{name});
    </insert>
</mapper>
```

## 1.SQL标签属性

> **namespace**和**id**

将**namespace**指定为接口的全类名，**id**指定为接口中的方法名，实现接口和mapper文件绑定。通过获取session对象并调用**getMapper()**方法，传入接口的class对象作为参数，MyBatis就可以通过以上配置创建接口的代理对象，该对象由框架通过sql语句标签的id值与接口的方法对应完成实现

> **resultType**

该属性用于指定将该sql执行的结果封装为什么数据类型，即接口方法的返回值类型，mybatis会根据类型中的属性名与数据库中的字段名进行匹配后自动封装。

- 在增删改的sql语句中不需要写该属性，框架会根据接口的返回值类型自动封装结果，接口返回**int**，框架封装的值表示受影响的行数，接口返回**boolean**，当影响0行时，框架封装**false**，否之**true**。
- 如果返回的是Collection集合，那么该属性指定为集合中的数据类型。
- 如果是Map集合，该属性值为value的数据类型，mybatis会根据value的数据类型自动进行封装，通过在方法上加`@MapKey()`来设置Map集合使用value对象的什么属性作为key
- 如果将该属性设置为map，则会将查询结果的列名作为map的key，该列的数据作为value封装成一个Map

```java
@MapKey("id")
public Map<Integer,Employee> getEmployeeByLastName(String lastName);
```

```xml
<select id="getEmployeeByLastName" resultType="com.zbvc.domain.Employee">
	SELECT * FROM tb_employee WHERE last_name LIKE #{lastName}
</select>
```

```java
    @Test
    public void test(){
        Map<Integer,Employee> employees = mapper.getEmployeeByLastName("%jian%");
        System.out.println(employees)
    }
```

> **ResultMap**

设置自定义结果集映射，属性值为自定义结果集映射的id

> **parameterType**

接口参数的全类名，可以省略。

在SQL标签的sql语句中获取接口中的参数值：

- 若参数类型是对象，在sql语句中使用**#{属性名}**获取参数对象中该属性的值。
- 若参数类型是Map，**#{key}**获取键所对应的value
- 参数为基本类型或String且只有一个，**#{}**中变量名可随意写
- 若有多个参数，获取参数时共有三种方法：
  - 在接口的每个参数前通过写`@Param("参数名")`声明参数名称，然后在sql语句中通过**#{}**获取，==注解中的参数名可以与接口中的参数名不同，只要获取时注解中的名字与**#{}**中的名字相同即可==
  - 在**#{}**中使用==param1，param2...paramN==代表从1到N的参数。eg：#{param1}
  - 使用参数的索引获取对应位置的参数，索引从0开始

**使用#{}和${}获取参数的区别：**

- \#{}
  1. 获取参数可以在其中写参数索引或param1，param2...
  2. SQL语句使用?占位符的方式执行
- ${}：可以用于SQL语句中表名的动态获取
  1. SQL语句的执行为直接将参数值拼接到sql语句中
  2. 需要根据情况使用引号将${}包裹，因为这个表达式不会自动为字符串添加引号导致SQL语法错误
  3. 如果接口中只有一个基本数据类型或String类型的参数，则无法通过在${}中写任意内容来获取。因为写在${}中的内容会被认为是接口参数的一个属性，而如果参数类型中没有该属性的getter就会报错。想要获取基本类型和String类型参数，表达式的内容只能是*value*或*_parameter*
  4. 如果写参数索引则就是一个数字拼接，即不支持索引获取，但可以使用parame1，parame2...来获取
  5. 如果接口参数是Map，同样可以使用**${key}**来获取值

**#{}的其它用法：**

jdbcType：当我们执行的SQL语句数据库某些字段被传入null时，有些数据库会不支持mybatis对null的默认处理。如Orcale（报错）。因为mybatis对null类型会默认处理为JDBC中的**OTHER**类型，Oracle并不支持这种类型数据

> **useGeneratedKeys**

设置自增主键获取策略

> **keyProperty**

指定获取到的自增主键赋值给参数对象的哪个属性。对于支持主键自增长的数据库，只需设置**keyProperty**和**useGeneratedKeys**两个属性即可获取到新增主键的值。也可以使用**<insert>**的子标签**<selectKey>**来查询主键，该标签属性：

- *order：**BEFORE***，**AFTER**。指定**<selectKey>**标签定义的查询主键语句执行与SQL标签的插入语句之前还是之后，对于mysql数据库来说，主键形成于插入数据之后，所以取值为**AFTER**。而Oracle主键自增值存于数据库的序列中，需要先从序列中获取到新主键的值，在再插入数据，所以是**BEFORE**。对于非自增的可以先查询出当前的最大值然后加1，将该属性设置为**BEOFRE**，让这个加1的值先设置到对象中再执行插入语句就可以完成自增
- *keyColumn：*指定数据库中的自增字段名，可以省略
- *keyProperty：*指定返回的新的自增主键赋值给接口参数的哪个属性

```xml
<insert id="saveStudent" parameterType="com.zbvc.domain.Student" databaseId="mysql">
    INSERT INTO student VALUES(null,#{number},#{name});
    <selectKey keyProperty="id" keyColumn="id" resultType="int" order="AFTER">
        select last_insert_id();
    </selectKey>
</insert>
<insert id="saveStudent" parameterType="com.zbvc.domain.Student" databaseId="oracle">
    INSERT INTO student VALUES(null,#{number},#{name});
    <selectKey keyProperty="id" resultType="int" order="BEFORE">
        select STUDENT_SEQ.nextval from dual;
    </selectKey>
</insert>
```

> **flushCache**

每个增删改标签的该属性的值默认都为**true**，查询标签默认值为**false**，表示一旦执行此类标签就会==同时清空一级缓存和二级缓存的数据==

> useCache

用于设置该查询标签是否使用二级缓存，该属性不会影响一级缓存，一级缓存默认开启

## 2.自定义结果集映射

**<resultMap>：**声明自定义结果集映射
	*type：*将数据映射为什么类型的数据
	*id：*结果集映射的唯一标识

- **<id>：**对数据库中的主键列进行映射配置

- **<result>：**对数据库的普通列进行映射配置
  *column：*值为数据库中的字段名
  *property：*值为*type*属性指定的对象的属性

  ```xml
  <resultMap type="com.zbvc.domain.Employee" id="employee">
  	<id column="employee_id" property="id"/>
      <result column="employee_email" property="email"/>
      <result column="employee_gender" property="gender"/>
  </resultMap>
  ```

- **<association>：**关联映射，如果封装的结果中存在对象类型的属性，可以使用该标签进行该对象类型的属性的封装
  
  *property：*指定联合对象的属性名
  
  *javaType：*指定该联合对象的类型
  

*select：*该属性可以将其它SQL标签的执行结果作为值直接赋给*property*属性所指对象的属性中，如果该SQL标签跨命名空间，则可以通过==命名空间+ID==指定，通过该属性可以实现分步查询，即分别由两个sql语句预处理对象执行两条sql语句。该属性和*javaType*功能相同，只需设置其中一个属性即可

  *column：*当*select*属性指定的SQL标签有参数时，通过该属性指定参数，属性值为使用该自定义结果集映射的SQL语句标签所查出的字段名；如果有多个参数时，该属性的值要设置为=="{key1=value1 , key2=value2}"==的形式，其中key指*select*属性引用的sql语句的**#{}**中所写的参数名称，value指使用该自定义结果集映射的SQL标签所查出的字段名

  *fetchType：*可选值**"lazy"，"eager"**。分别用于配置局部的延迟加载和立即加载

  - **<id>：**为联合对象在数据库中的的主键进行映射
  - **<result>：**为联合对象在数据库中的普通列进行映射

  > **联合对象结果集映射配置**

  ```xml
  <mapper namespace="com.zbvc.dao.IEmployeeDao">
  	<resultMap id="employeeMap">
      	<id column="employee_id" property="id"></id>
          <result column="employee_email" property="email"></result>
          <result column="employee_gender" property="gender"></result>
          <association property="department" javaType="com.zbvc.domain.Department">
          	<id column="department_id" property="departmentId"></id>
              <result column="department_name" property="departmentName"></result>
          </association>
      </resultMap>
    <select id="getEmployeeById" resultMap="employeeMap">
      	SELECT * FROM tb_employee e INNER JOIN tb_department d ON e.department_id = d.id WHERE e.id=#{id}
    </select>
  </mapper>
  ```

> **association分步查询**

  Employee对象中的属性：int id，String email，String gender，Department department

  tb_employee字段：employee_id，employee_email，employee_gender，department_id

  ```xml
  <mapper namespace="com.zbvc.dao.IEmployeeDao">
  	<resultMap id="employeeMap" type="com.zbvc.domain.Employee">
      	<id column="employee_id" property="id"></id>
          <result column="employee_email" property="email"></result>
          <result column="emoloyee_gender" property="gender"></result>
          
          <association property="department" select="com.zbvc.dao.IDepartmentDao.getDepartmentById" column="department_id"/>
          <!--该语句可理解为：将getEmployeeById查出的department_id字段作为参数传递给getDepartmentById标签语句，将查询结果封装为Department对象赋值给Employee对象的department属性-->
      </resultMap>
    <select id="getEmployeeById" resultMap="employeeMap">
      	SELECT * FROM tb_employee WHERE id=#{id}
      </select>
  </mapper>
  ```

  ```xml
  <mapper namespace="com.zbvc.dao.IDepartmentDao">
	<select id="getDepartmentById" resultType="com.zbvc.domain.Department">
      	SELECT * FROM tb_department WHERE id=#{id}
      </select>
  </mapper>
  ```

- **<collection>：**定义集合类型的数据映射规则
  *property：*声明该结果集映射在对象中的属性名
  *ofType：*声明集合中数据的类型
  *select*
  *column*
  *fetchType*

  - **<id>**
  - **<result>**

  > **collection分步查询**

  Department对象中的属性：int departmentId，String departmentName，List<Employee> employees

  ```xml
  <mapper namespace="com.zbvc.dao.IDepartmentDao">
      <resultMap id="departmentMap" type="com.zbvc.domain.Department">
      	<id column="department_id" property="departmentId"></id>
          <result column="department_name" property="departmentName"></result>
          
          <collection property="employees" select="com.zbvc.dao.IEmployeeDao.getEmployeesByDepartmentId" column="id"/>
      </resultMap>
      
  	<select id="getDepartmentById" resultMap="departmentMap">
      	SELECT id,department_name FROM tb_department WHERE id=#{id}
      </select>
  </mapper>
  ```

  ```xml
  <mapper namespace="com.zbvc.dao.IEmployeeDao">
  	<select id="getEmployeesByDepartmentId" resultType="com.zbvc.domain.Employee">
      	SELECT * FROM tb_employee WHERE department_id=#{deptId}
      </select>
  </mapper>
  ```

- **<discriminator>：**鉴别器，MyBatis可以使用**discriminator**判断某列的值，然后根据某列的值改变封装行为，MyBatis会将查询结果优先通过鉴别器中的映射配置来映射，如果不满足鉴别器所指定的条件，则执行其它映射配置来映射

  *column：*指定要判断的列名

  *javaType：*列值对应的java类型

  - **<case>：**相当于switch结构中的case语句
    *value：*要判断的列的值，当为这个值时，执行该**<case>**标签的结果集映射方式
    *resultType：*指定封装的结果类型

  > 封装Employee，如果查出的是女生，把部门信息一同查询出来，如果是男生，就把name这一列的值赋给email属性且不查询部门

  ```xml
  <mapper>
  	<resultMap id="map" resultType="com.zbvc.domain.Employee">
      	<id column="employee_id" property="id"></id>
          <result column="employee_name" property="name"></result>
          <result column="employee_email" property="email"></result>
          <discriminator javaType="string" column="gender">
          	<case value="女" resultType="com.zbvc.domain.Employee">
              	<association property="department" select="com.zbvc.dao.IDepartmentDao.getDepartmentById" column="department_id"/>
              </case>
              <case value="男" resultType="com.zbvc.domain.Employee">
              	<id column="employee_id" property="id"></id>
                  <result column="employee_name" property="name"></result>
                  <result column="employee_name" property="email"></result>
              </case>
          </discriminator>
      </resultMap>
      
      <select id="getEmployeeById" resultMap="map">
      	SELECT * FROM tb_employee WHERE id=#{id}
      </select>
  </mapper>
  ```

  ```xml
  <mapper namespace="com.zbvc.dao.IDepartmentDao">
  	<insert id="getDepartmentById" resultType="com.zbvc.domain.Department">
      	SELECT * FROM tb_department WHERE id=#{id}
      </insert>
  </mapper>
  ```

## 3.动态SQL

**动态sql：**当我们将所有查询条件封装到对象中并传入，而sql语句对于条件的使用有多种情况时，使用动态sql进行sql语句的拼接。==需要将sql的条件写为WHERE 1=1==

> **1.<if>：**当满足*test*属性的判断语句时，将**<if>**标签中的SQL语句进行拼接

```xml
<select id="selectStudents" parameterType="com.zbvc.domain.Student" resultType="com.zbvc.domain.Student">
    SELECT * FROM student WHERE 1=1
    <!--接口方法参数只有一个，可以省略参数名-->
    <if test="name!=null">
        AND name = #{name};
    </if>
</select>
```

**注：**if标签的test属性值为**OGNL表达式**，如果有多个判断语句，语句之间使用and链接而不能使用&&

**OGNL表达式：**

1. 访问对象属性：==对象名.属性名==
2. 调用方法：==对象名.方法名()==
3. 调用静态变量 / 方法：==@全类名@静态变量名==，==@全类名@静态方法名()==
   例：@java.lang.Math@PI，@java.util.UUID@randomUUID()
4. 调用构造方法：==new 全类名()==，还可以继续==.属性名==直接调用创建出的对象的属性
   例：new com.zbvc.domain.Person('wjq').name
5. 算数运算符
6. 逻辑运算符，但是在xml中“<”、“>”等为特殊字符，需要使用转义字符“\&lt;”、“\&gt;”



> **2.<where>：**用于将所有条件语句包括起来，就可以省去sql语句中的==WHERE 1=1==

```xml
<select id="selectStudent" parameterType="com.zbvc.domain.Student" resuleType="com.zbvc.domain.Student">
	SELECT * FROM student
    <where>
    	<if test="id != null">
        	AND id=#{id}
        </if>
        <if test="name != null and name != '' ">
        	AND name LIKE #{name}
        </if>
    </where>
</select>
```

```java
@Test
public void test(){
    String resource = "mybatis-config.xml";
    InputStream inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    SqlSession session = sqlSessionFactory.openSession();
    IStudentDao mapper = session.getMapper(IStudentDao.class);
    Student student = new Student(null,"%wu%");
    mapper.selectStudent(student);
}
```



> **3.<choose>：**相当于多重if语句，只会执行其中一个分支

```xml
<select id="selectStudents" parameterType="com.zbvc.domain.Student" resultType="com.zbvc.domain.Student">
    SELECT * FROM student
    <where>
        <choose>
            <when test="name != null and name != ''">
                name = #{name};
            </when>
            <when test="id != 0">
                id = #{id};
            </when>
            <otherwise>
            	gender="女";
            </otherwise>
        </choose>
    </where>
</select>
```



> **4.<set>：**用于update标签中，用于更新满足流程控制语句的数据

```xml
<update id="updateStudent" parameterType="java.lang.Integer" resultType="com.zbvc.domain.Student">
    UPDATE student
    <set>
        <if test="username != null and username != ''">
            name = #{name}
        </if>
        <if test="number != null">
            number = #{number}
        </if>
    </set>
    WHERE id = #{id}
</update>
```



> **5.<foreach>：**遍历数组和集合。常用于批量插入

-  *collection*配置的是传入的参数名称
- *item*为循环中的当前遍历出的元素所赋予的变量，如果遍历的是map，*item*是value
- *index*指定的变量保存当前元素在集合的索引，如果遍历的是map，那么*index*是key
- *open*和*close*为当该标签内部的sql语句拼接完毕后，以什么符号作为这个sql片段的开始和结束符号，*separator*为各个元素间以什么为分割。

```java
public interface IStudentDao{
    public List<Student> selectStudentByIds(@Param("ids")List<Integer ids);
    public void addStudents(@Param(students)List<Student> students);
}
```

```xml
<select id="selectStudentByIds" parameterType="com.zbvc.domain.Student" resultType="com.zbvc.domain.Student">
    SELECT * FROM student
    <foreach collection="ids" open=" WHERE id IN(" close=")" separator="," item="id">
        #{id}
    </foreach>
</select>

<select id="addStudents" parameterType="com.zbvc.domain.Student" resultType="com.zbvc.domain.Student">
	INSERT INTO student(name,email,gender) VALUES
    <foreach collection="students" item="student" separator=",">
    	(#{student.name},#{student.email},#{student.gender})
    </foreach>
</select>>
```

该标签通常用于批量操作，因为一个预编译对象只能传递一条SQL语句来执行，所以如果通过该标签将多条SQL语句拼接为一条语句来执行，则需要在MySql的链接地址后添加参数*allowMultiQueries=true*来支持这种形式的语句



> **6.<trim>：**用于取出sql语句中不需要的字符串，以保证sql语句语法的正确性，与where标签有相同的作用。*prefix*代表标签内的语句拼接完毕后需要加的前缀，*prefixOverrides*代表标签内部所有sql拼接好后，需要去掉的前缀字符串；*suffix*代表标签内的语句拼接完毕后需要加的后缀，*suffixOverrides*代表标签内部所有sql拼接好后，需要去掉的后缀字符串

**例1：**

```xml
<select id="selectStudents" resultType="com.zbvc.domain.Student">
    SELECT * FROM student
    <trim prefix="WHERE" prefixOverrides="AND">
        <if test="name != null and name != ''">
            AND name = #{name}
        </if>
        <if test="id != null">
            AND id = #{id}
        </if>
    </trim>
</select>
```

**例2：**

```xml
<insert id="saveStudent" parameterType="com.zbvc.domain.Student">
    INSERT INTO student (
    <trim suffixOverrides=",">
        <if test="name != null and name != ''">
            name,
        </if>
        <if test="number != null and number != ''">
            number,
        </if>
    </trim>
    ) VALUES(
    <trim suffixOverrides=",">
        <if test="name != null and name != ''">
            #{name},
        </if>
        <if test="number != null and number != ''">
            #{number},
        </if>
    </trim>
    )
</insert>
```



> **7.<sql>：**可以将重复被使用的sql语句写入该标签中成为sql片段，在需要使用该片段的地方引入即可。该标签中同样支持动态sql

```xml
<sql id="sqlFragment">
	SELECT id,username,number FROM student
</sql>
```

==在需要片段的地方引入：<include refid="sqlFragmentId"/ >==



> **8.<bind>：** 用于对变量的值进行修改。*name*属性用于设置一个新的变量存储通过*value*属性修改的变量值，*value*属性的值使用**OGNL**表达式指定。

```xml
<select id="getEmployeeByName" parameterType="com.zbvc.domain.Employee" resultType="com.zbvc.domain.Employee">
    <bind name="bindName" value=" '_' + name + '%' "/>
    SELECT * FROM employee WHERE name LIKE #{bindName}
</select>
```

## 4.内置参数

1. _parameter：代表接口方法中的所有参数，多个参数会被封装为Map

2. _databaseId：如果配置了**<databaseProvider>**标签，该参数的值就是代表当前数据库的别名

   ```xml
   <select id="getEmployeeById" parameterType="com.zbvc.domain.Employee" resultType="com.zbvc.domain.Employee">
   	<if test="_databaseId == 'oracle' ">
       	SELECT * FROM tb_employee
           <if test="_parameter != null">
           	WHERE name = #{_parameter.name}
           </if>
       </if>
       <if test="_databaseId == 'mysql' ">
       	SELECT * FROM tb_department
       </if>
   </select>
   ```

   

# 三.测试类

```java
public class TestStudent {
    private InputStream inputStream;
    private SqlSession session;
    private IStudentDao mapper;

    @Before
    public void init() throws Exception{
        String resource = "mybatis-config.xml";
        //读取主配置文件
        inputStream = Resources.getResourceAsStream(resource);
        //创建sqlSession工厂
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        //使用工厂创建session对象，创建session对象时会同时创建一个事务对象，并关闭自动提交
        session = sqlSessionFactory.openSession();
        //使用session创建Dao接口的代理对象
        mapper = session.getMapper(IStudentDao.class);
    }
    @After
    public void destroy() throws Exception{
        session.commit();
        if(session != null){
            session.close();
        }
        if(inputStream != null){
            inputStream.close();
        }
    }

    @Test
    public void testSelectStudents(){
        //使用代理对象执行dao接口中的抽象方法
        List<Student> students = mapper.selectStudents();
        for(Student student : students) {
            System.out.println(student);
        }
    }

}
```

# 四.PageHelper

>  MyBatis提供的一个分页插件

1. 导入jar包

   ```xml
   <dependency>
   	<groupId>com.github.pagehelper</groupId>
   	<artifactId>pagehelper</artifactId>
       <version>5.1.2</version>
   </dependency>
   ```

2. 配置插件

   > **单独使用在MyBatis时，在核心配置文件中配置拦截器**

   ```xml
   <plugins>
       <!--interceptor属性为PageHelper类所在的包名-->
   	<plugin interceptor="com.github.pagehelper.PageInterecptor">
       	<property name="param1" value="value1"/>
       </plugin>
   </plugins>
   ```

   > **在SSM中使用时，在applicationContext.xml文件的sqlSessionFactory中配置拦截器**

   ```xml
   <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
       <property name="dataSource" ref="dataSource"/>
   	<property name="plugins">
       	<array>
           	<bean class="com.github.pagehelper.PageInterceprot">
               	<property name="properties">
                   	<props>
                       	<prop key="helperDialect">mysql</prop>
                           <prop key="reasonable">true</prop>
                       </props>
                   </property>
               </bean>
           </array>
       </property>
   </bean>
   ```

   > **键值**

   - *helperDialect*：分页插件会自动检测当前的数据库连接，自动选择合适的分页方式。可以配置*helperDialect*属性来指定分页插件使用哪种数据库的分页语法。配置时可以使用以下缩写值：`oracle`，`mysql`，`mariadb`，`sqlite`，`hsqldb`，`postgresql`，`db2`，`sqlserver`，`informix`，`h2`，`sqlserver2012`，`derby`
     **注：**使用SqlServer2012数据库时，需要手动指定为sqlserver2012，否则会使用SqlServer2005的方式进行分页
   - *reasonable*：分页合理化参数，默认值为false，当该参数设置为true时，页码小于或等于0时会查询第一页，大于等于页码总数时会查询最后一页

3. 使用
   **紧跟着==PageHelper.startPage("当前页码","每页显示条数");==语句的第一个执行查询的方法会被分页，查询方法内部的sql语句在执行时会根据==startPage()==方法的参数自动进行分页**

   ```java
   package com.zbvc.service.imp;
   
   public class OrdersServiceImp implements IOrdersService{
       @Autowired
       private IOrdersDao ordersDao;
       
       @Override
       public List<Order> findAll(int pageNum, int pageSize) throws Exception{
           //将查询所有的sql语句使用插件变为分页查询
           PageHelper.startPage(pageNum, pageSize);
           return ordersDao.findAll();
       }
   }
   ```

> **PageInfo**

使用了PageHelper后，创建该对象时将插件返回的结果（**List<Order>**）作为构造方法的参数传入，可以得到一个**PageInfo<Order>**对象，对象的*list*属性保存了**List<order>**；*pageSize*属性保存每页需要展示多少条；*size*保存当前页的条数；*pages*属性代表总页数；*pageNum*保存当前页码；*total*保存总记录数。使用该两个参数的容构造方法创建该对象，第二个参数表示多少个连续的页码，*navigatepageNums*属性会保存一个大小为第二个参数的数组，数组内保存了一串连续的页码，当前页码总是位于数组的中间位置。通过其getter即可获取这些信息

```java
public class OrdersController{
    @Autowired
    private OrdersServiceImp ordersService;
    
    @RequestMappint("/getOrders")
    public String getOrders(@RequestParam("pageNum")int pageNum, @RequestParam(value="pageSize", defaultValue="5")int pageSize, Model model){
        List<Order> orders = ordersService.findAll(pageNum, pageSize);
        PageInfo<Order> pageInfo = new PageInfo(orders, 6);
        model.addAttribute("pageInfo", pageInfo);
        return "success";
    }
}
```

> 使用Spring中的JUnit模拟请求

- `@WebAppConfiguration`：在类上添加该注解可通过自动注入的方式获取SpringMVC容器

- **WebApplicationContext**：SpringMVC的IOC容器

- **MockMvcRequestBuilders：**创建模拟请求，不同的方法用于发送不同的请求，如`get(String url)`用于发送GET请求；`param()`用于设置此次请求的参数；`andReturn()`用于获取此次请求的响应结果

- **MockMvc：**能够执行模拟MVC请求，通过`MockMvcBuilders.webAppContextSetup(WebApplicationContext context).build()`获得，使用`perform()`执行模拟请求

- **MvcResult：**此次请求的响应结果，可以通过该对象的getter获取域对象，从域对象中取数据

```java
@RunWith(SpringJUnit4ClassRunner.class)
@WebAppConfiguration
@ContextConfiguration(locations={"classpath:applicationContext.xml","classpath:springmvc.xml"})
public class Test{
    @Autowired
    WebApplicationContext context;
    MockMvc mockMvc;
    @Before
    public void initMockMvc(){
        MockMvcBuilders.webAppContextSetup(context);
    }
    
    public void test() throws Exception{
        MvcResult result = mockMvc.perform(MockMvcRequestBuilders.get("/test").param("pageNum","2")).andReturn();
        MockHttpServletRequest request = result.getRequest();
        PageInfo pageInfo = (PageInfo) request.getAttribute("pageInfo");
        System.out.println("当前页码："+pageInfo.getPageNum());
        System.out.println("总页码："+pageInfo.getPages());
        System.out.println("前一页："+pageInfo.getPrePage());
        System.out.println("此次请求可显示的连续页码："+pageInfo.getNavigatepageNums());
    }
}
```

