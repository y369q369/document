## 面试题

### 一、Java虚拟机

#### 1.1 数据存储结构

 ![img](https://imgconvert.csdnimg.cn/aHR0cHM6Ly91c2VyLWdvbGQtY2RuLnhpdHUuaW8vMjAxNy85LzQvZGE3N2Q5MDE0Njc4NmMwY2IzZTE3MGI5YzkzNzZhZTQ_aW1hZ2VWaWV3Mi8wL3cvMTI4MC9oLzk2MC9mb3JtYXQvd2VicC9pZ25vcmUtZXJyb3IvMQ) 

#### 1.2 内存溢出排查

- jstack
- jconsole

#### 1.3 垃圾回收

数据可达性

### 二、Java基础

#### 2.1 集合

> **2.1.1 HashMap和HashTable区别**

- HashMap允许将 null 作为一个 entry 的 key 或者 value，而 Hashtable 不允许。
- HashMap 把 Hashtable 的 contains 方法去掉了，改成 containsValue 和 containsKey。因为 contains 方法容易让人引起误解。
- HashTable 继承自 Dictionary 类，而 HashMap 是 Java1.2 引进的 Map interface 的一个实现。
- HashTable 的方法是 Synchronize 的，而 HashMap 不是，在多个线程访问 Hashtable 时，不需要自己为它的方法实现同步，而 HashMap 就必须为之提供外同步。
- Hashtable 和 HashMap 采用的 hash/rehash 算法都大概一样，所以性能不会有很大的差异。

> **2.1.2 线程安全的集合类**

- Vector：就比Arraylist多了个同步化机制（线程安全）。
- Hashtable：就比Hashmap多了个线程安全。
- ConcurrentHashMap:是一种高效但是线程安全的集合。
- Stack：栈，也是线程安全的，继承于Vector。

> **2.1.3 HashMap在jdk1.8之后的新特性**

- 链表长度长度超过8，存储方式转换为红黑树：某个节点下的数据量超过8个

> **2.1.4 集合去重**

1. 常规对象去重：set集合与list集合互转，jdk1.8的stream去重(distinct)
2. 对象去重：jdk1.8的stream去重，对象中重写equals()方法和hashCode()方法

> **2.1.5 遍历map**

- 迭代器（Iterator）

```java
Iterator it=map.entrySet().iterator();         
String key;         
String value;  
while(it.hasNext()){  
    Map.Entry entry = (Map.Entry)it.next();         
    key=entry.getKey().toString();         
    value=entry.getValue().toString();         
} 
```

- for循环 / 增强for循环

```java
for (Map.Entry<String, String> entry : map.entrySet()) {
	System.out.println("key= " + entry.getKey() + " and value= " + entry.getValue());
}
```

- **遍历的时候需要删除元素使用迭代器**

  > 迭代器操作：先next()，再执行remove（）

  1. 当创建完成指向某个集合或者容器的Iterator对象是，这是的指针其实指向的是第一个元素的上方，即指向一个 空
  2. 当调用hasNext方法的时候，只是判断下一个元素的有无，并不移动指针
  3. 当调用next方法的时候，向下移动指针，并且返回指针指向的元素，如果指针指向的内存中没有元素，会报异 常。
  4. remove方法删除的元素是指针指向的元素。如果当前指针指向的内存中没有元素，那么会抛出异常。
  5.  在iterator的next方法中会判断modCount和expectedModCount是否相等不相等就抛异常 

  > 增强for循环：底层虽然也是用迭代器实现，当删除时先remove()，再next()，容易抛  `Exception in thread “main” java.util.ConcurrentModificationException`  异常

> **2.1.6 有序，重复集合**

- 无序不重复：set(顺序由HashCode决定， 检索元素效率低下，删除和插入效率高，插入和删除不会引起元素位置改变。  )
- 有序重复：list（ 和数组类似，List可以动态增长，查找元素效率高，插入删除元素效率低，因为会引起其他元素位置改变。 ）
  - ArrayList： (动态数组)的数据结构 ，查询快
  - LinkedList： (链表)的数据结构 ， 增加和删除 快

-  有序不重复：TreeSet 和 TreeMap 

> **2.1.7 hashMap底层实现原理**

#### 2.2 反射

#### 2.3 线程

> **2.3.1 线程的创建方式**

- 实现 callable 接口,或 future 接口
- 实现 runnable 接口
- 继承 thread 类

> **2.3.2 callable和runnable区别**

callable有返回值， runnable无返回值

> **2.3.3 wait和sleep区别**

wait 线程会释放对象锁，sleep 线程不会释放对象锁 

> **2.3.4 ThreadLocal**

> **2.3.5 线程池创建方式**

- 通过Executors工厂方法创建

  ```java
  //创建使用单个线程的线程池
  ExecutorService es1 = Executors.newSingleThreadExecutor();
  
  //创建使用固定线程数的线程池
  ExecutorService es2 = Executors.newFixedThreadPool(3);
  
  //创建一个会根据需要创建新线程的线程池
  ExecutorService es3 = Executors.newCachedThreadPool();
  
  //创建拥有固定线程数量的定时线程任务的线程池
  ScheduledExecutorService es4 = Executors.newScheduledThreadPool(2);
  ```

- 通过new ThreadPoolExecutor()自定义创建

  ```java
  public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue workQueue, ThreadFactory threadFactory, RejectedExecutionHandler handler) ;
  ```

#### 2.4 基础

> **2.4.1 final关键字作用**

- final类不能被继承，没有子类，final类中的方法默认是final的。 
- final方法不能被子类的方法覆盖，但可以被继承。 
- final成员变量表示常量，只能被赋值一次，赋值后值不再改变。 
- final不能用于修饰构造方法。

> **2.4.2 volatile**

 所有线程都能看到共享内存的最新状态。 

#### 2.5 设计模式

### 三、Spring

#### 3.1 bean的生命周期

- 实例化 Instantiation
- 属性赋值 Populate
- 初始化 Initialization
- 销毁 Destruction

#### 3.2 AOP的应用

 ![img](https://images2017.cnblogs.com/blog/1153743/201709/1153743-20170910204937335-296831698.png)  ![img](https://images2017.cnblogs.com/blog/1153743/201709/1153743-20170910204843054-205540626.png) 

#### 3.3 AOP的动态实现

> 常见实现方式

1、采用声明的方式来实现（基于XML）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/tx 
    http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
    http://www.springframework.org/schema/aop 
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">
    <!-- 声明一个业务类 -->
    <bean id="userManager" class="com.spring.service.impl.UserManagerServiceImpl">
        <property name="name" value="lixiaoxi"></property>
    </bean>  
    <!-- 声明通知类 -->
    <bean id="aspectBean" class="com.spring.aop.AopAspect" />
    <aop:config>
     <aop:aspect ref="aspectBean">
        <aop:pointcut id="pointcut" expression="execution(* com.spring.service.impl.UserManagerServiceImpl..*(..))"/>
        <aop:before method="doBefore" pointcut-ref="pointcut"/> 
        <aop:after-returning method="doAfterReturning" pointcut-ref="pointcut" returning="result"/>
        <aop:after method="doAfter" pointcut-ref="pointcut" /> 
        <aop:around method="doAround" pointcut-ref="pointcut"/> 
        <aop:after-throwing method="doAfterThrowing" pointcut-ref="pointcut" throwing="ex"/>
      </aop:aspect>
   </aop:config>
</beans>
```

2、是采用注解的方式来实现（基于AspectJ）。

```java
package com.spring.aop;
import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.AfterReturning;
import org.aspectj.lang.annotation.AfterThrowing;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;

@Aspect
public class AopAspectJ {
    /**  
     * 必须为final String类型的,注解里要使用的变量只能是静态常量类型的  
     */  
    public static final String EDP="execution(* com.spring.service.impl.UserManagerServiceImpl..*(..))";
    /**
     * 切面的前置方法 即方法执行前拦截到的方法
      * 在目标方法执行之前的通知
      * @param jp
     */
    @Before(EDP)
    public void doBefore(JoinPoint jp){
        System.out.println("=========执行前置通知==========");
    }
    /**
     * 在方法正常执行通过之后执行的通知叫做返回通知
     * 可以返回到方法的返回值 在注解后加入returning 
     * @param jp
     * @param result
     */
    @AfterReturning(value=EDP,returning="result")
    public void doAfterReturning(JoinPoint jp,String result){
        System.out.println("===========执行后置通知============");
    }
    /**
     * 最终通知：目标方法调用之后执行的通知（无论目标方法是否出现异常均执行）
     * @param jp
     */
    @After(value=EDP)
    public void doAfter(JoinPoint jp){
        System.out.println("===========执行最终通知============");
    }
    /**
     * 环绕通知：目标方法调用前后执行的通知，可以在方法调用前后完成自定义的行为。
     * @param pjp
     * @return
     * @throws Throwable
     */
    @Around(EDP)
    public Object doAround(ProceedingJoinPoint pjp) throws Throwable{
        System.out.println("======执行环绕通知开始=========");
        // 调用方法的参数
        Object[] args = pjp.getArgs();
        // 调用的方法名
        String method = pjp.getSignature().getName();
        // 获取目标对象
        Object target = pjp.getTarget();
        // 执行完方法的返回值
        // 调用proceed()方法，就会触发切入点方法执行
        Object result=pjp.proceed();
        System.out.println("输出,方法名：" + method + ";目标对象：" + target + ";返回值：" + result);
        System.out.println("======执行环绕通知结束=========");
        return result;
    }
    /**
     * 在目标方法非正常执行完成, 抛出异常的时候会走此方法
     * @param jp
     * @param ex
     */
    @AfterThrowing(value=EDP,throwing="ex")
    public void doAfterThrowing(JoinPoint jp,Exception ex) {
        System.out.println("===========执行异常通知============");
    }
}
spring的配置:
<?xml version="1.0" encoding="UTF-8"?>
<beans
    xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xmlns:p="http://www.springframework.org/schema/p"
    xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans-3.0.xsd
    http://www.springframework.org/schema/tx 
    http://www.springframework.org/schema/tx/spring-tx-3.0.xsd
    http://www.springframework.org/schema/aop 
    http://www.springframework.org/schema/aop/spring-aop-3.0.xsd">
    <!-- 声明spring对@AspectJ的支持 -->
    <aop:aspectj-autoproxy/>    
    <!-- 声明一个业务类 -->
    <bean id="userManager" class="com.spring.service.impl.UserManagerServiceImpl">
        <property name="name" value="lixiaoxi"></property>
    </bean>   
     <!-- 声明通知类 -->
    <bean id="aspectBean" class="com.spring.aop.AopAspectJ" />
</beans>

测试类：

package com.spring.test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;
import com.spring.service.IUserManagerService;
public class TestAop1 {
    public static void main(String[] args) throws Exception{
        ApplicationContext act =  new ClassPathXmlApplicationContext("applicationContext4.xml");
         IUserManagerService userManager = (IUserManagerService)act.getBean("userManager");
         userManager.findUser();
         System.out.println("\n");
         userManager.addUser();
    }
}
```

> 动态实现方式

#### 3.4 Spring优点

- 轻巧
- 控制反转，依赖注入
- 面向切面

### 四、MyBatis

#### 4.1 动态sql注解

> **if标签**

```xml
<if test="name != null and name != ''">
    and NAME = #{name}
</if>
```

>**foreach标签**
>
>collection：collection 属性的值有三个分别是 list、array、map 三种，分别对应的参数类型为：List、数组、map 集合。
>item ：表示在迭代过程中每一个元素的别名
>index ：表示在迭代过程中每次迭代到的位置（下标）
>open ：前缀
>close ：后缀
>separator ：分隔符，表示迭代时每个元素之间以什么分隔

```xml
<!-- in查询所有，不分页 -->
<select id="selectIn" resultMap="BaseResultMap">
    select name,hobby from student where id in
    <foreach item="item" index="index" collection="list" open="(" separator="," close=")">
        #{item}
    </foreach>
</select>
```

> **choose、when、otherwise标签**

```xml
<select id="findActiveBlogLike"
     resultType="Blog">
  SELECT * FROM BLOG WHERE state = ‘ACTIVE’
  <choose>
    <when test="title != null">
      AND title like #{title}
    </when>
    <when test="author != null and author.name != null">
      AND author_name like #{author.name}
    </when>
    <otherwise>
      AND featured = 1
    </otherwise>
  </choose>
</select>
```

> **trim、set标签**

```xml
<update id="updateAuthorIfNecessary">
  update Author
    <set>
      <if test="username != null">username=#{username},</if>
      <if test="password != null">password=#{password},</if>
      <if test="email != null">email=#{email},</if>
      <if test="bio != null">bio=#{bio}</if>
    </set>
  where id=#{id}
</update>

<trim prefix="SET" suffixOverrides=",">
  ...
</trim>
```

#### 4.2 对象参数注解

```java
public interface BizSdkGroupMapper {
	int updateById(@Param("oldBundleId") String oldBundleId, @Param("bizSdkGroup") BizSdkGroup bizSdkGroup);
}
```

```xml
<update id="updateById">
    update biz_sdk_group
    set 
    name = #{bizSdkGroup.name,jdbcType=VARCHAR},
    description = #{bizSdkGroup.description,jdbcType=VARCHAR},
    platform = #{bizSdkGroup.platform, jdbcType=TINYINT},
    bundle_id = #{bizSdkGroup.bundleId, jdbcType=VARCHAR}
    where bundle_id = #{oldBundleId,jdbcType=BIGINT}
</update>
```

### 五、Mysql

#### 5.1 引擎

>  **InnoDB存储引擎** 

  **支持事务处理，支持外键，支持崩溃修复能力和并发控制**。如果需要**对事务的完整性要求比较高**（比如银行），**要求实现并发控制**（比如售票），那选择InnoDB有很大的优势。如果需要**频繁的更新、删除**操作的数据库，也可以选择InnoDB，因为支持事务的提交（commit）和回滚（rollback）。  

>  **MyISAM存储引擎** 

 插入数据快，空间和内存使用比较低。如果表主要是**用于插入新记录和读出记录**，那么选择MyISAM能实现处理高效率。如果应用的完整性、并发性要求比 较低，也可以使用。 

>  **MEMORY存储引擎** 

 所有的数据都在内存中，数据的处理速度快，但是安全性不高。如果需要**很快的读写速度**，对数据的安全性要求较低，可以选择MEMOEY。它对表的大小有要求，不能建立太大的表。所以，这类数据库只使用在相对较小的数据库表。 

 ![img](https://img2020.cnblogs.com/blog/1070495/202006/1070495-20200610214231777-417543280.png) 

#### 5.2 事务

> **5.2.1  事务的基本要素（ACID）**

- **原子性（Atomicity）**：事务开始后所有操作，要么全部做完，要么全部不做，不可能停滞在中间环节。事务执行过程中出错，会回滚到事务开始前的状态，所有的操作就像没有发生一样。也就是说事务是一个不可分割的整体，就像化学中学过的原子，是物质构成的基本单位。
- **一致性（Consistency**）：事务开始前和结束后，数据库的完整性约束没有被破坏 。比如A向B转账，不可能A扣了钱，B却没收到。
- **隔离性（Isolation）**：同一时间，只允许一个事务请求同一数据，不同的事务之间彼此没有任何干扰。比如A正在从一张银行卡中取钱，在A取钱的过程结束前，B不能向这张卡转账。
- **持久性（Durability）**：事务完成后，事务对数据库的所有更新将被保存到数据库，不能回滚。

> **5.2.2  事务的并发问题**

- **脏读**：事务A读取了事务B更新的数据，然后B回滚操作，那么A读取到的数据是脏数据
- **不可重复读**：事务 A 多次读取同一数据，事务 B 在事务A多次读取的过程中，对数据作了更新并提交，导致事务A多次读取同一数据时，结果 不一致。
- **幻读**：系统管理员A将数据库中所有学生的成绩从具体分数改为ABCDE等级，但是系统管理员B就在这个时候插入了一条具体分数的记录，当系统管理员A改结束后发现还有一条记录没有改过来，就好像发生了幻觉一样，这就叫幻读。

>  **5.2.3 MySQL事务隔离级别** 

| 事务隔离级别                 | 脏读 | 不可重复读 | 幻读 |
| ---------------------------- | ---- | ---------- | ---- |
| 读未提交（read-uncommitted） | 是   | 是         | 是   |
| 不可重复读（read-committed） | 否   | 是         | 是   |
| 可重复读（repeatable-read）  | 否   | 否         | 是   |
| 串行化（serializable）       | 否   | 否         | 否   |

### 六、Redis

#### 6.1 数据类型

- string（字符串）： 最大能存储 512MB 

  ```bash
  redis 127.0.0.1:6379> SET runoob "菜鸟教程"
  OK
  redis 127.0.0.1:6379> GET runoob
  "菜鸟教程"
  ```

- hash（哈希）： 一个键值(key=>value)对集合 ， 适合用于存储对象 

  ```bash
  redis 127.0.0.1:6379> DEL runoob
  redis 127.0.0.1:6379> HMSET runoob field1 "Hello" field2 "World"
  "OK"
  redis 127.0.0.1:6379> HGET runoob field1
  "Hello"
  redis 127.0.0.1:6379> HGET runoob field2
  "World"
  ```

- list（列表）： 列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）。 

  ```bash
  redis 127.0.0.1:6379> DEL runoob
  redis 127.0.0.1:6379> lpush runoob redis
  (integer) 1
  redis 127.0.0.1:6379> lpush runoob mongodb
  (integer) 2
  redis 127.0.0.1:6379> lpush runoob rabbitmq
  (integer) 3
  redis 127.0.0.1:6379> lrange runoob 0 10
  1) "rabbitmq"
  2) "mongodb"
  3) "redis"
  redis 127.0.0.1:6379>
  ```

- set（集合）： string 类型的无序集合，是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)

  ```bash
  redis 127.0.0.1:6379> DEL runoob
  redis 127.0.0.1:6379> sadd runoob redis
  (integer) 1
  redis 127.0.0.1:6379> sadd runoob mongodb
  (integer) 1
  redis 127.0.0.1:6379> sadd runoob rabbitmq
  (integer) 1
  redis 127.0.0.1:6379> sadd runoob rabbitmq
  (integer) 0
  redis 127.0.0.1:6379> smembers runoob
  
  1) "redis"
  2) "rabbitmq"
  3) "mongodb"
  ```

- zset(sorted set：有序集合) ：string类型元素的不重复集合,不同的是每个元素都会关联一个double类型的分数。redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

  ```bash
  redis 127.0.0.1:6379> DEL runoob
  redis 127.0.0.1:6379> zadd runoob 0 redis
  (integer) 1
  redis 127.0.0.1:6379> zadd runoob 0 mongodb
  (integer) 1
  redis 127.0.0.1:6379> zadd runoob 0 rabbitmq
  (integer) 1
  redis 127.0.0.1:6379> zadd runoob 0 rabbitmq
  (integer) 0
  redis 127.0.0.1:6379> ZRANGEBYSCORE runoob 0 1000
  1) "mongodb"
  2) "rabbitmq"
  3) "redis"
  ```

#### 6.3 数据持久化

> RDB： 在指定的时间间隔内将内存中的数据集快照写入磁盘，实际操作过程是fork一个子进程，先将数据集写入临时文件，写入成功后，再替换之前的文件，用二进制压缩存储。 

```bash
save 900 1              #在900秒(15分钟)之后，如果至少有1个key发生变化，则dump内存快照。

