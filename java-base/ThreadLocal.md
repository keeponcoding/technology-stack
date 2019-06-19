ThreadLocal简述：  
> * Thread类中有一个成员变量属于ThreadLocalMap类(一个定义在ThreadLocal类中的内部类)，它是一个Map，他的key是ThreadLocal实例对象。  
> * 当为ThreadLocal类的对象set值时，首先获得当前线程的ThreadLocalMap类属性，然后以ThreadLocal类的对象为key，设定value。get值时则类似。  
> * ThreadLocal变量的活动范围为某线程，是该线程“专有的，独自霸占”的，对该变量的所有操作均由该线程完成！也就是说，ThreadLocal 不是用来解决共享对象的多线程访问的竞争问题的，因为ThreadLocal.set() 到线程中的对象是该线程自己使用的对象，其他线程是不需要访问的，也访问不到的。当线程终止后，这些值会作为垃圾回收。  

一个ThreadLocal只能存储一个Object对象，如果需要存储多个ThreadLocal对象，就需要多个ThreadLocal去存储  

当线程没有结束，但ThreadLocal已经被回收，可能会导致ThreadLocalMap<null,Object>的键值对，进而导致内存泄漏  
ThreadLocal的get、set方法可以清除ThreadLocalMap中key为null的value，但get、set方法在内存泄漏之后并不会必然被调用  
以下提供两种方式解决此问题：  
1. 第一种    
```
try{  
    //业务逻辑代码  
}finally{  
    threadLocal.remove();  
}
```

2. 第二种  
```
JDK建议ThreadLocal定义为private static，这样ThreadLocal的弱引用问题则不存在了
```

Netty中有 FastThreadLocal   
其性能是jdk中的ThreadLocal的三倍

