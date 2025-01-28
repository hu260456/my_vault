## 返回函数内部类的指针

```C++
auto func() {
    struct People {
        string name = "ivy";
        int age = 18;
    };
    return new People; // C++14前可以返回这个指针，但People的成员不会暴露
}
int main() {
    cout << func()->name;
}
```


