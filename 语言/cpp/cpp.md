#### 宏

```CPP
// CPP 程序会定义 __cplusplus
#define __cplusplus
```

#### 兼容 C 的原理

```CPP
// 编译器会将里面的代码当作C语言处理
extern "C" {
    c_code...
}

// 以 C 中 “printf” 为例
// 在预处理中，printf 会自动包裹在 extern "C" 内
// 所以 printf 不需要extern "C"
printf("hello, world!")
```

#### 多行字符串

```CPP
const char* str = "Hello, "
	"world!";
```

#### ofstream

```CPP
// ofstream类支持磁盘文件输出
// 如果在构造函数中指定一个文件名，当构造这个对象时该文件是自动打开的
ofstream file("<filename>");
// 可以在调用默认构造函数之后使用open成员函数打开文件
ofstream file();
file.open("<filename>");
// 在构造对象或用open打开文件时可以指定模式
ofstream file("<filename>", ios_base::out | ios_base::binary);
// 写入数据
file << data << ...;
// 格式控制
file.width(10); // 只对下个输出有效
file << setw(10) << ...; // 只对下个输出有效
file << setiosflags(ios_base::left) << ...; // 左对齐
///// 二进制文件的输出
// 其中data是自定义的一个结构体
file.write(reinterpret_cast<char*>(&data), sizeof(data));
///// 关闭文件
file.close();
```

#### 重载决议

- 重载决议发生在编译期
- 模版函数也参与重载决议
- 如果模版函数和非模版函数有相同 rank，编译器会选择非模版函数

#### GCC builtin 函数

```CPP
// 计算汉明重量
__builtin_popcount(int x)
__builtin_popcountl(long x)
__builtin_popcountll(long long x)

// 获取二进制数末尾 0 的个数
__builtin_ctz(int x)
__builtin_ctzll(long long x)

// 获取二进制数前导 0 的个数
__builtin_clz(int x)
__builtin_clzll(long long x)

// 判断二进制数的校验奇偶性，奇返回 1，偶返回 0
__builtin_parity(int x)

// 获取二进制数第一个 1 的位序
__builtin_ffs(int x)

// 快速开平方
__builtin_sqrt(int x)
__builtin_sqrtf(float x)
```

#### 动态异常机制（在cpp11中弃用）

```CPP
// 抛出异常
throw exception;
// 捕获异常
try {}
catch() {}
catch() {}
// 在函数声明中列出这个函数可能抛出的所有异常类型
void func() throw(A, B, C, D);
// 对明确不会抛出异常的函数使用noexcept修饰
void func() noexcept;
// 用noexcept运算符判断函数是否noexcept，返回bool
bool falwless = noexcept(func);
```

#### 一些头文件

- climits | limits.h
    包含了 CHAR_MAX, SHRT_MIN 等宏
- cstdint | stdint.h
    提供了 uint8_t, uint32_t 等宏类型
    包含了 INT32_MAX 等宏
- limits
	获取类型 T 的最值
	numeric_limits<T>::max()
	numeric_limits<T>::min()



#### rank

- 用来获取类型 T 的维度

```CPP
int a;
int b[1];
int c[1][1];
cout << rank<decltype(a)>() << endl; // 0
cout << rank<decltype(b)>() << endl; // 1
cout << rank<decltype(c)>() << endl; // 2
```