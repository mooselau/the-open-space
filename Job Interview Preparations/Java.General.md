# Java General / Java 综合

## Java Premitives

- Java 的八大原始数据类型包括：byte(1), short(2), int(4), long(8); float(4), double(8); boolean, char(2);

> 1 Byte = 8 bits
> 8 bits 最小是 0000 0000(16进制 00), 最大是 1111 1111(16进制 FF)
> 所以在 16进制的 字节流文件中，两个字符表示一个字节，如果是纯 2进制，则8个字符表示一个字节；

- Integer 的 MAX_VALUE +1 会溢出 成 MIN_VALUE;

### Java String

||StringBuilder|StringBuffer|
|-|-|-|-|
|Speed      |Fast     |Slow|
|Thread-Safe|Not Safe |Safe|

## Java Keywards

__Statis 静态化__
当 static 放在变量上的时候，数据会存放在 JVM 方法区上，不在 Heap 堆 中，序列化会导致丢失；

当 static 放在方法上时，该方法会在 类加载时加载进来，而不会等到对象初始化/类实例化的时候加载。不过，要注意 static 申明的方法对所有对象实例通用，可以理解为只有一个通用的方法存在；

__Transient 暂存化/半透明__
当 transient 放在变量上的时候，在序列化反序列化的过程中，变量会变为初始值，int 为0，String 为 “”，对象为 null;

__Final 终态化__
使用 final 意味着变量在第一次赋值之后不可更改，对象不可renew，方法不可重写，对象类不可继承；

> fanalize() 类似 System.gc()，是手动触发 JVM 系统GC(不一定会立即执行)，但一般不需要手动调用。

## Java Interfaces

__Serializable 接口__ 

- 这个接口是将内存对象状态序列化出来，主要使用在：持久化到数据库，网络传输等等；
- 为了避免各种反序列化的问题，最好制定类 UID；

> 反序列化往往会从父类开始，所以父类对象最好提供无参构造器，以防止反序列化失败；

### Functional 功能接口

??

## Java Atomic 相关类

- Atomic 相关类主要使用 CompareAndSet(A, B) 即 CAS 来完成类似原子操作，比如 Atomic Integer;
  + CAS 有 "ABA" 问题，解决办法是引入版本号；


## Java Reflection 反射

- 通过反射，可以基本得到所有需要的 class 类信息，常用的方法有 class.forName() 用来获得类，Method.invoke() 用来调用方法；
- 除此以外，还可以获得修饰符，以及注解等等；
- 使用反射可以逃避范型检查；