#### 数据类型

| 类型名                | 说明         |
| ------------------ | ---------- |
| `clock_t`          | 进程运行时间     |
| `time_t`           | 从纪元起的时间类型  |
| `tm`               | 日历时间类型     |
| `timespec` (C++17) | 以秒和纳秒表示的时间 |

#### tm 的结构体定义

```CPP
struct tm {
  int tm_sec;
  int tm_min
  int tm_hour;
  int tm_mday;
  int tm_mon; // 表示范围 [0, 12)
  int tm_year; // 需要 + 1900
  int tm_wday;
  int tm_yday;
  int tm_isdst;
};
```

#### timespec 的结构体定义

```CPP
struct timespec {
  std::time_t tv_sec;
  long tv_nsec; // 保存的纳秒数
};
```

#### 函数

| 函数名                                                                                   | 说明                     |
| ------------------------------------------------------------------------------------- | ---------------------- |
| `clock_t clock()`                                                                     | 返回自程序启动起的处理器时钟时间       |
| `time_t time(time_t* arg)`                                                            | 返回自纪元起计的系统当前时间         |
| `double difftime(time_t time_end, time_t time_beg)`                                   | 计算时间之间的差               |
| `int timespec_get(timespec* ts, int base)` (C++17)                                    | 返回基于给定时间基底的日历时间（C++17） |
| `char* ctime(const time_t* time)`                                                     | 转换 time_t 对象为文本表示      |
| `char* asctime(const tm* time_ptr)`                                                   | 转换 tm 对象为文本表示          |
| `size_t strftime(char* str, size_t count, const char* format, const tm* time)`        | 转换 tm 对象到自定义的文本表示      |
| `size_t wcsftime( wchar_t* str, size_t count, const wchar_t* format, const tm* time)` | 转换 tm 对象为定制的宽字符串文本表示   |
| `tm* gmtime(const time_t* time)`                                                      | 将time_t转换成UTC表示的时间     |
| `tm* localtime(const time_t *time)`                                                   | 将time_t转换成本地时间         |
| `time_t mktime(tm* time)`                                                             | 将tm格式的时间转换成time_t表示的时间 |

#### API 之间的关系

![[attachments/v2-bba5c8297364f5e7c5a1d57a3e8ccb16_1440w.webp]]

#### 获取当前时间

```CPP
// C
time_t t = time(nullptr);
cout << ctime(&t);

// CPP
auto now = chrono::system_clock::now();
time_t t = chrono::system_clock::to_time_t(now);
cout << ctime(&t) << endl;
```

#### 计算时间差

```CPP
/** C **/
time_t t1 = time(nullptr);
_sleep(1000);
time_t t2 = time(nullptr);
double delta = difftime(t2, t1);
cout << "time1: " << t1 << endl;
cout << "time2: " << t2 << endl;
cout << "time_diff: " << delta << "s" << endl;

/** CPP **/
auto t1 = chrono::steady_clock::now();
_sleep(1000);
auto t2 = chrono::steady_clock::now();
auto delta = t2 - t1;
auto duration = chrono::duration_cast<chrono::seconds>(delta);
cout << duration.count() << "s" << endl;
```