save 300 10            #在300秒(5分钟)之后，如果至少有10个key发生变化，则dump内存快照。

save 60 10000        #在60秒(1分钟)之后，如果至少有10000个key发生变化，则dump内存快照。
```

> AOF： 以日志的形式记录服务器所处理的每一个写、删除操作，查询操作不会记录，以文本的方式记录，可以打开文件看到详细的操作记录。 

```bash
# 三种同步方式
appendfsync always     #每次有数据修改发生时都会写入AOF文件。

appendfsync everysec  #每秒钟同步一次，该策略为AOF的缺省策略。

appendfsync no          #从不同步。高效但是数据不会被持久化。
```

> 区别

- RDB：备份的文件只有一个基于内存生成的快照，数据恢复快， 服务出问题时容易造成数据丢失
- AOF：数据恢复慢，保证数据安全

#### 6.4 高可用

#### 6.5 客户端工具

> RedisClient

https://github.com/caoxinyu/RedisClient/releases

### 七、消息中间件

#### 7.1 Kafka和RabbitMQ区别

1. **应用场景方面**
   RabbitMQ：用于实时的，对可靠性要求较高的消息传递上。
   kafka：用于处于活跃的流式数据，大数据量的数据处理上。
2. **架构模型方面**
   producer，broker，consumer
   RabbitMQ：以broker为中心，有消息的确认机制
   kafka：以consumer为中心，无消息的确认机制
3. **吞吐量方面**
   RabbitMQ：支持消息的可靠的传递，支持事务，不支持批量操作，基于存储的可靠性的要求存储可以采用内存或硬盘，吞吐量小。
   kafka：内部采用消息的批量处理，数据的存储和获取是本地磁盘顺序批量操作，消息处理的效率高，吞吐量高。

4. **集群负载均衡方面**
   RabbitMQ：本身不支持负载均衡，需要loadbalancer的支持
   kafka：采用zookeeper对集群中的broker，consumer进行管理，可以注册topic到zookeeper上，通过zookeeper的协调机制，producer保存对应的topic的broker信息，可以随机或者轮询发送到broker上，producer可以基于语义指定分片，消息发送到broker的某个分片上。

#### 7.2 kafka集群数据一致性

#### 7.3 防止数据丢失

### 八、linux指令

#### 8.1 sed命令

- **说明**

   利用脚本来处理文本文件 

- **语法**

  ```bash
  sed [-hnV][-e<script>][-f<script文件>][文本文件]
  ```

- **参数**

  -e<script>或--expression=<script> 以选项中指定的script来处理输入的文本文件。

  -f<script文件>或--file=<script文件> 以选项中指定的script文件来处理输入的文本文件。

  -h或--help 显示帮助。

  -n或--quiet或--silent 仅显示script处理后的结果。

  -V或--version 显示版本信息。

- **动作**

  a ：新增， a 的后面可以接字串，而这些字串会在新的一行出现(目前的下一行)～

  c ：取代， c 的后面可以接字串，这些字串可以取代 n1,n2 之间的行！

  d ：删除，因为是删除啊，所以 d 后面通常不接任何咚咚；

  i ：插入， i 的后面可以接字串，而这些字串会在新的一行出现(目前的上一行)；

  p ：打印，亦即将某个选择的数据印出。通常 p 会与参数 sed -n 一起运行～

  s ：取代，可以直接进行取代的工作哩！通常这个 s 的动作可以搭配正规表示法！例如 1,20s/old/new/g 就是啦！

- **实例**

  ```bash
  # 在testfile文件的第四行后添加一行，并将结果输出到标准输出
  sed -e 4a\newLine testfile 
  
  
  ```

  

#### 8.2 查看日志

> head： 于查看文件的开头部分的内容       				head [参数] [文件] 

- **参数**

  -q 隐藏文件名

  -v 显示文件名

  -c<数目> 显示的字节数。

  -n<行数> 显示的行数。

- **实例**

  ```bash
  # 要显示 runoob_notes.log 文件的开头 10 行，请输入以下命令
  head runoob_notes.log
  
  # 显示 notes.log 文件的开头 5 行，请输入以下命令
  head -n 5 runoob_notes.log
  
  # 显示文件前 20 个字节
  head -c 20 runoob_notes.log
  ```

> tail：  查看文件最尾部的内容    						tail [参数] [文件]  

-  **参数** 

  -f 循环读取

  -q 不显示处理信息

  -v 显示详细的处理信息

  -c<数目> 显示的字节数

  -n<行数> 显示文件的尾部 n 行内容

  --pid=PID 与-f合用,表示在进程ID,PID死掉之后结束

  -q, --quiet, --silent 从不输出给出文件名的首部

  -s, --sleep-interval=S 与-f合用,表示在每次反复的间隔休眠S秒

-  **实例**

  ```bash
  # 显示 notes.log 文件的最后 10 行
  tail notes.log         # 默认显示最后 10 行
  
  # 跟踪名为 notes.log 的文件的增长情况
  tail -f notes.log
  
  # 显示文件 notes.log 的内容，从第 20 行至文件末尾
  tail -n +20 notes.log
  
  # 显示文件 notes.log 的最后 10 个字符
  tail -c 10 notes.log
  ```

#### 8.3 vim编辑

- **快捷键**

  | 功能                  | 快捷键 |
  | --------------------- | ------ |
  | 删除                  | dd     |
  | 快速切换到末尾 / 开头 | G / gg |
  | 撤销                  | u      |

- **模式**

  - 一般模式

  - 编辑模式

  - 命令模式

#### 8.4 脚本

> **8.4.1 脚本中语句执行状态**

### 九、Vue

#### 9.1 vue父子组件的created和mounted加载顺序

父created  -  子created - 子mounted - 父created

### 十、大数据

#### 10.1 CDH集群

#### 10.2 HBase 与 传统数据库区别

#### 10.3 HBase Shell 命令