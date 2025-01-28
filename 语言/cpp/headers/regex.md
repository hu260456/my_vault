#### 创建一个 regex

```CPP
// std::regex 是 std::basic_regex<T> 对 char 的一个特化
// 还有一个针对 wchar_t 的特化为std::wregex
#include <regex>
int main() {
	regex re("<正则表达式>");
	// 创建一个忽略大小写的规则
	regex re("<正则表达式>", regex::icase);
	// std::regex 默认使用的是 ECMAScript 文法，指定 basic 文法
	regex re("<正则表达式>", regex::basic)
}
```

#### regex_match

```CPP
// 用于全文匹配
bool flag = regex_match("6", regex("[0-9]"));

// 如果想保存匹配结果
// cmatch 是 match_result<T> 对 C 字符的一个特化版本
// 若是 string，使用针对 string 的特化版本 smatch
// 同时还支持相应的宽字符版本 wcmatch 和 wsmatch
cmatch m;
bool ret = std::regex_match("<xml>value</xml>", m, std::regex("<(.*)>(.*)</(\\1)>"));
if (ret) {
	std::cout << m.str() << std::endl;
	std::cout << m.length() << std::endl;
	std::cout << m.position() << std::endl;
}

// 遍历匹配到的结果
for (int i = 0; i < m.size(); ++i) {
	std::cout << m[i].str() << " " << m.str(i) << std::endl;
}
// or
for (auto pos = m.begin(); pos != m.end(); ++pos) {
	std::cout << *pos << std::endl;
}
```

#### regex_search

```CPP
regex reg("<(.*)>(.*)</(\\1)>");
cmatch m;
auto ret = regex_search("123<xml>value</xml>456", m, reg);
if (ret)
	for (auto &elem : m)
		cout << elem << endl;
cout << "prefix:" << m.prefix() << endl; // 123
cout << "suffix:" << m.suffix() << endl; // 456
```