## 函数对象

### 1.priority_queue定义

```c
template <class T, class Container = vector<T>,
  class Compare = less<typename Container::value_type> > class priority_queue;
```

### 2.priority_queue初始化

1、函数指针

```c
1.匿名函数
auto cmp = [](int a, int b) { return a > b; };
priority_queue<int, vector<int>, decltype(cmp)> pq(cmp);

2.显式函数

bool static cmp1 (const pair<int, int>& a, const pair<int, int>& b) {
    return a.second > b.second;
}
priority_queue<int, vector<int>, decltype(cmp1)> pq(cmp1);

sort(vec.begin(), vec.end(), cmp); // 给频率排个序
```
2、函数对象

```c
struct greater {
    bool operator()(const int &iLeft, const int &iRight)
    {
        return (iLeft > iRight);  //如果是实现less<int>的话，这边是写return (iLeft<iRight);
    }
};
/**使用重载函数操作符的类对象*/
priority_queue<int, vector<int>, greater> pq(nums.begin(), nums.end());

```

3、使用预定义函数对象

	functor，翻译成函数对象，伪函数，算符，是重载了“()”操作符的普通类对象；
	greater<>与less<>就是函数对象。



```c
//greater实现源码
template <typename _Tp>
struct greater : public binary_function<_Tp, _Tp, bool> {
    bool operator()(const _Tp &__x, const _Tp &__y) const
    {
        return __x > __y;
    }
};

sort(v1.begin(), v1.end(), greater<string>() ); //从大到小
```



## 对map元素排序

```c
class Solution {
public:
    void sortMap()
    {
        unordered_map<int, int> map;  // map.insert( pair<int,int>( 1, 10 ) );
        vector<pair<int, int>> vec(map.begin(), map.end());
        auto cmp = [](const pair<int, int> &a, const pair<int, int> &b) { return a.second > b.second; };
        sort(vec.begin(), vec.end(), cmp);
    }
};
```

