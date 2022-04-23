# Java General / Java 综合

* Java Core
  - Reflection
  - Other
    + Java Delegation, Classloader, see[link](https://www.cnblogs.com/wxd0108/p/6681618.html)
    + WeakReference, see [link](https://mp.weixin.qq.com/s/TyM-zD2Irxk_BzS_D6I0KA)
    + Java8 Stream Operations [link](https://www.baeldung.com/java-8-streams-introduction)

## Java Characteristics

__Encapsulation 封装__

__Inheritance 继承__

__Polymorphism 多态__

## Java Primitives

- Java 的八大原始数据类型包括：byte(1), short(2), int(4), long(8); float(4), double(8); boolean, char(2);

> 1 Byte = 8 bits
> 8 bits 最小是 0000 0000(16进制 00), 最大是 1111 1111(16进制 FF)
> 所以在 16进制的 字节流文件中，两个字符表示一个字节，如果是纯 2进制，则8个字符表示一个字节；

- 上下限问题，以 Integer 举例，大小4个字节，共 32bit，二进制表示可能性 2^32 个；
  + 意味着，无符号范围 0 ~ 2^32 - 1 个值，有符号范围 -2^31 ~ 2^31 - 1 个值 (减去的1 都是给 0的);
- Integer 的 MAX_VALUE +1 会溢出 成 MIN_VALUE;
- 不要用 float, double 表示精确的含小数点的值；

### Java String

||StringBuilder|StringBuffer|
|-|-|-|
|Speed      |Fast     |Slow|
|Thread-Safe|Not Safe |Safe (synchronized)|

## Java Keywards

__Statis 静态化__
当 static 放在变量上的时候，数据会存放在 JVM 方法区上，不在 Heap 堆 中，序列化会导致丢失；

当 static 放在方法上时，该方法会在 类加载时加载进来，而不会等到对象初始化/类实例化的时候加载。不过，要注意 static 申明的方法对所有对象实例通用，可以理解为只有一个通用的方法存在；

__Transient 暂存化/半透明__
当 transient 放在变量上的时候，在序列化反序列化的过程中，变量会变为初始值，int 为0，String 为 “”，对象为 null;

__Final 终态化__
使用 final 意味着变量在第一次赋值之后不可更改，对象不可renew，方法不可重写，对象类不可继承；

> fanalize() 类似 System.gc()，是手动触发 JVM 系统GC(不一定会立即执行)，但一般不需要手动调用。

__Volatile__
针对共享变量，可以确保所有对该变量的修改可以立即刷新到主存；

- 在运行过程中，写优先于读动作；
- 有序性，禁止指令重排序(JVM 的优化措施);

__volatile vs atomic__
- _volatile_ 禁止了指令重排序，_atomic变量_ 是保证对该变量的操作可以达到原子性，但是 _volatile_ 在非原子操作中并不能保证操作对所有线程的可见性，如“number ++”操作就是非原子的，其实包含了一读一写两个操作，尽管使用 _volatile_ 可以使得对该变量的读发生在写之后，但是依然不能保证多线程对它的操作是会产生冲突的。
see [link](https://stackoverflow.com/a/19744523)

## Java Interfaces

__Serializable 接口__ 

- 这个接口是将内存对象状态序列化出来，主要使用在：持久化到数据库，网络传输等等；
- 为了避免各种反序列化的问题，最好制定类 UID；

> 反序列化往往会从父类开始，所以父类对象最好提供无参构造器，以防止反序列化失败；

__default implementation 接口默认实现__

- Java 对接口提供了默认实现的功能，但不能滥用；
  + 比如不同的接口 Bar, Baz 都提供了同一个名字的方法 defaultCommon() ，也都提供了默认实现（但实现逻辑不同），如果类同时实现了这两个接口，这时候就编译就会报错；
  + 解决办法可以是在新的实现类中，使用 @override 来重写 defaultCommon();
- Best Practice 是只有在需要升级接口但是又同时要向下兼容老接口的时候使用 defaut 实现；

### Stream API

- 流式处理接口主要用在 Collection 上面，具体可以见 [Java Collection](./Java.Collection.md);

### Functional 功能接口

??

## Java Atomic 相关类

- Atomic 相关类主要使用 CompareAndSet(A, B) 即 CAS 来完成类似原子操作，比如 Atomic Integer;
  + CAS 有 "ABA" 问题，解决办法是引入版本号；

## Java Reflection 反射

- 通过反射，可以基本得到所有需要的 class 类信息，常用的方法有 class.forName() 用来获得类，Method.invoke() 用来调用方法；
- 除此以外，还可以获得修饰符，以及注解等等；
- 使用反射可以逃避范型检查；
- 