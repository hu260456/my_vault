#### ts 配置项

strictNullChecks: 开启后不能将 null 分配给其他类型

#### js 类型

```typescript
let v: string = 'abc'
let v: number = 10
let v: boolean = true
let v: null = null
let v: undefined = undefined
let v: symbol = Symbol()
// 获取类型
typeof v
```

#### ts 类型

```typescript
// 字面量类型
// 不是 变量string
// ts 中 字符串、数字、对象、枚举成员 的常量也可以作为一种类型
const v = 'abc'

// ts 中的数组默认只允许相同的类型，与 js 不同
let v: number[] = [1, 2, 3]
// 写法 2
let v: Array<number> = [1, 2, 3]
// 允许多种类型
let v: (number | string)[] = [1, '2']

// 联合
// 允许 string 和 number
let v: string | number = 10
// 允许 1 | 2 | 3
let v: 1 | 2 | 3 = 1

// 元组
let v: [number, string, number] = [1, 'a', 2]
// 元组，第三分量可选
let v: [number, string, number?] = [1, 'a']

// 枚举
enum v {
	RED=10,
	GREEN,
	BLUE=20
}
console.log(v.RED)
console.log(v[1])
// 字符串枚举，没有自增行为，必须赋值
enum v {
	UP='UP',
	DOWN='DOWN',
	LEFT='LEFT',
	RIGHT='RIGHT'
}

// 自定义类型
type v = string | number
// 类型别名
type v = number
// 定义函数模式
type v = (arg: number) => void

// void
function v(): void {...}

// keyof
// 获取一个对象所有字段的联合类型
let v = {
	name: 'hu',
	age: 18
}

// 'hu' | 18
keyof v

// any
let v: any = 1
// 不报告错误
v = '6'
```

#### 索引签名

> 当无法确定对象中有哪些、有多少属性时，就用到索引签名类型

```typescript
interface AnyOBJ {
	// key 只是一个占位符
	// [key: string] 约束了 key 的类型
	// AnyOBJ 允许有多个此类属性
	[key: string]: number
}

let obj: AnyOBJ = {
	a = 1,
	b = 2
}

// 实际上，ts 数组就用到了索引签名
interface MyArray<T> {
	[n: number]: T
}

let arr: MyArray<number> = [1, 2, 3]
```

#### 映射类型

```typescript
type K = 'x' | 'y' | 'z'
type T = { x: number; y: number; z: number }

// 使用映射类型编写如下
// 映射类型是基于索引类型的
type K = 'x' | 'y' | 'z'
type T = { [key in K]: number }

// 索引查询类型
type OBJ = { a: number; b: string }
type A = OBJ['a']  // number
type A_B = OBJ[keyof OBJ]  // number | string
```

#### 函数

```typescript
// x 默认为 10
function Fn(x = 10) {}
// x 是可选的，可选参数需要在最后
function Fn(x?) {}
// 返回 number 类型
function Fn():number {}
// 其他多余的参数是 number[] 类型
function Fn(x, ...rest: number[]) {}
```

#### 函数重载

> javascript 中不支持函数重载

```typescript
function Fn(x: string) { console.log(x) }
function Fn(x: number) { console.log(x) }
Fn('1')
Fn(1)
```

#### 接口

```typescript
// 定义接口
interface Obj {
	name: string,
	age: number
}

// 对象实现接口
const obj: Obj = {
	name: 'a',
	age: 10
}

// 类实现接口
// 一个类可以实现多个接口，用逗号分隔
class Person implements Obj {
	name: string
	age: number
	...
}

// 接口继承
interface Parent {
	var1: string
	var2: number
}
interface Child extends Parent {
	// 不允许定义类型不兼容的同名属性
	var3: string
}
```

#### 交叉类

```typescript
interface A {
	name: string,
	age: number
}
interface B {
	age: string
	work: string,
	department: number
}

// Employee 拥有 A 和 B 的全部属性
// age: number | string
type Employee = A & B
```

#### 类

```typescript
// 定义类
class Person {

	// 属性
	name: string  // 默认 public
	protected age: number  // 受保护，只能在类内或子类中访问
	private balance?: number  // 可选的属性
	static readonly is_to_die = true  // 公共属性 且 只读属性
	gender = true  // 默认是 true
	xxx: string  // 严格模式下，未初始化的属性会报错

	// 构造方法
	constructor(name: string, age: number, gender: boolean) {
		this.name = name
		this.age = age
		this.gender = gender
	}

	// getter, setter
	get balance(): number {
		return this.balance
	}
	set balance(new_balance: number) {
		this.balance = new_balance
	}

	// 成员函数
	doSomeThing():void {...}
}

// 实例化
const zhangsan = new Person('zhangsan', 20, true)

// 继承
// 不支持多继承
class Shabi extends Person{
	constructor(name: string, age: number, gender: boolean) {
		super(name, age, gender)
	}
}

// 抽象类
abstract class Animal {
	abstract name: string  // 不初始化也不报错，因为本来就是抽象的，需要子类去实现
	abstract make_sound(): void
	// 抽象类实现的属性子类可以不实现
	move():void {
		console.log('移动')
	}
}
// 继承抽象类
class cat extends Animal {
	name: string = '小猫咪'
	make_sound() {
		console.log('moew, moew, moew...')
	}
}

```

#### 泛型

