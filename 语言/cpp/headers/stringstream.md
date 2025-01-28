#### 字符串分割例子

```CPP
stringstream ss("string stream");
string word;
while (ss >> word)
	cout << word << endl;

// 使用 getline 的方法
stringstream ss("another way...");
string word;
while (getline(ss, word, ' '))
	cout << word << endl;
```