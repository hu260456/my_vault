#### 结构体成员内存对齐

```C
// 同一类型的数据，直接合并最后再内存对齐
struct Test1 {
    int a: 11; // 变量 a 的位段，表示这个变量占用 11 位
    int b: 9;
    int c: 7;
    int d: 5;
    // 以上刚好 32 位，再加一位也多占 1B（内存对齐）
    int e: 1;
};

// 不同数据类型，按照最宽的对其，Test 占 (2->4) + 4 = 8B
class Test2 {
    short a;
    int b;
};

// 占 1B，可以用来模拟一些二进制算法，方便位操作
struct Byte {
    unsigned char a0: 1;
    unsigned char a1: 1;
    unsigned char a2: 1;
    unsigned char a3: 1;
    unsigned char a4: 1;
    unsigned char a5: 1;
    unsigned char a6: 1;
    unsigned char a7: 1;
};
```

#### clock

```C
#include <stdlib.h>
#include <time.h>
int main() {
    // 获取从程序开始运行所耗费的时间，单位：时钟打点
    // CLK_TCK: 机器时钟每秒打点数
    clock_t start = clock();
    function_to_test();
    clock_t end = clock();
    double duration = double(stop - start) / CLK_TCK;
}
```

