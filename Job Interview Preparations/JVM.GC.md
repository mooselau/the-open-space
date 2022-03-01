# JVM / GC

## JVM Java虚拟机

```text
"Thread Shared"      "Thread Private"
--------------- -------------------------------
| Heap        | | JVM Stack | | Native Stack  |
--------------- -------------------------------
| Method Area | | Program Counter Register    |
--------------- -------------------------------
```
- 左侧属于 线程共享区域，右侧属于 线程私有区域;
- 方法区 还包含 常量池 (Constants Pool)，类信息 (Class info);
- JVM Stack 包含本地变量 local vars；
- Native Stack 包含 原生方法和变量；
- Program Counter Register 程序计数器，包含 线程需要执行的下一行代码或者指令；

- JVM 结构
  + JVM 参数 -Xms 最小内存，-Xmx 最大内存，-Xx: +UseConcurrentMarkSweepGC 使用 CMS GC

### JMM Java Memory Model Java 内存模型

？？？

## Garbage Collection GC 垃圾回收器

### Generation Concept 年代回收概念

```text

--------  -------------- --------------
| Eden |  | Survivor 1 | | Survivor 2 | Young Generation
---------------------------------------
|                                     | Old Generation
---------------------------------------
|                 | Permanent Generation
-------------------
```

- 新对象会首先分配在 Eden 区域；
  + 如果 Eden 区域无法分配了，则会产生 Young GC / Minor GC；
  + Young GC 会将 存活的对象全部转移到 S1/S2 上面
  + 如此来回近 15次之后，依然存活的 对象会从 S1/S2 被升级到 老年代；
- 老年代用来存储一直存活的对象，但当老年代达到一定比率（可配置）的时候，则会产生 Old GC；
  + Old GC 会清理掉老年代中不再存活的对象；
- 当老年代 Old GC 也无法清理出足够的空间给新晋升的对象的时候，则会触发 Full GC；
  + Full GC 会对所有区域产生影响 ？？？
  + Full GC 会伴有 Compact 压缩过程，会对系统性能产生影响；

(要尽可能减少 FullGC 的可能)

## GC 类型

__Serial GC__
古老的线性GC，已过时。

单线程的 GC 过程，STW Event 巨长；

__Parallel GC__
比较老的并行 GC，已过时。

多线程的 GC 过程，STW Event 依然很长；

__CMS GC__

全称 ConcurrentMark&Sweep GC，并行标记清除 GC；

过程主要是：
initial mark (初次标记 stop-the-world)
resume execution
remark (再标记)
sweep (清除)

比较主流的 GC 过程，STW Event 比较短；

> 注意的是，默认CMS是没有 Compact的，在运行的过程中会不断产生碎片，直到某一天会因为饱和而触发 FullGC；

__G1GC__

```text
——————————
|  |  |  |
——————————
|  |  |  |
——————————
|  |  |  |
——————————
```
- G1GC 把 整体内存 Split 成了大小不同的 regions;
  + 1mb <= region size <= 32mb
- 任何一个 region 都可以用来作为 Eden/Survivor/Old generation 存放对象；

```text
Young GC:
————————————
| O |  | E |
————————————
| E |  |   |
————————————

- E + E => S, 两块 Eden 由于GC触发 合并称为一个 Survivor；

————————————
| O |  |   |
————————————
|   |  | S |
————————————

Old GC:
————————————
| O | O | E |
————————————
| S |   |   |
————————————

- 两块 Old 区域产生GC 合并 成为 一块 O 区域；

————————————
|   |  | E |
————————————
| S | O |  |
————————————
```

- G1GC 的优势非常明显，就是在GC的过程中就产生了清除和整理的效果，从而空出更多完整的空间给新的对象；
  + 具体发生在 remark phase, 就会被move；
  + GC 发生时，最优最低生存比率的 region 会被 清除，cleaned and removed；
- G1GC 相对于CMS GC 更快，更低延时；
- G1GC 有 compact；
- G1GC 的问题是，需要更多更大的memory；

### GC ROOTs

GC Roots有：
- local variables，本地变量
- active threads，活跃线程
- static variables，静态变量
- JNI References，JNI 引用

## Java & GC Java 和 GC

### Reference Types in Java Java 四大引用类型

强引用，弱引用，虚引用，影子引用？？
  
？？ 测试 weakReferences
