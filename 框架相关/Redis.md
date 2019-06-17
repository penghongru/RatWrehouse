# **Redis**


#### Redis的5种数据类型

 * ###### 字符串（String）

    * ###### set key value
      设定key持有制定的字符串value,如果该key已存在则覆盖原有数据,无论新增或覆盖都返回OK

      示例 : `set username "penghongru"`

  * ###### get key
    获取key持有的字符串,若该key持有value不是String类型则返回错误信息,若该key不存在则返回nil

    示例 : `get username`

  * ###### del key
    删除制定的key

    示例 : `del username`
  * ###### incr
    数据自增

    示例 : `incr point_support`

    步长自增

    示例 : `incrby experience 500`
  * ###### decr
    数据自减

    示例 : `decr product_num`

    步长自减

    示例 : `decrby health 100`


 * ###### 哈希（hash）
  * ###### hset key field value
    向指定的key中添加或覆盖field/value键值对

    示例 : `hset sms_code 15338739039 6524`

  * ###### hget key field
    获取指定key中指定键的值

    示例 : `hget sms_code 15338739039`

  * ###### hdel key field[field1 .....]
    删除一个或多个字段,返回值是被删除字段个数

    示例 : `hdel visit_log lastday today`

  * ###### hgetall key
    获取指定key中的所有键值对

    示例 : `hgetall brand`

 * ###### 字符串列表（list)
  * ###### lpush key values[value1 value2]
    向指定key所关联的list头部插入所有value,如果该key不存在则创建与该key关联的空链表然后插入数据,成功则返回插入数据个数

    示例 : `lpush message message1 message2 message3`

  * ###### rpush key values[value1 value2]
    同上,从尾部插入数据

    示例 : `rpush message message6 message5 message4`

  * ###### lpop key
    从头部弹出一个数据,返回该数据,弹出数据会从链表中删除,若该key不存在则返回nil

    示例 : `lpop message`

  * ###### rpop key
    同上,从尾部弹出

    示例 : `rpop message`

  * ###### lrange key startIndex endIndex
    从头部开始获取指定索引范围的数据

    示例 : `lrange message 2 5`

    遍历所有数据

    示例 : `lrange message 0 -1`

 * ###### 字符串集合（set）
  * ###### sadd key members[member1 member2 member3]
    向set中添加数据,若该key已存在则不会重复添加

    示例 : `sadd room_user liubei guanyu zhangfei zilong`

  * ###### smembers key
    获取set中的所有数据

    示例 : `smembers room_user`

  * ###### srem key members[member1 member2 member3]
    删除set中指定的多个数据

    示例 : `srem room_user liubei guanyu zhangfei `

 * ###### 有序字符串集合（sorted set）
  * ###### zadd key values[value1 value2 value3]
    向set中添加数据

    示例 : `zadd rank 1 songjiang 2 lujunyi 3 gongsunsheng`

  * ###### zrem key value
    删除set中的数据

    示例 : `zrem rank songjiang`

  * ###### zrange key startIndex endIndex
    获取指定索引区间的数据

    示例 : `zrange room_user 0 36`

    获取所有数据

    示例 : `zrange room_user 0 -1`


 ###### PS : <font color=red>key不可过长, 超过1024字节会降低查找效率</font>

---
### Redis 通用命令
  * ###### keys pattern
    根据正则查找匹配的key

    示例 : `keys paylog*`

    查看所有key

    示例 : `keys *`

  * ###### del key1 key2
    删除制定的key

    示例 : `del errorLog warnLog`

  * ###### exists key
    判断key是否存在 返回1存在,0不存在

    示例 : `exists username`

  * ###### type key
    获取key的类型,以字符串形式返回,string,list,set,hash,若key不存在返回none

    示例 : `type username`

---

### Redis 持久化机制

* ###### RDB 持久化机制
  * 优点

    单文件存储,方便备份,易于灾备
    性能高,服务进程在持久化时只需要frok子进程完成持久化操作
  * 缺点

    未备份的数据可能丢失,数据量大是备份时间长

  * 配置

    在redis.conf中

    ```
    save 900 1
    save 300 10
    save 60 10000
    ```

关键字|时间(秒)|key修改数量|解释
:----:|:-----:|:--------:|:---:
save  |900    |1         |每900秒至少有1个key发生变化,则dump内存快照
save  |300    |10        |每300秒至少有10个key发生变化,则dump内存快照
save  |60     |10000     |每60秒至少有10000个key发生变化,则dump内存快照

  * ###### AOF 持久化机制
   * 优点

      数据安全性高,有三种同步策略,每秒同步,每次修改同步及不同步,同步采用异步完成

      日志写入采用append模式,写入过程中宕机不会破坏已存在内容,若在写入过程中宕机则可通过redis-check-aof工具来解决数据一致性问题

      若日志过大则自动启用rewrite机制,在redis以append模式不断的向日志文件写入时,同时创建一个新的文件记录此期间有哪些修改命令被执行,更好的保证了数据安全性

      AOF保安一个格式清晰且易于理解的日志文件来记录所有的修改操作,也可通过该文件来完成数据重建

    * 缺点

     对于相同数据量的数据集,AOF文件通常大于RDB文件

     根据同步策略的不同,AOF在运行效率上往往慢于RDB,美妙同步策略的效率较高,不同步则与RDB效率基本一致

     * 配置
      在redis.conf中

      ```
      #appendonly yes为开启,no关闭,开启AOF持久化机制,默认会在目录下产生一个appdendonly.aof文件
      appendonly yes
      #appendfsync always
      appendfsync everysec
      #appendfsync no
      ```

