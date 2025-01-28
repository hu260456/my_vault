#### 三类时钟的共同点

| 名称         | 说明                                          |
| ---------- | ------------------------------------------- |
| now()      | 静态成员函数，返回当前时间，类型为 clock::time_point         |
| time_point | 成员类型，当前时钟的时间点类型。                            |
| duration   | 成员类型，时钟的时长类型。                               |
| rep        | 成员类型，时钟的tick类型，等同于 clock::duration::rep     |
| period     | 成员类型，时钟的单位，等同于 clock::duration::period      |
| is_steady  | 静态成员类型：是否是稳定时钟，对于 steady_clock 来说该值一定是 true |
#### system_clock

- 是唯一有能力映射其时间点到 C-Style 时间的 C++ 时钟
- system_clock 提供了两个静态成员函数来与 std::time_t 进行互相转换：

| 名称          | 说明                      |
| ----------- | ----------------------- |
| to_time_t   | 转换系统时钟时间点为 std::time_t  |
| from_time_t | 转换 std::time_t 到系统时钟时间点 |

![[attachments/Pasted image 20240410140126.png]]

#### 时长

```CPP
// 预定义
chrono::nanoseconds
chrono::microseconds
chrono::milliseconds
chrono::seconds
chrono::minutes
chrono::hours

// 自定义一个时间间隔单位，表示一个 60 秒，用 long long 存储
using t = long long;
using span = duration<t, ratio<60>>;

// 例子：计算 2 小时 + 5 分钟的总秒数
chrono::hours two_hours(2);
chrono::minutes five_minutes(5);
auto t = two_hours + five_minutes;
auto seconds = chrono::duration_cast<chrono::seconds>(t);
cout << "总共的秒数" << seconds.count() << endl;

// 从 C++14 开始，你甚至可以用字面值来描述常见的时长：
// - h表示小时
// - min表示分钟
// - s表示秒
// - ms表示毫秒
// - us表示微妙
// - ns表示纳秒
using namespace std::chrono_literals;
auto two_hours = 2h;
auto five_minutes = 5min;
```

#### 时间点

```CPP
// 时钟的 now() 返回的就是一个时间点
// 时间点包含了时钟和时长两个信息
// 时间点 chrono::time_point 定义如下：
template<
    class Clock,
    class Duration = typename Clock::duration
>
class time_point;

// 通过时间点的差运算可以获得时长
auto start = chrono::steady_clock::now();
_sleep(1000);
auto end = chrono::steady_clock::now();
auto delta = chrono::duration_cast<chrono::milliseconds>(end - start);
cout << "休眠了 " << delta.count() << " ms" << endl;

// time_point 重载了 ==、!=、<、>、<=、>= 等运算符
// 用来比较时间点的先后与是否相等
```
