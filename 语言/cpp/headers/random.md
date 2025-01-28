#### shuffle & random_shuffle

```CPP
int a[]{1, 2, 3, 4, 5};

// 使用伪随机数打乱 a 序列
random_device rd;
mt19937 m(rd());
shuffle(a, a + 5, m);

// 打乱 a 序列，在 c++ 17 中弃用
random_shuffle(a, a + 5);
```