```typescript
// 定义函数
function Fn<T>(a: T, b: T): T[] {
	return [a, b]
}
// 使用函数
Fn<number>(1, 2)
Fn<string>('1', '2')
// 自动推断
Fn('1', '2')

// 泛型接口
interface My_Interface<T> {
	name: string
	age: number,
	do: () => T
}

// 泛型类
class My_class<T>{
	val: T
	constructor(val: T) {
		this.val = val
	}
	Fn() { console.log(T) }
}


// 内置泛型类

// PartialT 获得 T 的结构，但是所有属性都是可选的
type PartialT = Partial<T>
// ReadonlyT 获得 T 的结构，但是所有属性都是只读的
type ReadonlyT = Readonly<T>
// PickT 的所有属性必须被 T 的所有属性覆盖
type PickT = Pick<T, 'title' | 'date'>
// 用来定义键值对对象类型
type RecordX = Record<'a'|'b'|'c', number>
let obj: RecordX = {
	a: 1,
	b: 2,
	c: 3
}

// 内置泛型类是基于映射类型实现的
type Partial<T> = {
	[K in keyof T]?: T[K]
}
```

#### 泛型约束

```typescript
function Fn<T>(val: T): void {
	// 会报错
	// 不是所有类型都有 length 属性
	console.log(val.length)
}

// 方案1
// 指定更加具体的类型
function Fn<T>(val: T[]): void {
	console.log(val.length)
}

// 方案2
// 添加约束
interface With_length { length: number }
function Fn<T extends With_length>(val: T): void {
	console.log(val.length)
}
```

#### 类型断言

```typescript
let arr = [1, 2, 3]
// 使用 as type 来断言类型
const result = arr.find(item => item > 2) as number
// 如果不添加类型断言会报错
// 因为 result 可能是 undefined
console.log(result * 5)
```

#### 类型兼容性

- 类型系统
	- 结构化类型系统（鸭子类型）（ts 采用）
	- 表明类型系统

- 类兼容性

```typescript
class Point {
	x: number
	y: number
	...
}
class Point3D {
	x: number
	y: number
	z: number
	...
}

// Point3D 的成员覆盖 Point，则 Point 兼容 Point3D，允许赋值
const p:Point = new Point3D(1, 1)
```

- 接口兼容性与类兼容性类似

- 函数兼容性

```typescript
type F1 = (a:number) => void
type F2 = (a:number, b: number) => void

// 1. 同位置参数类型相同或兼容
// 2. 返回类型相同或兼容
// 2. f1 参数不大于 f2
// 结果：允许赋值
let f1: F1
let f2: F2 = f1
```

#### 类型声明文件

> 用来为已存在的 JS 库提供类型信息，这样在 TS 环境中使用这些库时，就像用 TS一样，都会有代码提示、类型保护等机制

- .ts file
	- 包含类型信息与代码
- .d.ts file
	- 只包含类型信息
	- 用于为 js 文件提供类型信息
	- 分类
		- 内置类型声明文件
			- ts 为 js 运行时的内置 api 都提供了类型声明
			- ts 为 DOM、BOM API 也提供了相应的类型声明
		- 第三方库的类型声明文件
			- 库自带的类型声明文件
				- 导入该库 ts 就会自动加载此库的类型声明文件
			- 由 DefinitelyTyped 仓库提供
				- 可以在 ts 官网搜索需要的 type
				- 这些文件以 @types/* 命名和存储

##### 自定义类型声明文件

> ==note==: 类型声明文件的编写与模块化方式有关（CommonJS, ESModule），示例采用 ESModule

- 为 ts 项目创建共享类型

```typescript
// index.d.ts
type Point = { x: number; y:number }
export { Point }

// index.ts
import { Point } from 'index'
```

- 为 js 提供类型信息

```typescript
// 创建与 js 文件同名的 .d.ts 文件
// 导入 js 文件时，TS 会自动加载此文件以提供类型声明

// 使用 declare 关键字为其他 js 已经存在的变量提供类型声明
declare let var1: number
// 对于 type, interface 等明确的 ts 类型无需 declares
// 对于 let, function 等双重含义，应该使用 declare
type T =  number | string
// 根据模块化方式的不同编写不同的导出代码
export {var1, T}
```

#### tsconfig.json

```json
{
	// 配置相关
	"compilerOptions": {
		...,
		// 编译目标，--target ES6
		"target": "ES6",
		// 定义要包含在编译中的库
		"lib": ["dom"],
		// 允许 tsc 编译 js
		"allowJs": true,
		// 跳过对类型声明文件的类型检查
		"skipLibCheck": false,
		// 屏蔽 ESModule 和 CommonJS 之间的差异
		"esModuleInterop": true,
		// 允许通过 impoer x from 'y' 即使 'y' 没有显式的默认导出
		"allowSyntheticDefaultImports": false,
		// 开启严格模式
		"strict": true,
		// 导入文件时区分大小写
		"forceConsistentCasingInFileNames": true,
		// 为 switch 语句启用错误报告
		"noFallthroughCasesInSwitch": true,
		// 生成代码的模块化标准，esnext：最新的 ESModule
		"module": "ESNext",
		// 模块解析（查找）策略
		"moduleResolution": "node",
		// 允许导入扩展名为 .json 的模块
		"resolveJsonModule": true,
		// 是否将没有 import/export 的文件视为全局而非模块化脚本的文件
		"isolatedModules": true,
		// 编译时不生成任何文件（只进行类型检查）
		"noEmit": true
	},
	// 指定 ts 处理的目录
	"include": ["src"]
}
```