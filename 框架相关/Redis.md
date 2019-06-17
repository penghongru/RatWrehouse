# **redis**


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
  * ###### sadd key values[value1 value2 value3]
    向set中添加数据,若该key已存在则不会重复添加
 * ###### 有序字符串集合（sorted set）


 ###### PS : <font color=red>key不可过长, 超过1024字节会降低查找效率</font>
