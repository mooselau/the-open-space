# Algorithm 算法

## Sorting 排序算法

__Bubble Sort__
- Loop 1: For N times, one largest number at a time;
- Loop 2: For M times, each traverse from 0 to (len - 1 - n);
  + Swap inside

Time O(n^2), Space O(1)

__Selection Sort__
- Loop 1: For N times, one largest number at a time;
- Loop 2: For M times, each traverse from 0 to (len - 1 - n);
- Swap outside;

Time O(n^2), Space O(1)

__Merget Sort__

__Insertion Sort__


__Heap Sort__

## Searching 查找算法

__Linear Search__

__Binary Search__


### Array Combination 数组合并



## Caching 缓存算法

优秀的缓存算法的标准就是正常使用过程中的命中率。

### LRU 最近使用

### LFU 最近最常使用

缓存方式 LRU 和 LFU 对比
- 主要是在对比正常使用过程中的命中率
- LRU Least Recent Used，最近使用的元素会在最上方，最晚使用的元素会被新进来的元素淘汰；
  + 可以使用 ArrayList 或者 LinkedList 来实现；
- LFU Least Frequent Used, 最近最常使用 的元素会在最上方，最不常使用的元素会被新进来的元素淘汰；
  + 可以使用 Map 来做，key 为元素的值，value 为频数；

?? Coding


背包最优问题 / 动态规划