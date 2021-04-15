## 分布式锁概览

在多线程的环境下，为了保证一个代码块在同一时间只能由一个线程访问，Java中我们一般可以使用synchronized语法和ReetrantLock去保证，这实际上是本地锁的方式。但是现在公司都是流行分布式架构，在分布式环境下，如何保证不同节点的线程同步执行呢？

实际上，对于分布式场景，我们可以使用分布式锁，它是控制分布式系统之间**互斥访问共享资源**的一种方式。

比如说在一个分布式系统中，多台机器上部署了多个服务，当客户端一个用户发起一个数据插入请求时，如果没有分布式锁机制保证，那么那多台机器上的多个服务可能进行并发插入操作，导致数据重复插入，对于某些不允许有多余数据的业务来说，这就会造成问题。而分布式锁机制就是为了解决类似这类问题，保证多个服务之间互斥的访问共享资源，如果一个服务抢占了分布式锁，其他服务没获取到锁，就不进行后续操作。大致意思如下图所示（不一定准确）：

### 分布式锁的特点

分布式锁一般有如下的特点：

- 互斥性： 同一时刻只能有一个线程持有锁
- 可重入性： 同一节点上的同一个线程如果获取了锁之后能够再次获取锁
- 锁超时：和J.U.C中的锁一样支持锁超时，防止死锁
- 高性能和高可用： 加锁和解锁需要高效，同时也需要保证高可用，防止分布式锁失效
- 具备阻塞和非阻塞性：能够及时从阻塞状态中被唤醒

### 分布式锁的实现方式

我们一般实现分布式锁有以下几种方式：

- 基于数据库
- 基于Redis
- 基于zookeeper

## Redis的分布式锁实现

### 1. 利用setnx+expire命令 (错误的做法)

Redis的SETNX命令，setnx key value，将key设置为value，当键不存在时，才能成功，若键存在，什么也不做，成功返回1，失败返回0 。 SETNX实际上就是SET IF NOT Exists的缩写

因为分布式锁还需要超时机制，所以我们利用expire命令来设置，所以利用setnx+expire命令的核心代码如下：

```java
public boolean tryLock(String key,String requset,int timeout) {
    Long result = jedis.setnx(key, requset);
    // result = 1时，设置成功，否则设置失败
    if (result == 1L) {
        return jedis.expire(key, timeout) == 1L;
    } else {
        return false;
    }
}

```

实际上上面的步骤是有问题的，setnx和expire是分开的两步操作，不具有原子性，如果执行完第一条指令应用异常或者重启了，锁将无法过期。

### 2.  使用 set key value[ EX seconds] [PX milliseconds] [NX|XX] 命令 (正确做法)

Redis在 2.6.12 版本开始，为 SET 命令增加一系列选项：

```
SET key value[EX seconds][PX milliseconds][NX|XX]
```

- EX seconds: 设定过期时间，单位为秒
- PX milliseconds: 设定过期时间，单位为毫秒
- NX: 仅当key不存在时设置值
- XX: 仅当key存在时设置值

set命令的nx选项，就等同于setnx命令，代码过程如下：

```java
public boolean tryLock_with_set(String key, String UniqueId, int seconds) {
    return "OK".equals(jedis.set(key, UniqueId, "NX", "EX", seconds));
}

```

value必须要具有唯一性，我们可以用UUID来做，设置随机字符串保证唯一性，至于为什么要保证唯一性？假如value不是随机字符串，而是一个固定值，那么就可能存在下面的问题：

- 1.客户端1获取锁成功
- 2.客户端1在某个操作上阻塞了太长时间
- 3.设置的key过期了，锁自动释放了
- 4.客户端2获取到了对应同一个资源的锁
- 5.客户端1从阻塞中恢复过来，因为value值一样，所以执行释放锁操作时就会释放掉客户端2持有的锁，这样就会造成问题

所以通常来说，在释放锁时，我们需要对value进行验证

### SpringBoot 分布式session

> 在如今服务器集群的情况下，用户登录会话状态的保存也从单机的变成了分布式要求的，下面详细说一下几种分布式session存储方案。

1. session复制：在支持session复制的服务器上进行，同步session，保持session一致

   方案：`tomcat-redis-session-manager`

2. session粘滞：强行分发session到各个服务器

   方案：负载均衡

3. cookie存储session：把sessionid存储到cookie中(不安全，cookie容易被盗取，可以存储不重要的数据)

4. session集中管理：把用户的session存储在单台或者集群服务器的缓存中，所有web服务器从中拿取session，实现session共享

   方案：Redis存储用户生成的sessionId或者存储保存sessionId的cookie

> 这里只讲解第四种方案，使用最多最稳定
>
> 

### Redis做缓存持久化存储session

#### Redis存储cookie，里面保存用户生成的uuid(token作为sessionId)

- cookie名称和过期时间

```java
public static final String COOKIE_NAME_TOKEN = "token";private static final ex  = 3600;
```

- 创建cookie并存储到redis

```java
private String addCookie(HttpServletResponse response,SeckillUser user){
    
    String token = UUIDUtil.uuid();
    
    redisService.set(token,user);//使用redis把token作为键存储user作为值 我使用jedis自己实现的也可以使用redisTemplate     
    Cookie cookie = new Cookie(COOKIE_NAME_TOKEN, token);//设置cookie 名称为token        		   
    
    cookie.setMaxAge(ex);//设置过期时间         
    
    cookie.setPath("/");
    
    response.addCookie(cookie);        
    
    return token;  
}
```

- uuid作为sessionid生成工具

```java
public class UUIDUtil {
    public static String uuid(){    
        return UUID.randomUUID().toString();    
    }
}
```


