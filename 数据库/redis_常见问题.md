# 缓存穿透
### 什么是缓存穿透
正常情况下，查询的数据都存在，如果请求一个不存在的数据，也就是缓存和数据库都查不到这个数据，每次都会去数据库查询，这种查询不存在数据的现象我们称为缓存穿透
### 穿透带来的问题
如果每次都拿一个不存在的id去查询数据库，可能会导致你的数据库压力增大
## 解决办法

#### BloomFilter
BloomFilter 类似于一个hbase set 用来判断某个元素（key）是否存在于某个集合中
我们把有数据的key都放到BloomFilter中，每次查询的时候都先去BloomFilter判断，如果没有就直接返回null
注意BloomFilter没有删除操作，对于删除的key，查询就会经过BloomFilter然后查询缓存再查询数据库，所以BloomFilter可以结合缓存空值用，对于删除的key，可以在缓存中缓存null


#### 缓存空值
 之所以发生穿透，是因为缓存中没有存储这些数据的key，从而每次都查询数据库
我们可以为这些key在缓存中设置对应的值为null，后面查询这个key的时候就不用查询数据库了
当然为了健壮性，我们要对这些key设置过期时间，以防止真的有数据  
```java
//伪代码
public object GetProductListNew() {
    int cacheTime = 30;
    String cacheKey = "product_list";

    String cacheValue = CacheHelper.Get(cacheKey);
    if (cacheValue != null) {
        return cacheValue;
    }

    cacheValue = CacheHelper.Get(cacheKey);
    if (cacheValue != null) {
        return cacheValue;
    } else {
        //数据库查询不到，为空
        cacheValue = GetProductListFromDB();
        if (cacheValue == null) {
            //如果发现为空，设置个默认值，也缓存起来
            cacheValue = string.Empty;
        }
        CacheHelper.Add(cacheKey, cacheValue, cacheTime);
        return cacheValue;
    }
}
```

# 缓存击穿
### 什么是缓存击穿
在高并发的情况下，大量的请求同时查询同一个key时，此时这个key正好失效了，就会导致同一时间，这些请求都会去查询数据库，这样的现象我们称为缓存击穿
### 击穿带来的问题
会造成某一时刻数据库请求量过大
##  解决办法
采用分布式锁，只有拿到锁的第一个线程去请求数据库，然后插入缓存，当然每次拿到锁的时候都要去查询一下缓存有没有
使用互斥锁(mutex key)

业界比较常用的做法，是使用mutex。简单地来说，就是在缓存失效的时候（判断拿出来的值为空），不是立即去load db，
而是先使用缓存工具的某些带成功操作返回值的操作（比如Redis的SETNX或者Memcache的ADD）去set一个mutex key，当操作返回成功时，再进行load db的操作并回设缓存；否则，就重试整个get缓存的方法。

SETNX，是「SET if Not eXists」的缩写，也就是只有不存在的时候才设置，可以利用它来实现锁的效果。
```java
public String get(key) {
      String value = redis.get(key);
      if (value == null) { //代表缓存值过期
          //设置3min的超时，防止del操作失败的时候，下次缓存过期一直不能load db
      if (redis.setnx(key_mutex, 1, 3 * 60) == 1) {  //代表设置成功
               value = db.get(key);
                      redis.set(key, value, expire_secs);
                      redis.del(key_mutex);
              } else {  //这个时候代表同时候的其他线程已经load db并回设到缓存了，这时候重试获取缓存值即可
                      sleep(50);
                      get(key);  //重试
              }
          } else {
              return value;      
          }
 }
 ```
