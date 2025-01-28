#### strtok

- 用于字符串分割

```CPP
char str[] = "a c-style way to split string";
char *a = strtok(str, " ");
while (a) {
	cout << a << endl;
	a = strtok(nullptr, " ");
}
```
