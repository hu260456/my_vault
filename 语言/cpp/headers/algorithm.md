#### transform

```cpp
// 非原地算法
// 用于变换一段区间
// 例子：将所有字母变成小写
char op(const char &ch) {
	return ch >= 'A' && ch <= 'Z' ? ch + 32 : ch;
}
string str = "abcDEFG";
string result;
transform(str.begin(), str.end(), result.back_inserter(), op);
// 也可以使用 C 自带的 toupper / tolower
// transform(str.begin(), str.end(), result.back_inserter(), ::toupper);
```

#### unique

```CPP
// unique
// 原地算法
// 用于去除相邻元素的重复
// 返回已去重序列的 end 迭代器
vector<int> v{1, 1, 2, 2, 3, 3};
v.erase(unique(v.begin(), v.end()), v.end());
// 复杂类型需要重载 operator== 或在最后一个参数放入比较逻辑

// unique_copy
// 非原地算法
vector<int> res;
unique_copy(v.begin(), v.end(), back_inserter(res));
```

#### copy

```CPP
int a[5];
int b[5];

// copy
copy(a, a + 5, b);

// copy_backward
vector<int> v{1, 2, 3, 4, 5};
v.resize(10);
// 1, 2, 3, 4, 5, 0, 0, 0, 0, 0
copy_backward(v.begin(), v.begin() + 3, v.end());
// 1, 2, 3, 4, 5, 0, 0, 1, 2, 3

// copy_n
copy_n(a, 5, b);

// copy_if
copy_if(a, a + 5, b, [] (const int &i) {return i % 2;});
// uninitialized_copy & uninitialized_copy_n
```

#### reverse

```CPP
// reserve
// 原地算法
reverse(v.begin(), v.end());

// reverse_copy
// 非原地算法
reverse_copy(v.begin(), v.end(), ostream_iterator<int>(cout, " "));
```

#### replace

```CPP
// replace
// 原地算法
replace(v.begin(), v.end(), 6, 666);

// replace_if
replace_if(v.begin(), v.end(), [] (const int &i) { return i & 1; }, 666);

// replace_copy
// 非原地算法
replace_copy(v.begin(), v.end(), ostream_iterator<int>(cout, " "), 3, 0);

// replace_copy_if
replace_copy_if(v.begin(), v.end(), ostream_iterator<int>(cout, " "),
				[](const int &i) { return i % 2 == 0; }, 0);
```

#### remove

```CPP
// remove
// 原地算法
// 要记得 erase 掉删除的元素
remove(v.begin(), v.end(), 6);

// remove_if
remove_if(v.begin(), v.end(), [](const int &i) { return i == 3; });

// remove_copy
// 非原地算法
remove_copy(v.begin(), v.end(), ostream_iterator<int>(cout, " "), 3);

// remove_copy_if
remove_copy_if(v.begin(), v.end(), ostream_iterator<int>(cout, " "),
			   [](int i) { return i % 2 == 0; });
```

#### rotate

```CPP
// rotate
// 原地算法
// 向左旋转直到 v.begin() + 3 成为序列的首元素
rotate(v.begin(), v.begin() + 3, v.end());

// rotate_copy
// 非原地算法
rotate_copy(v.begin(), v.begin() + 3, v.end(), ostream_iterator<int>(cout, " "));
```

#### fill & iota

```CPP
int a[5];

// fill
fill(a, a + 5, 666);

// fill_n
fill_n(a, 5, 666);

// uninitialized_fill & uninitialized_fill_n

// iota
// 为区间赋值 5, 6, 7...
iota(a, a + 5, 5);
```


#### find & search

