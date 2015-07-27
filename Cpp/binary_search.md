二分搜索算法有很多的应用，写出一个正确的二分查找还是比较有难度的，如下是一个基本的实现。
```cpp
int binary_search_ex(int* array, size_t len, int element)
{
  int begin = 0;
  int end = len;
  int middle;
  while (begin < end) {
    middle = begin + (end - begin)/2;
    if (array[middle] < element) {
      begin = middle + 1;
    } else if (array[middle] > element) {
      end = middle;
    } else {
      return middle;
    }
  }
  //no such element
  return -1;
}
```
上面的实现是半闭半开区间。


对于有重复元素的，有时我们需要获取其插入的上界和下界，示例代码如下
```cpp
int upper_bound(int* array, size_t len, int element)
{
  int begin = 0;
  int end = len;
  int middle;
  while (begin < end) {
    middle = begin + ((end - begin)>>1);
    if (array[middle] <= element) { // right
      begin = middle + 1;
    } else {
      end = middle;
    }
  }
  if ((begin > end) || array[begin - 1] != element)
  {
      return -1;
  }
  return begin;
}
```
此处涉及到是否有该元素的判断。
同理
```cpp
int lower_bound(int* array, size_t len, int element)
{
  int begin = 0;
  int end = len;
  int middle;
  while (begin < end) {
    middle = begin + ((end - begin)>>1);
    if (array[middle] < element) {
      begin = middle + 1;
    } else {
      end = middle;
    }
  }
  if (array[begin] != element)
  {
      return -1;
  }
  return begin;
}
```


对于要统计某个元素在有序数组中的个数，代码实现有两种，一种是直接调用upper_bound 和lower_bound 计算两者的差即可。
实现代码如下
```cpp
int count(int* array, size_t len, int element)
{
	
}
```

还有就是调用一次二分搜索，不断向前和向后找边界，代码实现如下
```cpp
int count(int* array, size_t len, int element)
{
    int index = binary_search(array, len, element);
    int count = 0;
    if (index >= 0)
    {
        int lower = index;
        while (lower >= 0 && array[lower] == element)
        {
            --lower;
        }
        int upper = index;
        while ((upper < len) && (array[upper] == element))
        {
            ++upper;
        }
        count += (upper - lower - 1);
    }
    return count;
}
```
可以讨论下两种方法的优劣。


## 逆序查找

一个有序的数组，被拆分为两段后拼接在一起，在这样的一个数组中，查找某一个元素。

分析，在无序数组中查找某一个元素，其复杂度为O(n)。

此刻需要利用到其有序的特性。


## 查找k大的数

两个有序的数组，长度分别为n和m，求其中位数。

最直接的方法，归并排序后，


## 查找



