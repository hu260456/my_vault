#### rustup

```shell
# 查看版本
rustup --version

# 更新 rust
rustup update

# 卸载 rust
rustup self uninstall

# 添加组件
rustup component add <组件名>

# 添加 nightly 版本
rustup install nightly

# 查看编译器详情
rustup show

# 切换到 nightly 或 stable
rustup default nightly
rustup default stable
```

#### rustc

```shell
# 查看版本
rustc --version

# 编译生成二进制文件
rustc <input.rc> -o <output>

# 编译生成库文件
rustc --crate-type lib <input.rc> 
```

#### cargo

```shell
# 创建项目
cargo new <project>

# 创建一个 rust 库项目
cargo new --lib <project>

# 构建项目，生成可执行文件或库文件，用于开发环境
cargo build

# 构建项目，生成可执行文件或库文件，用于生产环境
cargo build --release

# 检测
cargo check

# 自动 build + 运行二进制可执行文件
cargo run

# 自动 build + 运行库的测试
cargo test

# 安装包
cargo install <包>

# 卸载包
cargo install <包>

# 安装项目依赖
cargo add <依赖>
cargo add <依赖>@<版本>

# 安装开发时依赖
cargo add --dev <依赖>

# 安装构建时时依赖
cargo add --build <依赖>

# 删除依赖
cargo rm <依赖>
```

#### 变量

```rust
// 使用 let 关键字声明变量
// 支持自动推导，也可以显式指定类型
let x: i32 = 5;

// rust 中的变量默认是不可变的
// 使用 mut 关键字声明可变变量
let mut y = 10;

// 打印变量
// 复杂类型需要实现 trait
prinln!("val: {}", x);
prinln!("val: {x}");

// 未使用的变量使用 _ 前缀
let _x = 1;

// const
// 常量必须在编译时知道类型与值
// 常量与静态变量建议使用大写命名
// 必须显式标注类型
fn main() {
	const X = 1;
}

// static
// 在运行时分配内存
// 可以使用 unsafe 使其可变
// 生命周期贯穿整个程序运行
static X = 1;
fn main() {
	unsafe {
		X = 2;
	}
}

// 强制类型转化
let a = b as i32;
```

#### 简单数据类型

```rust
// 有符号默认是 i32
// i8 - i128

// 无符号
// u8 - u128

// 浮点数默认是 f64
// f32 - f64

// 布尔
// true, false

// 字符
// 支持 unicode
// 使用单引号
// char

// 由平台决定
// usize, isize
```

#### 元组

```rust
fn main() {
	// 创建元组，默认不可变
	let tup = (0, "hi", 3.14);
	// 获取分量
	println!("{tup.0} {tup.1} {tup.2}");
}
```

#### 数组

```rust
fn main() {
	// 创建元组，默认不可变
	let mut arr = [1, 2, 3];
	// 创建数组，3 个 2
	let mut arr = [2; 3];
	// 获取第 index 个
	println!("arr[2]");
	// 获取长度
	println!("arr.len()");
}
```

#### String 与 &str

```rust
fn main() {

	// String: 字符串
	// 由堆分配
	// 可变
	// 有所有权
	
	// &str: 字符串切片引用
	// 由栈分配
	// 不可变
	// 无所有权
	// 转化为 String
	let str = "test".to_owned();
	let str = "test".to_string();
}
```

#### 所有权机制

```rust
fn print_str(str: String) {
	println(str);
}

fn main() {
	let num = 1;
	let num1 = num;
	// 合法，简单类型有 copy 特质
	println!(num);

	let arr = [2; 3];
	let arr1 = arr;
	// 合法，数组和元组实现了 copy 特质
	println!(arr);

	let str = String::from("hello");
	let str1 = str;
	// 不合法，str 的所有权已转移
	println!(str);

	// 如果需要拷贝
	let str = String::from("hello");
	let str1 = str.clone();
	// 合法
	println!(str);

	print_str(str);
	// 不合法，str 所有权转移到函数内，随着函数结束而销毁
	println!(str);
}
```

#### borrowing

- 分为
	- 不可变 borrowing
	- 可变 borrowing

```
```

#### vec

```rust
// 是集合类型 collection type
```

#### map

```rust
// 是集合类型 collection type
```

#### 不同进制字面量

```rust
fn main() {
	let x = 1;
	let x1 = 0xff;
	let x3 = 0o77;
	let x2 = 0b11;
	// Byte 类型
	b'A'
}
```

#### shadowing variables

> Rust 允许您隐藏一个变量，这意味着您可以声明一个与现有变量同名的新变量，从而有效地隐藏前一个变量

```rust
fn main() {
	let x = 1;
	let x = x + 1;
	let x = x * x;
}
```
#### 命名空间

```rust
let x = 5;
// 创建一个命名空间
{
    let x = 10;
    println!("inner x: {x}");
}
// 此处命名空间内的 x 将被销毁
println!("outer x: {x}");
// 在相同作用域重新声明了 x，会覆盖
let x = "hello"
```

#### for

```rust
fn main() {
	let arr = [1, 2, 3, 4, 5];
	for i in arr {
		println(i);
	}
}
```

#### 结构体

```rust
struct Person {
	name: String,
	age: i32
}
```