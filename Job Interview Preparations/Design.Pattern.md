# Design Pattern / 设计模式

## Factory Pattern 基础工厂模式

典型用例: 
Factory.getProduct("A");
Factory.getProduct("B"); 
Factory.getProduct("C"); 

- 用来实例化一堆相似的 instances 的接口设计；
- 典型业界方案如：LOGGER 通过 LoggerFactory 的实例化；

## Facade Pattern 表面模式

典型用法：
(Facase Interface)ShapeMaker.drawCircle();
ShapeMaker.drawRectangle();
ShapeMaker.drawSquare();

- 用来实现一堆相似的动作，同时屏蔽底层的 __动作实现__；
- 往往是屏蔽 Legacy System，同时又为了兼容新系统或者新方案而做的接口设计；

> Factory Pattern 和 Facase Pattern 最大的区别，就是 Factory 用来生产 相似的 实例，但 Facade 用来执行 相似的 动作；