```CPP
int a[] = {1, 2, 3, 4, 5};

// find
int *tar = find(a, a + 5, 3);

// find_if
int *tar = find_if(a, a + 5, [] (const int &a) {
	return a == 666;
});

// find_first_of
// 返回 a 中第一个满足此元素也在 [b, b + 3) 中出现的元素的位置
int b[] = {4, 5, 6};
int *tar = find_first_of(a, a + 5, b, b + 3);

// find_end
// 查找 b 序列在 a 序列中最后出现的位置
int a[] = {1, 2, 3, 1, 2, 3};
int b[] = {1, 2};
int *tar = find_end(a, a + 6, b, b + 2);

// search
// 查找 b 序列在 a 序列中首次出现的位置
int a[] = {1, 2, 3, 1, 2, 3};
int b[] = {2, 3};
int *tar = search(a, a + 6, b, b + 2);

// search_n
// 在给定的序列中查找连续的 3 个元素，他们都是 6
int *tar = search_n(a, a + 6, 3, 6);
// 在给定的序列中查找连续的 3 个元素，他们都大于 6
int *tar = search_n(v.begin(), v.end(), 3, 6,
	[](const int &i, const int &val) {
		return i > val;
});
```

#### 二分算法

```CPP
vector<int> v{1, 2, 3, 4, 5};

// binary_search
bool found = binary_search(v.begin(), v.end(), 666);

// upper_bound & lower_bound
auto iter = upper_bound(v.begin(), v.end(), 666);

// equal_range
auto p = equal_range(v.begin(), v.end(), 666);
auto lower = p.first;
auto upper = p.second;
```

#### count

```CPP
int a[] = {1, 1, 2, 2, 3, 3};

// count
int sum = count(a, a + 6, 1);

// count_if
int sum = count_if(a, a + 6, [](const int &a) { return a % 2; });
```

#### adjacent_what

```CPP
// adjacent_find
// 返回首次出现重复的元素指针，返回左边的那个
int a[] = {1, 2, 3, 3, 4};
int *tar = adjacent_find(a, a + 5);
// 自定义 comp 案例：返回序列第一个极小值的位置
int *tar = adjacent_find(a, a + 5, [] (const int &a, const int &b) {
	return a < b;
});

// adjacent_difference
// 求差分数组
int a[] = {1, 2, 3, 4, 5};
adjacent_difference(a, a + 5, ostream_iterator<int>(cout, " "));
// 相当于
inline int op(const int &a, const int &b) {
	return a - b;
});
adjacent_difference(v.begin(), v.end(), ostream_iterator<int>(cout, " "), op);
```

#### generate(_n)

```CPP
//
```

#### heap

```CPP
vector<int> v{5, 4, 6, 3, 7, 2, 8, 1, 9, 0};

// 建立小根堆，默认是大根堆
make_heap(v.begin(), v.end(), greater<int>());

// 判断一段区间是不是堆
bool flag = is_heap(v.begin(), v.end(), greater<int>());

// 将堆的第一个元素与最后一个元素交换位置，然后将前 n-1 个元素重新调整
pop_heap(v.begin(), v.end(), greater<int>());

// 获取 v 中第一个属于 heap 的元素的位置
auto iter = is_heap_until(v.begin(), v.end(), greater<int>());

// 获取原来的堆顶并且从容器 pop 掉
int tmp = v.back();
v.pop_back();

// 添加新元素到堆中，然后重新调整堆
v.emplace_back(10);
push_heap(v.begin(), v.end(), greater<int>());

// 对堆进行排序，序列将失去堆的性质，并得到一个有序序列
sort_heap(v.begin(), v.end(), greater<int>());

// 输出调试
copy(v.begin(), v.end(), ostream_iterator<int>(cout, " "));
```

#### includes

```CPP
// includes
// 判断 v2 是否是 v1 的连续子列
// 要求序列有序
vector<int> v1{1, 2, 3, 4, 5};
vector<int> v2{2, 3};
bool flag = includes(v1.begin(), v1.end(), v2.begin(), v2.end(), less<int>());
```

#### sort

```CPP
int a[1, 3, 5, 4, 2];

// sort
sort(a, a + 5, greater<int>());

// stable_sort
stable_sort(a, a + 5, greater<int>());

// is_sorted
bool flag = is_sorted(a, a + 5, greater<int>());

// is_sorted_until
// 返回第一个未经排序的位置，如果都已有序，返回 end 位置
int *a = is_sorted_until(a, a + 5, greater<int>());

// nth_element
// 将第 2 大（从 0 开始）的元素放到 2
// 并且这个数左边的都比它小，右边的都比它大
// 缺省使用 less<int>()
nth_element(v.begin(), v.begin() + 2, v.end());
```

