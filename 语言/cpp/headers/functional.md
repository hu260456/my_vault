#### functional

```CPP
// 包装普通函数 func
function<void(string)> f1 = func;

// 包装类 CLS 中的静态函数 func
function<void(string)> f2 = CLS::func;

// 包装类 CLS 中的成员函数 func
CLS x
function<void(string)> f3 = bind(&CLS::func, &x, "Hello");

// 包装类 CLS 中实例 x 的变量 a
CLS x
function<int&(void)> item = bind(&CLS::a, &x);
item() = 666;
cout << item();

// 包装可调用对象
Demo d;
function<void(string)> f3 = d;

////////// bind | placeholder //////////
int sum(int a, int b) {
    return a + b;
}
int main() {
    ///// 函数包装成对象
    function<int(int, int)> f(sum);
    cout << f(3, 3);
    ///// 包装时绑定参数
    auto f = bind(sum, 3, 3);
    // 绑定了参数的位置在调用时不需要传参，传了也只作为占位符
    cout << f();
    ///// 只绑定部分参数
    // placeholder::_n 代表第 n 个被传入的参数
    auto f = bind(sum, 3, placeholders::_1);
    cout << f(3);
}
```
