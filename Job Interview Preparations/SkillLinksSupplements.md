# Supplements for Skill Links
Supplementary notes for the skill review links.

# Table of contents
1. [Java Common](#java-common)
    1. [Thread Interrupt](#thread-interrupt)
    2. [Locks & Wait](#locks-and-wait)
        1. [Wait() and sleep()](#wait-and-sleep)
    3. [volatile vs atomic](#volatile-and-atomic)
2. [Java8](#java8)
    1. [Lambda and functional interface](lambda-and-functional-interface)

## Java Common <a name="java-common"></a>
### Thread Interrupt <a name="thread-interrupt"></a>
Two kinds of thread interruption. One is active interruption(主动中断), e.g. Thread.interrupt(); one is passive interruption(被动中断), e.g. be destroyed.
Additionally, about thread interruption, please note "Thread.interrupted()" detects AND clear current thread interruption signal, however "Thread.isInterrupted()" is a normal way to detect interruption signal of another thread without clearing the signal.

It is suggested (??) to throw an interruptedException rather than directly handling.

### Locks & Wait <a name="locks-and-wait"></a>
Please take a good look at Object.wait() and Condition.await().
Typically, Object.wait() usually used together with Object.notify() or Object.notifyAll(); likewise, Condition.await() is with Condition.signal() or Condition.signalAll().

In a simple case, Object.wait() usually used within a loop like below:
```java
synchronized(mutex) {
    while(volatile flag) {
        mutex.wait();
    }
}
```

Similarly, Condition.await() usually used like below:
```java
lock.trylock();
try {
    while(volatile flag) {
        condition.await();
    }
} finally {
    lock.unlock();
}

```

Also, please note wait() will let current thread give up its lock and release system resources like CPU to hang for a while until it is been notified.

#### Wait() and sleep() <a name="wait-and-sleep"></a>
Apparently, wait() belongs to Object classes, however, sleep() belongs to thread. Another difference is that wait() gives up its locks and resources while sleep() does not.

### volatile vs atomic <a name="volatile-and-atomic"></a>
_volatile_ 禁止了指令重排序，_atomic变量_ 是保证对该变量的操作可以达到原子性，但是 _volatile_ 在非原子操作中并不能保证操作对所有线程的可见性，如“number ++”操作就是非原子的，其实包含了一读一写两个操作，尽管使用 _volatile_ 可以使得对该变量的读发生在写之后，但是依然不能保证多线程对它的操作是会产生冲突的。
see [link](https://stackoverflow.com/a/19744523)

## Java8 <a name="java8"></a>
Some topics under Java8.

### Lambda and functional interface <a name="lambda-and-functional-interface"></a>
Lambda expressions can ONLY be used for functional interface. Functional interface is the type of interface ONLY has ONE abstract method.
For more information about lambda and functional interface, please refer to this post [comment](https://stackoverflow.com/a/36233545).
For more information about functional interface, please refer to this [javadoc](https://docs.oracle.com/javase/8/docs/api/java/lang/FunctionalInterface.html).
For how to use lambda expression, please refer to this post [Java8Lambda](https://www.geeksforgeeks.org/lambda-expressions-java-8/).
