#### Raw String

```CPP
string str = R"abc(\n将会原样输出)abc";
```

#### nullptr

```CPP
define NULL ((void*) 0); // C
define NULL 0; // C++
void *demo = nullptr; // 在C++使用nullptr替代NULL
// 原因：c++不允许void*隐式转化为其他类型
```

#### constexpr

- constexpr 可以将没有必要在运行时计算的变量提前到编译器
- constexpr 必须能在编译期确定，而 const 不必

#### using

```CPP
////////// 定义别名 //////////
// 效果类似 typedef int T
using T = int;
////////// 继承函数 //////////
class Child: public Base {
public:
    using Base::Base; // 继承父类的构造函数
    using Base::func; // 继承父类的函数func
}
```

#### auto

```CPP
// auto 声明的变量不会推导成 const、volatile，除非右值是个指针或引用
const int a = 1;
const int *ptr = &a;
auto b = a; // b: int
auto c = ptr; // c: const int*
// auto 不能定义参数类型
void func(auto x) {}
// auto 不能定义数组类型
auto arr[]{1, 2, 3};
// 不能推导模板形参类型
vector<auto> a;
```

#### decltype

```CPP
int a = 10;
decltype(a) b = 20; // b: int
decltype(a+3.14) c = 30; // c: double
const decltype(a) d = 40; // d: const int
```

#### 返回类型后置

```CPP
// C++14前返回类型为 auto 需要后置表名返回类型
// C++14可以直接返回auto
auto func(int t, int u) -> decltype(t+u) {
    return t + u;
}
```

#### final

```CPP
// 只能修饰类、虚函数
// 被修饰的成员函数不会继承给子类，子类也不允许定义，但是不影响孙类定义一个同名函数
int func() final {...}
// 类不能继承自被 final 修饰的类
class Child final : public Base {...}
```

#### override

```CPP
// 只能修饰虚函数
// 虚函数只能是非静态成员函数，故 override 只能修饰非静态成员函数
void func() override {...}
```

#### 解析多尖括号

> C++11以前会把 `>>` 直接当作右移运算符

```CPP
// c++11 以前需要在 > 之间加空格
func<map<int, string>>
```

#### lambda 表达式

```PlainText
- 捕获列表
[]        -> 不捕获变量
[foo]     -> 按值捕获 foo, 不捕获其他变量
[=, &foo] -> 按引用捕获 foo, 其他变量按值捕获
[this]    -> 捕获当前实例的 this 指针

- 参数列表
	如果没有参数，可以省略参数列表 '()'

- 选项
	mutable      -> 允许修改按值捕获的变量副本
	throw(int)   -> 指定函数抛出 int 型的异常
	noexcept     -> 函数不抛出异常

- 返回类型
	指定 int : []() -> int {...}
	不指定返回类型编译器会自动推导
	initializer_list<T> 无法被自动推导

- lambda 表达式的本质
	lambda 表达式是一个重载了 operator() 的对象
	lambda 表达式的 operator() 默认是常函数，mutable 的作用在于取消 const 的修饰
	没有捕获变量的 lambda 表达式可以作为函数指针使用
	捕获变量的 lambda 表达式则需要使用 function 或 bind 包装
```

#### 右值引用

- 现代 C++ 中
	- `lvalue` 被重新解释为 `location value`
	- `rvalue` 被重新解释为 `read value`
		- 将亡值(xvalue): 原始字面量、lambda 表达式
		- 纯右值(prvalue): 右值引用
- 引用折叠
	- 通过 `T&&` 或 `auto&&` 推导类型时可能推导成 `Type&` 类型
	- 规则
		- 右值推导为右值引用
		- 非右值（左值、左值引用、常量左值引用、右值引用、常量右值引用）推到为左值引用
- 右值引用
	- 右值引用只能使用右值初始化
	- 形如 const T&& 的一定是右值引用
	- 编译器会将已命名的右值引用视为 lvalue，未命名的右值引用视为 rvalue

#### move

```CPP
// 将左值转化为右值，得到的是一个将亡值
```

#### forward

```CPP
// 该函数实现的功能也叫完美转发
// 当T为左值引用类型时，t被转化为T类型的左值
// 当T不为左值引用类型时，t被转化为T类型的右值
forward<T>(variable);
```

#### 移动构造函数

- 移动构造函数也称为右值引用构造函数
- 移动构造函数应当被声明为 noexcept

#### 委托构造函数

- 在初始化列表中调用其他构造函数的逻辑，减少代码冗余

#### initializer_list

```CPP
Demo a = {x};
// 等价于
Demo a{x};
// 不等价于
Demo a(x);

// 返回用 {...} 构造的 X 对象
X demo() {
    return {...};
}

// initializer_list
template<typename T>
void forList(initializer_list<T> list) {
	copy(list.begin(), list.end(), ostream_iterator<T>(cout, " "));
}
forList({1,2,3});
```

#### 增强 for 循环

```CPP
vector<int> vt{1,2,3};
for(auto item: vt) {...} // item是对vt内容的拷贝
for(auto &item: vt) {...} // item是vt内容的引用
```

#### 可调用对象

```CPP
// way 1
class X {
public:
    void operator()(string msg) {
        cout << msg << endl;
    }
}
X x;
x("hello, world!");

// way 2
class X {
public:
    using funcPtr = void (*) (const string&);
	operator funcPtr() {
        return func;
	}
	static void func(const string &str) {
		cout << str << endl;
	}
};
int main() {
	X x;
	x("hello, world!");
}
```

#### enum class

```CPP
// enum
enum Color {
	red, blue, purple
}
int main() {
	cout << red; // 可直接访问
	int val = red; // 可隐式转化
}

// enum class
enum class Color {
	red, blue, purple
}
int main() {
	cout << Color::red; // 访问需域运算符
	int val = red; // error, 不允许隐式转化
}
```

#### X_cast

```CPP
int num = static_cast<int>(Color::red); // 上例中的 enum class
Base *base = static_cast<Base*>(child); // 将子类指针转化为父类指针
int *demo = static_cast<int*>(new int(666)); // 转化 void*
```

#### 可变参数模板

```CPP
// 展开参数包方法一
void print() {
    cout << "over" << endl;
}
template<class H, class ...T>
void print(H first, T... rest) {
    cout << first << endl;
    print(rest...);
}
int main() {
    print(1, 2, 3, 4);
}

// 展开参数包方法二
// sizeof... 获取参数包大小
template<typename... T>
void func(const T &... args) {
    cout << sizeof...(args) << endl;
}

// 使用args构造tuple
template<typename ...T>
tuple<T...> func(const T& ...args) {
    return make_tuple(args...);
}
```
