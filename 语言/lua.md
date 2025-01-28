#### 定义变量

```Lua
-- a 默认是全局变量，可以在任意文件中使用
a = 1

-- b 是文件全局变量，在文件中是全局的
local b = 1

-- 在 Lua 中未声明过的变量都是 nil

-- 同时赋值多个变量
c, d = 1, 2
```

#### 基本类型与操作

- number
	- 不区分 int、float 等
	- tonumber('10'): 将 '10' 转化为 10
- string
	- a = 'a str'
	- a = "a str"
	- 多行文本：使用 `[[` 开头，`]]` 结尾
	- `a..b` 表示 a 与 b 连接后的结果
	- tostring(10): 将 10 转化为 '10'
	- `#a` 查看 a 的 长度
- 布尔类型
	- true
	- false 
	- lua 中不等于是 `~=`
	- nil == false
	- 0 == true
	- 与 nil 做布尔运算结果还是 nil
#### 函数

```Lua
function 函数名 (a, b)
	...
	return a, b
end
```

#### table

```Lua
-- Lua 中下标是从 1 开始的
-- 数组中元素的类型可以不一致
a = {1, "ac", {}, function() end}

-- 获取数组大小
length = #a

-- 在最后插入
table.insert(a, "new data")
-- 在第 1 个元素后插入
table.insert(a, 2, "new data")

-- 删除 a 中第二个元素，返回删除的元素
local deleted = table.remove(a, 2)

-- 用字符串作为下标的 table
a = {
	a = 1,
	b = "1234",
	c = function()
	end
}
-- 使用 a.a 或 a["a"] 获取 1
```

#### _G

```Lua
-- Lua 中所有的全局变量都在 _G table 中
```

#### 分支判断

```Lua
if 条件 then
	...
elseif 条件
	...
else
	...
end
```

#### 循环语句

```lua
--[[
for 循环
i 从 1 到 10，步长为 2
for 循环中不允许修改 i
--]]
for i = 1, 10, 2 do
	if <condition> then break end
	...
end

-- while 循坏
local a = 10
while a > 1 do
	-- Lua 没有自减
	n = n - 1
end
```