| 关键字        | 持久化时机  | 解释                           |
| :-----------: | :----------| :---------------------------- |
| appendfsync   | always     | 每执行一次更新命令，持久化一次   |
| appendfsync   | everysec   | 每秒钟持久化一次                |
| appendfsync   | no         | 不持久化                       |

---

### SpringDataRedis 使用
  * ###### 依赖
  pom中添加
  ```
  <!--  redis 客户端 -->
  <dependency>
    <groupId>redis.clients</groupId>
    <artifactId>jedis</artifactId>
    <version>2.8.1</version>
  </dependency>

  <!--  Spring redis 封装 客户端 -->
  <dependency>
    <groupId>org.springframework.data</groupId>
    <artifactId>spring-data-redis</artifactId>
    <version>1.7.2.RELEASE</version>
  </dependency>

  ```

  * ###### 属性配置
    创建文件redis-config.properties
    ```
    redis.host=redis服务器IP
    redis.port=redis服务端口
    redis.pass=
    redis.database=0
    redis.maxIdle=300 #最大空闲数
    redis.maxWait=3000 #连接时的最大等待毫秒数
    redis.testOnBorrow=true
    ```
  * ###### xml 配置
    创建文件applicationContext-redis.xml
    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <beans xmlns="http://www.springframework.org/schema/beans"
    	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xmlns:p="http://www.springframework.org/schema/p"
    	xmlns:context="http://www.springframework.org/schema/context"
    	xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
      xmlns:mvc="http://www.springframework.org/schema/mvc"
    	xsi:schemaLocation="http://www.springframework.org/schema/beans
      http://www.springframework.org/schema/beans/spring-beans.xsd
      http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
      http://code.alibabatech.com/schema/dubbo http://code.alibabatech.com/schema/dubbo/dubbo.xsd
      http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

       <context:property-placeholder location="classpath*:*.properties" />   
       <!-- redis 相关配置 -->
       <bean id="poolConfig" class="redis.clients.jedis.JedisPoolConfig">  
         <property name="maxIdle" value="${redis.maxIdle}" />   
         <property name="maxWaitMillis" value="${redis.maxWait}" />  
         <property name="testOnBorrow" value="${redis.testOnBorrow}" />  
       </bean>  
       <bean id="JedisConnectionFactory" class="org.springframework.data.redis.connection.jedis.JedisConnectionFactory"
           p:host-name="${redis.host}" p:port="${redis.port}" p:password="${redis.pass}" p:pool-config-ref="poolConfig"/>  

       <bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">  
         <property name="connectionFactory" ref="JedisConnectionFactory" />  
       </bean>  
    </beans>

    ```

  * ###### 示例

    ```java
    @autowired
    private RedisTemplate redisTemplate;

    @Test
    public void test(){
        //String类型操作
        //添加数据
        redisTemplate.boundValueOps("username").set("wusong");
        //查找数据
        String username = (String) redisTemplate.boundValueOps("username").get();
        //删除数据
        redisTemplate.delete("username");
        //Set类型操作
        //添加数据
        redisTemplate.boundSetOps("nameset").add("张楚岚");		
        redisTemplate.boundSetOps("nameset").add("冯宝宝");
        redisTemplate.boundSetOps("nameset").add("王也");
        //获取所有值
        Set members = redisTemplate.boundSetOps("nameset").members();
        //删除指定集合的指定值
        redisTemplate.boundSetOps("nameset").remove("刘备");
        //删除指定集合的所有数据
        redisTemplate.delete("nameset");
        //List类型操作
        //右压栈
        redisTemplate.boundListOps("namelist").rightPush("张楚岚");
        redisTemplate.boundListOps("namelist").rightPush("冯宝宝");
        redisTemplate.boundListOps("namelist").rightPush("王也");
        //根据索引获取右压栈数据集合
        List rightNameList = redisTemplate.boundListOps("namelist").range(0, 10);
        //左压栈
        redisTemplate.boundListOps("namelist").leftPush("诸葛青");
        redisTemplate.boundListOps("namelist").leftPush("张灵玉");
        redisTemplate.boundListOps("namelist").leftPush("无根生");
        //根据索引获取左压栈数据集合
        List leftNameList = redisTemplate.boundListOps("namelist").range(0, 10);
        //根据索引查找数据
        String secondName = (String) redisTemplate.boundListOps("namelist").index(1);
        //移除指定数据
        redisTemplate.boundListOps("namelist").remove(1, "冯宝宝");
        //Hash类型操作
        //添加数据
        redisTemplate.boundHashOps("namehash").put("刮骨刀", "夏荷");
        redisTemplate.boundHashOps("namehash").put("祸根苗", "沈冲");
        redisTemplate.boundHashOps("namehash").put("雷烟炮", "高宁");
        redisTemplate.boundHashOps("namehash").put("穿肠毒", "窦梅");
        //获取所有数据
        Set nameHash = redisTemplate.boundHashOps("namehash").keys();
        //根据key获取数据
        Object object = redisTemplate.boundHashOps("namehash").get("祸根苗");
        //根据key移除数据
        redisTemplate.boundHashOps("namehash").delete("雷烟炮");
    }
    ```
