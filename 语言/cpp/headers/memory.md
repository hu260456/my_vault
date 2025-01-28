#### shared_ptr

```CPP
// 原理：引用计数
// 构造
shared_ptr<T> x(new T{...});
shared_ptr<T> b = a;
shared_ptr<T> m = move(n); // 转移所有权，引用计数不变
shared_ptr<T> p = make_share<T>(p); // p 是 T 的实例

// 注意：不能使用同一个地址初始化多个 shared_ptr
// 下面这个结构体的方法的返回值同样会造成一个原始地址初始化多个 shared_ptr
struct Test {
    shared_ptr<Test> getSharedPtr() { return shared_ptr<Test>(this); }
};
int main() {
    shared_ptr<Test> sp1{new Test()};
    shared_ptr<Test> sp2{sp1->getSharedPtr()};
}
// 解决办法，使用c++提供的enable_shared_from_this<T>：
struct Test: public enable_shared_from_this<Test>{
    shared_ptr<Test> getSharedPtr() { return shared_from_this(); }
};
///// 注意：shared_ptr 不能循环引用，
// 否则导致资源将永远无法释放，可以使用 weak_ptr 解决此问题
///// 实例方法
reset(); // 解除指针对某一块内存的管理
reset(new T value); // 让指针指向另外一块内存
use_count(); // 查看指针指向空间的引用计数
T* get(); // 获取原始地址，返回类型是数据类型的指针
///// 指定删除器，如果 ptr 指向的是一个数组，默认的删除器只能释放 0 号元素
// 可以自定义删除器函数
shared_ptr<int[]> ptr(new int[2]{}, [](const int *p) {
    delete[] p;
    cout << "int被释放了";
});
// 或者传入模板删除器函数： default_delete<int[]>()
////////// unique_ptr //////////
// 引用计数只允许为 1
// 创建方式
unique_ptr<int> ptr1(new int{9});
unique_ptr<int> ptr2 = move(ptr1);
// 同 shared_ptr 一样有 get, reset 方法
// 同 shared_ptr 一样可以指定删除器，但是有略微差异:
using f = void (*)(const int *); // 需要在模板中指定删除器的类型
unique_ptr<int, f> ptr{new int{0}, [](const int *p) {
    delete p;
}};
// 如果需要捕获变量：
unique_ptr<int, function<void(int*)>> ptr{new int{0}, [=](const int *p) {
    delete p;
}};
// unique_ptr 能够自动管理数组类型，正确释放数组空间
// 总结：shared_ptr 不能正确管理数组，除非指定删除器，而 unique_ptr 无此问题
////////// weak_ptr //////////
// 弱引用智能指针
// weak_ptr 是辅助 shared_ptr 工作的，可以解决循环引用问题
// 创建方法
weak_ptr<int> wp1;
weak_ptr<int> wp2(wp1);
shared_ptr<int> sp(new int{});
weak_ptr wp3(sp);
weak_ptr wp4 = sp; // 隐式转化
// 实例方法
use_count(); // 返回 weak_ptr 指向的空间的 shared_ptr 维护者数量
expired(); // 返回 true 表示资源已被释放
lock(); // 获取一个所监测资源的 shared_ptr 对象
reset(); // 重置 weak_ptr
```