memcache代码：
```java
if (memcache.get(key) == null) {  
    // 3 min timeout to avoid mutex holder crash  
    if (memcache.add(key_mutex, 3 * 60 * 1000) == true) {  
        value = db.get(key);  
        memcache.set(key, value);  
        memcache.delete(key_mutex);  
    } else {  
        sleep(50);  
        retry();  
    }  
}
```
[缓存问题_图_代码](https://zhuanlan.zhihu.com/p/75588064)

# 缓存雪崩
### 什么是缓存雪崩
当某一时刻发生大规模的缓存失效的情况，比如你的缓存服务宕机了
与缓存击穿的区别在于这里针对很多key缓存，前者则是某一个key。
## 解决办法

### 采用集群，降低服务宕机的概率
ehcache本地缓存 + Hystrix限流&降级
ehcache 本地缓存的目的也是考虑在 Redis Cluster 完全不可用的时候，ehcache 本地缓存还能够支撑一阵
使用 Hystrix进行限流 & 降级 ，比如一秒来了5000个请求，我们可以设置假设只能有一秒 2000个请求能通过这个组件，那么其他剩余的 3000 请求就会走限流逻辑



缓存正常从Redis中获取，示意图如下：
缓存失效时的雪崩效应对底层系统的冲击非常可怕！大多数系统设计者考虑用加锁或者队列的方式保证来保证不会有大量的线程对数据库一次性进行读写，
从而避免失效时大量的并发请求落到底层存储系统上。还有一个简单方案就时讲缓存失效时间分散开，
比如我们可以在原有的失效时间基础上增加一个随机值，比如1-5分钟随机，这样每一个缓存的过期时间的重复率就会降低，就很难引发集体失效的事件

加锁排队，伪代码如下：
```java
//伪代码
public object GetProductListNew() {
    int cacheTime = 30;
    String cacheKey = "product_list";
    String lockKey = cacheKey;

    String cacheValue = CacheHelper.get(cacheKey);
    if (cacheValue != null) {
        return cacheValue;
    } else {
        synchronized(lockKey) {
            cacheValue = CacheHelper.get(cacheKey);
            if (cacheValue != null) {
                return cacheValue;
            } else {
              //这里一般是sql查询数据
                cacheValue = GetProductListFromDB(); 
                CacheHelper.Add(cacheKey, cacheValue, cacheTime);
            }
        }
        return cacheValue;
    }
}
 ```
 
 加锁排队只是为了减轻数据库的压力，并没有提高系统吞吐量。假设在高并发下，缓存重建期间key是锁着的，这是过来1000个请求999个都在阻塞的。同样会导致用户等待超时，这是个治标不治本的方法！

注意：加锁排队的解决方式分布式环境的并发问题，有可能还要解决分布式锁的问题；线程还会被阻塞，用户体验很差！因此，在真正的高并发场景下很少使用！

随机值伪代码：

//伪代码
public object GetProductListNew() {
    int cacheTime = 30;
    String cacheKey = "product_list";
    //缓存标记
    String cacheSign = cacheKey + "_sign";

    String sign = CacheHelper.Get(cacheSign);
    //获取缓存值
    String cacheValue = CacheHelper.Get(cacheKey);
    if (sign != null) {
        return cacheValue; //未过期，直接返回
    } else {
        CacheHelper.Add(cacheSign, "1", cacheTime);
        ThreadPool.QueueUserWorkItem((arg) -> {
      //这里一般是 sql查询数据
            cacheValue = GetProductListFromDB(); 
          //日期设缓存时间的2倍，用于脏读
          CacheHelper.Add(cacheKey, cacheValue, cacheTime * 2);                 
        });
        return cacheValue;
    }
} 
解释说明：

缓存标记：记录缓存数据是否过期，如果过期会触发通知另外的线程在后台去更新实际key的缓存；
缓存数据：它的过期时间比缓存标记的时间延长1倍，例：标记缓存时间30分钟，数据缓存设置为60分钟。这样，当缓存标记key过期后，实际缓存还能把旧数据返回给调用端，直到另外的线程在后台更新完成后，才会返回新缓存。
关于缓存崩溃的解决方法，这里提出了三种方案：使用锁或队列、设置过期标志更新缓存、为key设置不同的缓存失效时间，还有一种被称为“二级缓存”的解决方法

# 解决热点数据集中失效问题
我们在设置缓存的时候，一般会给缓存设置一个失效时间，过了这个时间，缓存就失效了。
对于一些热点的数据来说，当缓存失效以后会存在大量的请求过来，然后打到数据库去，从而可能导致数据库崩溃的情况
## 解决办法

设置不同的失效时间
采用缓存击穿的解决办法，加锁
永不失效，就是采用定时任务对快要失效的缓存进行更新缓存和失效时间

[缓存穿透、缓存击穿、缓存雪崩](https://juejin.im/post/6844903813904596999)

# 小结
针对业务系统，永远都是具体情况具体分析，没有最好，只有最合适。

于缓存其它问题，缓存满了和数据丢失等问题，大伙可自行学习。最后也提一下三个词LRU、RDB、AOF，通常我们采用LRU策略处理溢出，Redis的RDB和AOF持久化策略来保证一定情况下的数据安全
