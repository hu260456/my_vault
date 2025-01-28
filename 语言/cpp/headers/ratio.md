#### 定义

```CPP
using r = ratio<2, -4>
// 分子（-1），自动约分，符号优先出现在分子上
intmax_t num = r::num;
// 分母（2），自动约分
intmax_t den = r::den;
```


#### 四则运算

```CPP
using r1 = ratio<1, 2>;
using r2 = ratio<3, 4>;

// +
using r3 = ratio_add<r1, r2>;
// -
using r4 = ratio_subtract<r1, r2>;
// *
using r5 = ratio_multiply<r1, r2>;
// /
using r6 = ratio_divide<r1, r2>;
```

#### 分数之间的比较

```CPP
using r1 = ratio<1, 2>;
using r2 = ratio<3, 4>;

// ==
using r3 = ratio_equal<r1, r2>;
// !=
using r4 = ratio_not_equal<r1, r2>;
// <
using r5 = ratio_less<r1, r2>;
// <=
using r6 = ratio_less_equal<r1, r2>;
// >
using r7 = ratio_greater<r1, r2>;
// >=
using r8 = ratio_greater_equal<r1, r2>;
```