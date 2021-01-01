# Redis

- 概念：redis是一款高性能的NOSQL系列的非关系型数据库，redis有0~15，共16个库，默认使用的是第0个

- redis的数据结构：
  reis存储的是key，value格式的数据，其中key都是字符串，value有5种不同的数据结构
  1. 字符串类型（String）
  2. 哈希类型（hash）：值为多个键值对的形式
  3. 列表类型（list）：LinkedList格式
  4. 集合类型（set）：值不可重复
  5. 有序集合类型（sortedset）：值不可重复且自动排序

##1.命令操作

> **字符串类型**

- 存储：set key value   ==重复存储会覆盖前一个值==

  ```markdown
  set weibo:thumb-up 0
  set name zhang
  ```

- 为key的值追加字符串：append key valueAppended

  ```markdown
  append name san 
  ```

- 为一个可转为数值型的值加1：incr key

  ```markdown
  incr weibo:thumb-up
  ```

- 为一个可转为数值型的值加指定大小：incrby key stepSize

  ```markdown
  incrby weibo:thumb-up 5
  ```

- 获取：get key

  ````markdown
  get weibo:thumb-up
  ````

- 获取该key从index1到index2截取后的值：getrange key index1 index2

  ```markdown
  getrange name 2 4
  ```

- 拿到旧值并赋值新值：getset key newValue

  ```markdown
  getset weibo:thumb-up 20
  ```

- 如果key不存在则存储，存在不覆盖：setnx key value

- 删除：del key

> **哈希类型**

- 存储：hset key field value   ==可重复操作同一个key以赋值多个值==
- 获取：
  hget key field    获取指定字段的值
  hgetall key         获取所有字段的值
- 删除：hdel key field

> **列表类型：**可以添加一个元素到列表头或尾

- 添加：
  lpush key value：将元素添加到列表头，可一次添加多个值，但插入的位置和一个一个添加相同
  rpush key value：将元素添加到列表尾
- 获取：
  lrange key start -end：范围获取，开始索引为0
  例：lrange mylist 0 -1  ==-1表示读出整个list的数据==
- 删除：
  lpop key：删除列表最左边的元素，并返回被删元素
  rpop key：删除列表最右边的元素，并返回被删元素

> **集合类型**

- 存储：sadd key member  ==可直接添加多个值，用空格分割==
- 获取：smembers key    获取set集合所有元素
- 删除：srem key member

> **有序集合类型**

- 存储：zadd key score value   指定分数，通过分数进行升序
- 获取：zrange key start end
  例：zrange mysort 0 -1 withscores   获取所有值和分数
- 删除：zrem key value

> **通用命令**

- keys *：查询所有的键
- type key：获取键所对应的值的类型
- del key：删除任意键和值
- select 数据库索引：切换数据库
- flushdb：清空当前数据库
- flushall：清除全部数据库内容
- expire key seconds：为key的值设置失效时间
- exists key：参看该key是否存在
- ttl key：查看这个key还有多久失效
- help @数据类型：查看该数据类型的帮助文档

## 2.持久化

redis是一个内存数据库，数据是临时的，当redis重启数据会丢失，需要将redis内存中的数据持久化保存到硬盘中

- RDB：默认方式，在一定的时间间隔中，检测key的变化情况，然后持久化数据
  使用：编辑redis.windows.conf文件，修改save的值。在redis目录下打开cmd，通过cmd打开server并指定配置文件：redis-server.exe redis.windows.conf
  ==可能会丢失数据==
- AOF：日志记录的方式，可以记录每一条命令的操作。可以每次一命令操作后持久化数据，和MySQL差不多
  使用：编辑redis.windows.conf文件，找到appendonly，将其值改为yes
  ==影响性能==

##3.jedis

jedis是一款java操作redis数据库的工具

使用：导入jar包

```java
@Test
public void test(){
    Jedis jedis = new Jedis("localhost",6379); //若该构造不传参则默认为此参数
    jedis.set("username","zhangsan");
    jedis.setex("active",20,"hehe"); //存储键为active，值为hehe的字符串数据，20秒后自动删除
    jedis.close();
}
```

jedis连接池：JedisPool

使用：

```java
@Test
public void test(){
    //0.创建一个配置对象
    JedisPoolConfig config = new JedisPoolConfig();
    config.setMaxTotal(50);  //设置最大连接对象数
    config.setMaxIdle(10);   //设置最大闲置连接数
    //1.创建Jedis连接池对象
    JedisPool jedisPool = new JedisPool(config,"localhost","6379");
    //2.获取连接
    Jedis jedis = jedisPool.getResource();
    //3.使用
    jedis.set("username","zhangsan");
    //4.归还连接
    jedis.close();
}
```

##4.JedisUtils

```java
public class JedisPoolUtils{
    private static JedisPool jedisPool;
    static{
        InputStream is = JedisPoolUtils.class.getClassLoader().getResourceAsStream("jedis.properties");
        Properties pro = new Properties();
        try{
            pro.load(is);
        }catch(IOException e){
            e.printStackTrace();
        }
        JedisPoolConfig config = new JedisPoolConfig();
        config.setMaxTotal(Integer.parseInt(pro.getProperty("maxTotal")));
        config.setMaxIdle(Integer.parseInt(pro.getProperty("maxIdle")));
        //初始化JedisPool
        jedisPool = new JedisPool(config,pro.getProperty("host"),Integer.parseInt(pro.getProperty("port")));
    }
    //获取Jedis方法
    public static Jedis getJedis(){
        return jedisPool.getResource();
    }
}
```

##5.缓存优化

Redis常用于存储数据库中不经常发生变化的数据，通过在Service层添加Redis作为缓存可减少对数据库的操作，增加性能

```java
package service

public Class ProvinceService (){
    private ProvinceDao dao = new ProvinceDao();
    public String findAllJson(){
        //通过工具类创建Jedis
        Jedis jedis = JedisPoolUtils.getJedis();
        //先从redis中查询是否有key为province的数据
        String province_json = jedis.get("province");
        //判断province
        if(province_json == null || province_json.length() == 0){
            //说明Redis中没有数据，从数据库中查询
            System.out.println("Resid中没有数据，从数据库查询");
            List<Province> ps = dao.FindAll();
            //将从数据库查询出的结果序列化为JSON
            ObjectMapper mapper = new ObjectMapper();
           try{
                province_json = mapper.writeValueAsString(ps);
           }catch(JsonProcessingException e){
               e.printStackTrace();
           }
            //将json数据存入Redis中
            jedis.set("province",province_json);
            jesid.close();
        }
    }
}
```

```java
@WebServlet("/provinceServlet")
public class ProvinceServlet extends HttpServlet{
    protected void doPost(HttpServletRequest request,HttpServletResponse response) throws ServletException{
        //调用service查询
        ProvinceService service = new ProvinceService();
        String json = service.findAlljson();
        System.out.println(json);
        
        //响应结果
        response.setContentType("application/json;charset=utf-8");
        response.getWriter().write(json);
    }
}
```

