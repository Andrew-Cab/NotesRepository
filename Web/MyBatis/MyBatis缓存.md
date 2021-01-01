## 1.什么是缓存

1. 缓存就是存在于内存中的数据
2. 缓存的优势：使用缓存可以减少和数据库交互的次数，提高执行效率
3. 适用于缓存的数据：
   经常查询并且不经常改变的数据
   数据的正确与否对最终的结果影响不大的数据

##2.MyBatis中的一级缓存

> 概念

一级缓存也叫**本地缓存**，它指的是MyBatis中的SqlSessioin对象的缓存。与数据库同一次会话期间查询的结果会同时存入到SqlSession为我们提供的一块区域中，该区域的结构是一个Map。每个SqlSession的缓冲区是独立的。当我们再次查询同样的数据，MyBatis会先去SqlSession中查询是否有，有的话直接拿来使用。当SqlSession对象消失时，MyBatis的一级缓存也就消失了。

> 示例

**Dao接口：**

```java
public interface IUserDao{
    //根据id查询用户信息
    User findById(Integer userId);
    //更新用户信息
    void updateUser(User user);
}
```

**Mapper文件：**

```xml
<mapper namespace="com.zbvc.dao.IUserDao">
    <!--根据id查询用户-->
    <select id="findById" parameterType="int" resultType="user">
    	SELECT * FROM user WHERE id = #{id}
    </select>
    
    <!--更新用户信息-->
    <update id="updateUser" parameterType="user">
    	UPDATE user SET username=#{username},address=#{address} WHERE id=#{id}
    </update>
</mapper>
```

**测试代码：**

```java
@Test
public void testCatch(){
    String resource = "mybatis-config.xml";
    inputStream = Resources.getResourceAsStream(resource);
    
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    
    SqlSession session = sqlSessionFactory.openSession();
    
    IUserDao mapper = session.getMapper(IUserDao.class);
    
    User user1 = mapper.findById(1);
    //session.clearCache(); 可清空一级缓存
    User user2 = mapper.findById(1);
    System.out.println(user1==user2);//结果为true，日志只记录了一次sql语句的执行
}

@Test
public void testCatch(){
    String resource = "mybatis-config.xml";
    inputStream = Resources.getResourceAsStream(resource);
    
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    
    SqlSession session = sqlSessionFactory.openSession();
    
    IUserDao mapper = session.getMapper(IUserDao.class);
    
    User user1 = mapper.findById(1);
    
    user1.setUserName("update user");
    user1.setAddress("山西省");
    mapper.updateUser(user1);
    
    User user2 = mapper.findById(1);
    System.out.println(user1==user2);
    /*
    	结果为false，一级缓存是SqlSession范围的缓存，当调用SqlSession的修改，添加，删除，commit(),close()等方法时，就会清空一级缓存
    */
}
```



## 3.MyBatis中的二级缓存

> 概念

它指的是MyBatis中SqlSessionFactory对象的缓存，由同一个SqlSessionFactory对象创建的SqlSession共享其缓存。只有当SqlSession会话关闭时，一级缓存中的数据才会保存到二级缓存中。当进行查询时，==会优先从二级缓存中查找，再查找一级缓存，最后从数据库中查询==。使用二级缓存保存的数据类型需要实现**Serializable**接口

> 示例

**MyBatis-Config文件配置：**开启全局二级缓存支持

```xml
<configuration>
	<settings>
    	<setting name="cacheEnabled" value="true"/>
    </settings>
</configuration>
```

**Mapper文件配置：**在需要二级缓存的Mapper文件中配置**<cache/>**

- *eviction：*缓存回收策略，取值：
  - LRU：最近最少使用的，移除最长时间不被使用的对象，==默认值==
  - FIFO：先进先出，按照对象进入缓存的顺序来移除
  - SOFT：软引用，移除基于垃圾回收器状态和软引用规则的对象
  - WEAK：弱引用，更积极地移除基于垃圾回收器状态和弱引用规则的对象
- *flushInterval：*缓存多久清空一次，单位毫秒，==默认不清空==
- *readOnly：*
  - true：只读，mybatis认为所有从缓存中获取数据的操作都是只读操作，不会修改数据，为了加快速度，会直接将数据在缓存中的引用直接交给用户，不安全，但速度快
  - false：非只读，mybatis认为数据可能会被修改，会利用序列化和反序列化技术克隆一份数据，安全但速度慢，==默认false==，因为要用到序列化和反序列化，所以POJO需要实现Serializable接口

- *size：*缓存区可以保存多少个元素

- *type：*让框架使用第三方缓存或自定义缓存，即实现了**MyCache**接口的类，值为类的全类名

**<cache-ref/>：**用于配置当前Mapper文件和哪个Mapper文件使用桶一个缓存，属性*namespace*用于指定命名空间

```xml
<mapper namespace="com.zbvc.dao.IUserDao">
    
    <cache eviction="FIFO" flushInterval="60000" readOnly="false" size="1024"/>
	<!--根据id查询用户-->
    <select id="findById" parameterType="int" resultType="user" useCache="true">
    	SELECT * FROM user WHERE id = #{id}
    </select>
    
</mapper>
```

**Dao接口：**

```java
public interface IUserDao{
    //根据id查询用户信息
    User findById(Integer userId);
}
```

**测试代码：**

```java
@Test
public void testSecondaryCache(){
    String resource = "mybatis-config.xml";
    inputStream = Resources.getResourceAsStream(resource);
    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    
    SqlSessioin session1 = sqlSessionFactory.openSession();
    IUserDao mapper1 = session.getMapper(IUserDao.class);
    User user1 = mapper.findById(1);
    mapper1.close();//关闭会话，数据保存至二级缓存中
    
    SqlSessioin session2 = sqlSessionFactory.openSession();
    IUserDao mapper2 = session.getMapper(IUserDao.class);
    User user2 = mapper.findById(1);
    mapper2.close();//关闭会话
    
    System.out.println(user1 == user2);
    /*
    	结果为false，但日志只记录了一次sql语句的执行。
    	原因：在二级缓存中存放的内容是数据而不是对象（序列化和反序列化），当再次调用同一个方法时，会根据缓存中记录的信息创建一个新的对象
    */
}
```

## 4.第三方缓存整合

框架提供了**MyChahe**接口，可用于自定义缓存。其`putObject()`方法用于将数据存入缓存，`getObject()`用于从缓存获取数据