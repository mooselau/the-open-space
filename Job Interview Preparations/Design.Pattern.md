# Design Pattern / 设计模式

## Factory Pattern 基础工厂模式

典型用例: 
Factory.getProduct("A");
Factory.getProduct("B"); 
Factory.getProduct("C"); 

- 用来实例化一堆相似的 instances 的接口设计；
- 典型业界方案如：LOGGER 通过 LoggerFactory 的实例化；

### 工厂方法 / 抽象工厂模式

- 其实是对于复杂的产品，比如 Product A = Head01 + Body01, Product B = Head02 + BodyO2;
- 这时候需要抽象一个工厂的接口，如 Factory Product, 拥有两个实现 ProductAFactory, ProductBFactory;
- 同时抽象出来两个产品接口，如 produceHead(), produceBody();
- 这样在需要生产A的时候就是：Factory.produce("A") -> ProductAFactory.produce() -> produceHead("01") + produceBody("01"), 在需要生产B的时候就是 Factory.produce("B") -> ProductBFactory.produce() -> produceHead("02") + produceBody("02");

所以对于抽象工厂，就是在生产多产品以及复杂产品的时候可以使用的方式。

更多的解释，可以查看[这篇文章](https://www.jianshu.com/p/8c6b9bf3b1a9)。

## Facade Pattern 表面模式 / 外观模式

典型用法：
(Facase Interface)ShapeMaker.drawCircle();
ShapeMaker.drawRectangle();
ShapeMaker.drawSquare();

- 用来实现一堆相似的动作，同时屏蔽底层的 __动作实现__；
- 往往是屏蔽 Legacy System，同时又为了兼容新系统或者新方案而做的接口设计；

> Factory Pattern 和 Facase Pattern 最大的区别，就是 Factory 用来生产 相似的 实例，但 Facade 用来执行 相似的 动作；

更多的解释，可以查看[这篇文章](https://www.jianshu.com/p/6c4441e441ca)。

## Decoration Pattern 装饰器模式

典型用例，比如IO操作：BufferedInputStream(FileInputStream(File)).