#### lexicographical_compare

```CPP
// 判断 v1 字典序是否小于 v2
vector<int> v1{1, 2, 3};
vector<int> v2{1, 2};
bool flag = lexicographical_compare(v1.begin(), v1.end(), v2.begin(), v2.end());
```

#### mismatch

```CPP
// 从头匹配两个序列
// 返回两个序列失配的位置
// 缺省参数可以定义每对元素匹配的规则
vector<int> v1{1, 2, 3, 6};
vector<int> v2{1, 2, 3, 4};
auto a = mismatch(v1.begin(), v1.end(), v2.begin());
auto point_v1 = a.first;
auto point_v2 = a.second;
```

#### minmax

```CPP
// minmax
auto a = minmax(1, 2);
int min = a.first;
int max = a.second;

// minmax_element
auto a = minmax_element(v1.begin(), v1.end());
auto min_iter = a.first;
auto max_iter = a.second;
```

#### merge

```CPP
// merge
// 非原地算法
// 可以指定 less<T>(), greater<T>, 或自定义
vector<int> v1{0, 2, 4, 6, 8};
vector<int> v2{1, 3, 5, 7, 9};
merge(v1.begin(), v1.end(), v2.begin(), v2.end(), ostream_iterator<int>(cout, " "));

// inplace_merge
// 原地算法
// 需要两段待合并序列是连续的
vector<int> v3{1, 3, 5, 2, 4, 6};
inplace_merge(v3.begin(), v3.begin() + 3, v3.end());
```

#### partial_what

```CPP
// partial_sort
// 原地算法
// v.begin() + 3 是排序结果的 end 迭代器
partial_sort(v.begin(), v.begin() + 3, v.end());

// partial_sort_copy
// 将 v 中前三小的元素拷贝到 result 中
vector<int> result(3);
partial_sort_copy(v.begin(), v.end(), result.begin(), result.end());

// partial_sum
// 非原地算法
// 求前缀和
partial_sum(v.begin(), v.end(), ostream_iterator<int>(cout, " "));
```

#### swap

```CPP
int a[]{0, 1, 2, 3, 4};
int b[]{5, 6, 7, 8, 9};

// swap
swap(a[0], b[0]);

// swap_ranges
swap_ranges(a, a + 5, b);

// iter_swap
// 交换指针指向的内容（而不是指针本身）
iter_swap(a + 1, b + 1);
```

#### permutation

```CPP
int a[]{0, 1, 2, 3, 4};

// next_permutation | prev_permutation
// 原地算法
next_permutation(a, a + 5);

// is_permutation
// 判断 b 是否是 a 中元素的一个排列
// 允许一个关于 operator== 的缺省参数
int b[]{1, 0, 2, 3, 4};
bool flag = is_permutation(a, a + 5, b);
```

#### partition

```CPP
vector<int> v{1, 2, 3, 4, 5, 6};

function<bool(const int &)> is_odd = [](const int &i) {
	return i % 2 == 0;
};

// partition
// 原地算法
// 将序列中的元素根据 is_odd 规则进行分组，满足 true 的元素在前
// bound：第一组的 end，即第二组的 begin
auto bound = partition(v.begin(), v.end(), is_odd);

// stable_partition
// 稳定版的 partition
auto bound = stable_partition(v.begin(), v.end(), is_odd);

// partition_point
// 获取一个已分组序列的交界位置
auto bound = partition_point(v.begin(), v.end(), is_odd);

// is_partitioned
// 检查一个序列是否已按照 is_odd 规则分好组
bool flag = is_partitioned(v.begin(), v.end(), is_odd);

// partition_copy
// 非原地算法
// 将 v 按规则 is_odd 分组并分别保存到 a 和 b 中
vector<int> a;
vector<int> b;
partition_copy(v.begin(), v.end(), back_inserter(a), back_inserter(b), is_odd);
```

#### iterator

```CPP
vector<int> v{0, 1, 2, 3, 4};

// next
// 第二参数默认为 1，可以为负
auto iter = next(v.begin(), 3);

// prev
auto iter = prev(v.end(), 3);
```
