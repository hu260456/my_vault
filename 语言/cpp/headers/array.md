#### 初始化

```CPP
#include <array>
int main() {
	// 创建一个 int 类型大小为 5 的 array
	array<int, 5> a0 = {1, 2, 3, 4, 5};
	// array 中的元素直接存放在实例中，而不像许多容器一样在堆上分配空间
	// array 的大小必须能在编译器确定
	
	// 在 CPP17 中允许不填写数组大小，但是同时不允许填写类型参数
	array a1 = {1, 2, 3, 4, 5};
	
	// 在 CPP20 中可以通过使用 to_array<T> 的方法获得一个 T 类型的 array
	auto a2 = to_array<int>({1, 2, 3, 4, 5});
}
```
