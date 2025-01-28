```CPP
int main() {
    tuple<int, int, string> tp0{6, 66, "ivy"};
    tuple<int, int, string> tp1 = make_tuple(6, 66, "ivy");
    
	string name = get<2>(tp0);
    
    int a, b;
    tie(a, b, name) = tp0;
    
    // cpp17 支持
    auto [a,b,name] = tp0;
}
```