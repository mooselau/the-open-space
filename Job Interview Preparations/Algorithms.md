# Algorithm 算法

缓存方式 LRU 和 LFU 对比
- 主要是在对比正常使用过程中的命中率
- LRU Least Recent Used，最近使用的元素会在最上方，最晚使用的元素会被新进来的元素淘汰；
  + 可以使用 ArrayList 或者 LinkedList 来实现；
- LFT Least Frequent Used, 最近最常使用 的元素会在最上方，最不常使用的元素会被新进来的元素淘汰；
  + 可以使用 Map 来做，key 为元素的值，value 为频数；

?? Coding