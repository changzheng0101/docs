# Redis高并发分布式处理

## 场景

某个商品的秒杀实现，通过向`deduct_stock`接口发送申请来完成减少库存，库存以`key:value`的形式保存在Redis中。

## 模拟代码

```java
@RequestMapping("/deduct_sotck")
public String deductStock(){
    int stock = Integer.parseInt(stringRedisTemplate.opsForValue().get("stock")); // 得到stock对应的value
    if(stock>0){
        int realStock = stock - 1;
        stringRedisTemplate.opsForValue().set("stock",realStock+"");
        System.out.println("扣减成功，剩余库存："+realStock+"");
    }else{
        System.out.println("库存不足，扣减失败");
    }
    return "end";
}
```

## 代码分析

在**高并发**的场景之下，可能有多个线程同时访问这个方法，`stock`的值还没有被设置，就又被读取，无法保证操作的原子性。

## 单体架构解决

如果要想保证操作的原子性，直接通过`synchronized`关键字加锁即可,**但是在分布式的架构中还会出现问题**,用`synchronized`精简过的代码如下。

```java
@RequestMapping("/deduct_sotck")
public String deductStock(){
    synchronized(this){
        int stock = Integer.parseInt(stringRedisTemplate.opsForValue().get("stock")); // 得到stock对应的value
        if(stock>0){
            int realStock = stock - 1;
            stringRedisTemplate.opsForValue().set("stock",realStock+"");
            System.out.println("扣减成功，剩余库存："+realStock+"");
        }else{
            System.out.println("库存不足，扣减失败");
        }  
    }
	return "end";
}
```

>分布式架构：一般有一个`nginx`来做负载均衡，然后将请求分发给不同的实体，synchronized关键字只能保证每一个进程中加锁。

## 分布式加锁

在redis中通过`key:value`的形式来加锁，如果用来标识锁的key存在值，则证明有线程在对库存进行访问，别的线程不能再进行访问。

```java
@RequestMapping("/deduct_sotck")
public String deductStock(){
    String lockKey = "stock_key";
    
    Boolean reuslt = stringRedisTemplate.opsForValue().setIfAbsent(lockKey,"test");
    if(!result){
      return "访问太过于频繁"; // setIfAbsent 对应的key存在返回true，否则返回false
    }
    
    int stock = Integer.parseInt(stringRedisTemplate.opsForValue().get("stock")); // 得到stock对应的value
    if(stock>0){
        int realStock = stock - 1;
        stringRedisTemplate.opsForValue().set("stock",realStock+"");
        System.out.println("扣减成功，剩余库存："+realStock+"");
    }else{
        System.out.println("库存不足，扣减失败");
    }   
    
    stringRedisTemplate.delete(localKey);
   
	return "end";
}
```

每次执行时候，通过`setIfAbsent`来设置key对应的值，该函数如果key不存在，则会完成设置并且返回true，否则返回false。如果返回false，则证明已经有别的线程来访问，直接返回，完成了加锁的操作。

如果在对库存进行操作的过程中报错了，抛出异常，程序终止，则localKey对应的值不会被删除，就会永久死锁。

```java
@RequestMapping("/deduct_sotck")
public String deductStock(){
    String lockKey = "stock_key";
    
    try{
        Boolean reuslt = stringRedisTemplate.opsForValue().setIfAbsent(lockKey,"test");
        if(!result){
          return "访问太过于频繁"; // setIfAbsent 对应的key存在返回true，否则返回false
        }

        int stock = Integer.parseInt(stringRedisTemplate.opsForValue().get("stock")); // 得到stock对应的value
        if(stock>0){
            int realStock = stock - 1;
            stringRedisTemplate.opsForValue().set("stock",realStock+"");
            System.out.println("扣减成功，剩余库存："+realStock+"");
        }else{
            System.out.println("库存不足，扣减失败");
        }   
    }finally{
   		stringRedisTemplate.delete(localKey); 
    }
   
	return "end";
}
```

只处理了抛出异常的情况，但是假如在程序对库存操作的过程中，程序崩溃了，也会造成锁死的情况。所以可以给`localKey`加一个过期时间。（不能分开两步写，也有可能在设置那一步崩溃，之后再设置过期时间那句没执行到，也会导致死锁）

```java
@RequestMapping("/deduct_sotck")
public String deductStock(){
    String lockKey = "stock_key";
    
    try{
        Boolean reuslt = stringRedisTemplate.opsForValue().setIfAbsent(lockKey,"test",10,TimeUnit.SECONDS);
        if(!result){
          return "访问太过于频繁"; // setIfAbsent 对应的key存在返回true，否则返回false
        }

        int stock = Integer.parseInt(stringRedisTemplate.opsForValue().get("stock")); // 得到stock对应的value
        if(stock>0){
            int realStock = stock - 1;
            stringRedisTemplate.opsForValue().set("stock",realStock+"");
            System.out.println("扣减成功，剩余库存："+realStock+"");
        }else{
            System.out.println("库存不足，扣减失败");
        }   
    }finally{
   		stringRedisTemplate.delete(localKey); 
    }
   
	return "end";
}
```

在上述的逻辑中，假设**对库存的执行时间超过10s**，此时这个线程就会丢失这把锁，别的线程就可以获得这把锁进行操作，每次删除的都不是自己加的锁，不满足原子性。

可以将`localKey`对应的vlaue，用来表示客户端进程，确保每次释放的锁是自己加的锁。

```java
@RequestMapping("/deduct_sotck")
public String deductStock(){
    String lockKey = "stock_key";
    String clientId = UUID.randomUUID().toString();
    
    try{
        Boolean reuslt = 									stringRedisTemplate.opsForValue().setIfAbsent(lockKey,clientId,10,TimeUnit.SECONDS);
        if(!result){
          return "访问太过于频繁"; // setIfAbsent 对应的key存在返回true，否则返回false
        }

        int stock = Integer.parseInt(stringRedisTemplate.opsForValue().get("stock")); // 得到stock对应的value
        if(stock>0){
            int realStock = stock - 1;
            stringRedisTemplate.opsForValue().set("stock",realStock+"");
            System.out.println("扣减成功，剩余库存："+realStock+"");
        }else{
            System.out.println("库存不足，扣减失败");
        }   
    }finally{
        if(clientId.equals(stringRedisTemplate.opsForValue().get(lockKey))){
            stringRedisTemplate.delete(localKey); 
        }
    }
   
	return "end";
}
```

上面的代码只保证了不会删除别的线程的锁，但是仍然可能出现多个线程同时访问一个资源的情况。

为了解决这种情况，可以在内部开启一个定时器，没经过10s的三分之一，就将key所对应的有效时间重新设置为10s；



## 框架:game_die:

为了完成redis分布式锁等操作，可以参考[redission](https://redisson.org/)，已经完成了对常用操作的封装

[redission中文文档](https://www.bookstack.cn/read/redisson-doc-cn/config.md)