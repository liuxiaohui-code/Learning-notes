# 开始

1. 文件以 `.lua`结尾
2. 单行注释 `--` 多行注释 `--[[  ]]--`
3. 标识符 以一个字母 A 到 Z 或 a 到 z 或下划线 \_ 开头后加上 0 个或多个字母，下划线，数字（0 到 9）。区分大小写
4. 换行为语句分隔符

[文档](https://www.runoob.com/manual/lua53doc/contents.html)

# 变量

Lua 变量有三种类型：全局变量、局部变量、表中的域。

1. _全局变量_:
   1. 默认情况下,变量都是全局的,全局变量不需要声明，给一个变量赋值后即创建了这个全局变量，
   2. 访问一个没有初始化的全局变量也不会出错，只不过得到的结果是：nil。
2. 局部变量: 关键字 `local`,函数内部也可以声明全局变量
3. 支持多变量赋值

```lua
a=1
print(a) -- a
print(b) -- nil

-- 删除变量
a=nil

-- 局部变量
local x =1

a, b, c = 0, 1
print(a,b,c)             --> 0   1   nil
a, b = a+1, b+1, b+2     -- value of b+2 is ignored
print(a,b)               --> 1   2
a, b, c = 0
print(a,b,c)             --> 0   nil   nil
```

# 全局函数

```lua
print() -- 控制台打印
string type(变量) -- 返回变量的数据类型

ipairs(a) -- 迭代table
```

# 数据类型

lua 数据类型 8 中,nil、boolean、number、string、userdata、function、thread 和 table

## nil

`nil` 这个最简单，只有值 nil 属于该类，表示一个无效值（在条件表达式中相当于 false）。

## boolean

`boolean` 包含两个值：false 和 true。

Lua 把 false 和 nil 看作是 false，其他的都为 true，数字 0 也是 true

## number

number 表示双精度类型的实浮点数,Lua 默认只有一种 number 类型 -- double（双精度）类型

```lua
print(type(2))
print(type(2.2))
print(type(0.2))
print(type(2e+1))
print(type(0.2e-1))
print(type(7.8263692594256e-06))
```

### 运算

**算术运算符**

```lua
a=10 b=20
+	加法	A + B 输出结果 30
-	减法	A - B 输出结果 -10
*	乘法	A * B 输出结果 200
/	除法	B / A 输出结果 2
%	取余	B % A 输出结果 0
^	乘幂	A^2 输出结果 100
-	负号	-A 输出结果 -10
//	整除运算符(>=lua5.3)	5//2 输出结果 2
```

**关系运算符**

```lua
设定 A 的值为10，B 的值为 20：
操作符	描述	实例
==	等于，检测两个值是否相等，相等返回 true，否则返回 false	(A == B) 为 false。
~=	不等于，检测两个值是否相等，不相等返回 true，否则返回 false	(A ~= B) 为 true。
>	大于，如果左边的值大于右边的值，返回 true，否则返回 false	(A > B) 为 false。
<	小于，如果左边的值大于右边的值，返回 false，否则返回 true	(A < B) 为 true。
>=	大于等于，如果左边的值大于等于右边的值，返回 true，否则返回 false	(A >= B) 返回 false。
<=	小于等于， 如果左边的值小于等于右边的值，返回 true，否则返回 false	(A <= B) 返回 true。
```

**逻辑运算符**

```lua
逻辑运算符
下表列出了 Lua 语言中的常用逻辑运算符，设定 A 的值为 true，B 的值为 false：

操作符	描述	实例
and	逻辑与操作符。 若 A 为 false，则返回 A，否则返回 B。	(A and B) 为 false。
or	逻辑或操作符。 若 A 为 true，则返回 A，否则返回 B。	(A or B) 为 true。
not	逻辑非操作符。与逻辑运算结果相反，如果条件为 true，逻辑非为 false。	not(A and B) 为 true。
```

## string

1. string 字符串由一对双引号或单引号来表示,也可以用 2 个方括号 `[[]]` 来表示"一块"字符串。
2. 字符串连接 `..`
3. 对一个数字字符串上进行算术操作时，Lua 会尝试将这个数字字符串转成一个数字,无法转成数字就报错
4. 取字符串长度 `#"this is string1" -- 16`

```lua
string1 = "this is string1"
string2 = 'this is string2'
html = [[
<html>
<head></head>
<body>
    <a href="http://www.runoob.com/">菜鸟教程</a>
</body>
</html>
]]
print(html)

-- 字符串连接 ..
> print("a" .. 'b')
ab

-- 在对一个数字字符串上进行算术操作时，Lua 会尝试将这个数字字符串转成一个数字:
> print("2" + "6")
8.0

> print("error" + 1)
stdin:1: attempt to perform arithmetic on a string value
stack traceback:
        stdin:1: in main chunk
        [C]: in ?
>
```

## function

function 由 C 或 Lua 编写的函数,被看作是"第一类值（First-Class Value）"

1. 函数使用类似 js,可以做变量,做参数,匿名传递
2. 返回值支持多返回
3. 支持可变参数,可以通过 `select("#",...)` 来获取可变参数的数量:
4.

```lua
function factorial1(n)
    return xxx
end

范围限制local function function_name( argument1, argument2, argument3..., argumentn)
    function_body
    return result_params_comma_separated
end

function add(...)
local s = 0
  for i, v in ipairs{...} do   --> {...} 表示一个由所有变长参数构成的数组
    s = s + v
  end
  return s
end
print(add(3,4,5,6,7))  --->25
```

## userdata

userdata 表示任意存储在变量中的 C 数据结构,userdata 是一种用户自定义数据，用于表示一种由应用程序或 C/C++ 语言库所创建的类型，可以将任意 C/C++ 的任意数据类型的数据（通常是 struct 和 指针）存储到 Lua 变量中调用。

## thread

thread 表示执行的独立线路，用于执行协同程序,在 Lua 里，最主要的线程是协同程序（coroutine）。它跟线程（thread）差不多，拥有自己独立的栈、局部变量和指令指针，可以跟其他协同程序共享全局变量和其他大部分东西。

线程跟协程的区别：线程可以同时多个运行，而协程任意时刻只能运行一个，并且处于运行状态的协程只有被挂起（suspend）时才会暂停。

## table

Lua 中的表（table）其实是一个"关联数组"（associative arrays），数组的索引可以是数字、字符串或表类型。在 Lua 里，table 的创建是通过"构造表达式"来完成，最简单构造表达式是{}，用来创建一个空表。

1. 创建 `local tbl1 = {}`
2. table 的元素类型可以不一致,索引可以是数字或者是字符串,值为任意;
3. 默认索引为数字,从 1 开始
4. table 不会固定长度大小，有新数据添加时 table 长度会自动增长，没初始的 table 都是 nil。
5. 访问通过 `tbl[key]`或 `tbl.key`

```lua
-- 创建一个空表
local tbl1 = {}
-- 创建一个初始化表
local tbl2 = {"apple", "pear", "orange", "grape"}

a = {}
a["key"] = "value"
key = 10
a[key] = 22
a[key] = a[key] + 11
for k, v in pairs(a) do
    print(k .. " : " .. v)
end
--[[
    key : value
    10 : 33
]]--

t[i]
t.i   -- 当索引为字符串类型时的一种简化写法
```

# 流程控制

## 分支

```lua
if( 布尔表达式 1)
then
   --[ 布尔表达式 1 为 true 时执行该语句块 --]
   if(布尔表达式 2)
   then
      --[ 布尔表达式 2 为 true 时执行该语句块 --]
   end
end
```

## 循环

### while

```lua
while(condition)
do
   statements
end
```

### for

```lua
-- var 从 exp1 变化到 exp2，每次变化以 exp3 为步长递增 var，并执行一次 "执行体"。exp3 是可选的，如果不指定，默认为1。
for var=exp1,exp2,exp3 do
    <执行体>
end

-- 泛型for循环
a = {"one", "two", "three"}
-- i是数组索引值，v是对应索引的数组元素值。ipairs是Lua提供的一个迭代器函数，用来迭代数组。
for i, v in ipairs(a) do
    print(i, v)
end
```

### repeat...until

我们注意到循环条件判断语句（condition）在循环体末尾部分，所以在条件进行判断前循环体都会执行一次。

如果条件判断语句（condition）为 false，循环会重新开始执行，直到条件判断语句（condition）为 true 才会停止执行。

```lua
repeat
   statements
until( condition )
```

### 循环控制

```lua
break -- 退出当前循环
goto -- 允许将控制流程无条件地转到被标记的语句处

local a = 1
::label:: print("--- goto label ---")

a = a+1
if a < 3 then
    goto label   -- a 小于 3 的时候跳转到标签 label
end

```
