# Java Collection / Java 集合类

- Q: Java 中 == 与 equals？
- A: "==" 用来比较原始数据的值，和对象数据的内存地址 Object References，equals 可以用来比较对象的值，一般对于自定义对象，都是十分建议重写 equals() 和 hashcode() 方法的；
  + 自定义对象在使用 集合类的使用，尤其是 Hashtable 这种集合类，hashcode 会被用来查询 Bucket，之后会通过 equals() 来判断是否和 Bucket 内部的元素重复，不重复才会放到集合中;

## Data Structure 基本数据结构

Array, LinkedList, (BST) Binary-Search Tree 二叉搜索树, Hashtable;

Abstract Data Type (ADT): Stack, Queue, List, Set, Map, Tree, \*Graph










TreeMap 与 TreeSet 是有Order的