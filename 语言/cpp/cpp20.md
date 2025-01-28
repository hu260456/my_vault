## 三路比较运算符

```C++
// 三路比较函数
is_eq()
is_neq()
is_lt()
is_lteq()
is_gt()
is_gteq()
// partial_ordering：偏序比较
int main() {
    double a = 0.0;
    double b = -0.0;
    partial_ordering po = (a <=> b);
    cout << boolalpha << is_eq(po);
}
```

## std::format

```C++
#include <format>
int main() {
    cout << format("{:.2f}", 3.14159265);
}
```

## 判断大小端存储

```C++
cout << boolalpha << (endian::native == endian::big); // 大端存储
cout << boolalpha << (endian::native == endian::little);
```

