#### 构造

```CPP
// 构造一个大小为 5 的 valarray
valarray<int> v(4);

// 构造一个大小为 4、值为 3 的 valarray
valarray<int> v(3,4);

// 使用数组初始化 valarray
int arr[]{1, 2, 3, 4, 5};
valarray<int> v(arr, 5);
```

#### apply

```CPP
valarray<int> v{1, 2, 3, 4, 5};
valarray<int> result = v.apply([] (const int &i) {
	return i + 1;
})
```

#### 最值

```CPP
int min_val = v.min();
int max_val = v_max();
```

#### shift

```CPP
valarray<int> v{1, 2, 3, 4, 5};
valarray<int> result1 = v.shift(2);
	// 3, 4, 5, 0, 0
valarray<int> result2 = v.cshift(2);
// 3, 4, 5, 1, 2
```

#### slice

```CPP
// 切片复制
valarray<int> v1{0, 1, 2, 3, 4, 5, 6, 7, 8, 9};
valarray<int> v2 = v1[slice(0, 4, 2)];
// v2: 0, 2, 4, 6

// 切片上的修改
v1[slice(0, 5, 2)] = 0;
// v1: 0, 1, 0, 3, 0, 5, 0, 7, 0, 9

// 构造一个切片
slice slice0(0, 5, 2);
v1[slice0] = 666;
```