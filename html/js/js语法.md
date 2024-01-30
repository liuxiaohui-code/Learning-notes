# 语法与数据类型

## 语法规则

1. 区分大小写的，
2. 使用 Unicode 字符集。
3. 语句以`;`结尾,单独一行可省略
4. 单行注释 `//` 多行注释`/* */`

## 变量

名词解释:

-   **声明**,使用关键字定义一个数据，不赋值,默认`undefined`
-   **变量**,可以多次赋值=；以字母、下划线（\_）或者美元符号（$）开头；后续的字符也可以是数字（0-9
-   **常量**:只能也必须在声明时赋值=,且不能再次=赋值,但是对象和数组内部可以改变
-   **全局变量**: 不在函数体内声明，是全局对象的属性; 在浏览器全局变量为`window`,可缺省;
-   **局部变量**,在{}内部声明
-   **块作用域**: {}
-   **变量提升**:先使用变量稍后再声明变量而不会引发异常；
-   **函数提升**: 对于函数来说，只有函数声明会被提升到顶部，而函数表达式（var baz = function()）不会被提升。

1. `var`,局部变量,全局变量,变量提升
2. `let`,局部变量,全局变量,变量提升,块作用域
3. `const`,常量

## 数据类型

1. `boolean`: 布尔,false|true;特殊 false:`0,-0,null,false,NaN,undefined,""`
2. `number` 数字 ,特殊的数字 `NaN,Infinity`
3. `string` 字符串
4. `object` 对象，数组也是对象, 特殊的对象:`globalThis`
5. `undefined` 未定义
6. `function` 函数
7. `symbol` `Symbol()`函数会返回**symbol**类型的值

### 关键字

1. `typeof x` 一元操作符，以字符串返回 x 的数据类型
2. `NaN` 表示不是一个数字,`isNaN(x)`判断 x 是否不是数字
3. `Infinity` 数值，表示无穷大，
4. `undefined`
5. `globalThis` 提供了一个标准的方式来获取不同环境下的全局 `this` 对象

### 类型转换

​ JavaScript 是一种动态类型语言(dynamically typed language)。这意味着你在声明变量时可以不必指定数据类型，而数据类型会在代码执行时会根据需要自动转换。

```js
// 数字 -> 字符串
x = "The answer is " + 42 // "The answer is 42"
y = 42 + " is the answer" // "42 is the answer"
"37" - 7 // 30
"37" + 7 // "377"
// 字符串 -> 数字
Number.parseInt()
Number.parseFloat()
"1.1" +
	"1.1"(
		// "1.11.1"
		+"1.1"
	) +
	+"1.1" // 2.2
```

### 数字进制转换

```js
// 0x表示十六进制 0X
var a = 0xa //，但是js会强制转换为十进制来运算,0xa == 10
// 0开头表示八进制,es6--0o 0O
var b = 010 //，同样强制转换为十进制来运算 010 = 8
// 0b 二进制 0B
a * b //= 80

Number.parseInt("010", 8) //8
```

## 运算符

```
赋值 	= 	+= 	-= 	/= 	*= 	%=
算数	+ 	- 	/ 	* 	% 	++ 	--
字符串	+	+=  （级联运算符）
比较	==	===	!=	!==	>	<	>=	<=	?:
逻辑	&&	||	!
类型	typeof	instanceof #返回变量的类型;    返回 true，如果对象是对象类型的实例。
位	&	|	~非	^异或	<<	>>	>>>无符号右移
```

### 优先级

| Operator type          | Individual operators                  |
| :--------------------- | :------------------------------------ | --- | --- |
| member                 | `. []`                                |
| call / create instance | `() new`                              |
| negation/increment     | `! ~ - + ++ -- typeof void delete`    |
| multiply/divide        | `* / %`                               |
| addition/subtraction   | `+ -`                                 |
| bitwise shift          | `<< >> >>>`                           |
| relational             | `< <= > >= in instanceof`             |
| equality               | `== != === !==`                       |
| bitwise-and            | `&`                                   |
| bitwise-xor            | `^`                                   |
| bitwise-or             | `                                     | `   |
| logical-and            | `&&`                                  |
| logical-or             | `                                     |     | `   |
| conditional            | `?:`                                  |
| assignment             | `= += -= \*= /= %= <<= >>= >>>= &= ^= | =`  |
| comma                  | `,`                                   |

# 控制流与错误处理

## 语句块

最基本的语句是用于组合语句的语句块。该块由一对大括号界定：

```js
{
   statement_1;
   statement_2;
   statement_3;
   .
   .
   .
   statement_n;
}
```

## 条件|分支

-   使用 `if` 来规定要执行的代码块，如果指定条件为 true
-   使用 `else` 来规定要执行的代码块，如果相同的条件为 false
-   使用 `else if` 来规定要测试的新条件，如果第一个条件为 false
-   使用 `switch` 来规定多个被执行的备选代码块

### if

```js
if (条件) {
    如果条件为 true 时执行的代码
} else if (条件) {
    如果条件为 true 时执行的代码
} else {
    条件为 false 时执行的代码块
}
```

### switch

```js
switch (
	表达式 // Switch case 使用严格比较（===）。
) {
	case n:
		代码块
		break
	case n:
		代码块
		break
	default:
		默认代码块
}
/*
计算一次 switch 表达式
把表达式的值与每个 case 的值进行对比
如果存在匹配，则执行关联代码
*/
```

### 三元运算符？

```js
( condition ) ? run this code : run this code instead
```

## 错误处理

### try-catch-finally

**try 语句使您能够测试代码块中的错误。**

**catch 语句允许您处理错误。**

**finally 使您能够执行代码，在 try 和 catch 之后，无论结果如何。**

```js
try {
	// 供测试的代码块
} catch (err) {
	// 处理错误的代码块
} finally {
	// 无论结果如何都执行的代码块
}
```

### throw

​ throw 语句允许您创建自定义错误。从技术上讲您能够*抛出异常（抛出错误）*。异常可以是 JavaScript 字符串、数字、布尔或对象：

```js
throw "Too big" // 抛出文本
throw 500 //抛出数字
```

如果把 throw 与 try 和 catch 一同使用，就可以控制程序流并生成自定义错误消息。

```js
function myFunction() {
	var message, x
	message = document.getElementById("message")
	message.innerHTML = ""
	x = document.getElementById("demo").value
	try {
		if (x == "") throw "空的"
		if (isNaN(x)) throw "不是数字"
		x = Number(x)
		if (x < 5) throw "太小"
		if (x > 10) throw "太大"
	} catch (err) {
		message.innerHTML = "输入是 " + err
	}
}
```

### Error 对象

JavaScript 拥有当错误发生时提供错误信息的内置 error 对象。error 对象提供两个有用的属性：name 和 message。

属性

```js
name //设置或返回错误名
message //设置或返回错误消息（一条字符串）

EvalError //已在 eval() 函数中发生的错误,更新版本的 JavaScript 不会抛出任何 EvalError。请使用 SyntaxError 代替。
RangeError //已发生超出数字范围的错误
ReferenceError //已发生非法引用
SyntaxError //已发生语法错误
TypeError //已发生类型错误
URIError //在 encodeURI() 中已发生的错误
```

### debugger

设置断点

```js
debugger
```

## 循环与迭代

-   for - 多次遍历代码块
-   for/in for/of - 遍历对象属性
-   while - 当指定条件为 true 时循环一段代码块
-   do/while - 当指定条件为 true 时循环一段代码块

### for

```js
for (初始化语句 1; 判断语句 2; 更新语句 3) {
     要执行的代码块
}
```

### for-in 对象

语句循环一个指定的变量来循环一个对象所有可枚举的属性。

```js
var person = { fname: "Bill", lname: "Gates", age: 62 }

var text = ""
var x
for (x in person) {
	text += person[x]
} // Bill Gates 62

// 如果索引顺序很重要，请不要在数组上使用 for in。
// 当顺序很重要时，最好使用 for 循环、for of 循环或 Array.forEach()。
```

### for-of 数组

遍历数组

```js
for (variable of iterable) {
	// code block to be executed
}

// variable - 对于每次迭代，下一个属性的值都会分配给变量。变量可以用 const、let 或 var 声明。
// iterable - 具有可迭代属性的对象。
```

### while

```js
while (条件) {
	要执行的代码块
}
```

### do while

```js
do {
	要执行的代码块
} while (条件)
```

### 循环控制

```js
break [label];//语句“跳出”循环。
continue [label];//语句“跳过”循环中的一个迭代。
```

### label 语句

一个 `label` 提供了一个让你在程序中其他位置引用它的标识符。例如，你可以用 label 标识一个循环， 然后使用 `break` 或者 `continue` 来指出程序是否该停止循环还是继续循环。

label 语句的语法看起来像这样：

```js
label: statement

...

break label

continue label
```

`label` 的值可以是任何的非保留字的 JavaScript 标识符， `statement` 可以是任意你想要标识的语句（块）。

# 函数

## 定义

```js
function func(参数表){/*return*/}

//使用
func(参数表);

//匿名函数
function() {alert('hello')}

//函数表达式,不会函数提升
var x = function() {alert('hello')}

typeof x ;// function
```

### 属性

| 属性名   | 说明               |
| -------- | ------------------ |
| `length` | 指明函数的形参个数 |
| `name`   | 返回函数实例的名称 |

### 方法

| prototype.方法 | 说明                                                                           |
| -------------- | ------------------------------------------------------------------------------ |
| `apply()`      | 调用一个具有给定`this`值的函数，以及以一个数组（或类数组对象）的形式提供的参数 |
| `bind()`       | 创建一个新的函数,返回一个原函数的拷贝，并拥有指定的 **`this`** 值和初始参数    |
| `call()`       | 使用一个指定的 `this` 值和单独给出的一个或多个参数来调用一个函数。             |
| `toString()`   | 返回一个表示当前函数源代码的字符串                                             |

## 递归

调用自身的函数我们称之为*递归函数*。在某种意义上说，递归近似于循环。两者都重复执行相同的代码，并且两者都需要一个终止条件（避免无限循环或者无限递归）。

## 闭包

你可以在一个函数里面嵌套另外一个函数。嵌套（内部）函数对其容器（外部）函数是私有的。它自身也形成了一个闭包。一个闭包是一个可以自己拥有独立的环境与变量的表达式（通常是函数）。

-   内部函数只可以在外部函数中访问。
-   内部函数形成了一个闭包：它可以访问外部函数的参数和变量，但是外部函数却不能使用它的参数和变量。

```js
var add = (function () {
	var counter = 0
	return function () {
		return (counter += 1)
	}
})()
add()
add()
add()
// 计数器目前是 3
```

解释：

​ 变量 add 的赋值是自调用函数的返回值。这个自调用函数只运行一次。它设置计数器为零（0），并返回函数表达式。这样 add 成为了函数。最“精彩的”部分是它能够访问父作用域中的计数器。

​ 这被称为 JavaScript **闭包**。它使函数拥有“**私有**”变量成为可能。

​ 计数器被这个匿名函数的作用域保护，并且只能使用 add 函数来修改。闭包指的是有权访问父作用域的函数，即使在父函数关闭之后。

## 参数

### arguments

只能在函数内部使用,函数的实际参数会被保存在一个类似数组的 arguments 对象中。

```js
function(){
  arguments[i]
}

//其中i是参数的序数编号（译注：数组索引），以0开始。所以第一个传来的参数会是arguments[0]。参数的数量由arguments.length表示。
```

你可以用`arguments.length`来获得实际传递给函数的参数的数量，然后用`arguments`对象来取得每个参数。

**备注：**`arguments`变量只是 ”类数组对象“，并不是一个数组。称其为类数组对象是说它有一个索引编号和`length`属性。尽管如此，它并不拥有全部的 Array 对象的操作方法。

### 默认参数 es6

在 JavaScript 中，函数参数的默认值是`undefined`。

```js
// b默认为1
function multiply(a, b) {
	b = typeof b !== "undefined" ? b : 1

	return a * b
}

multiply(5) // 5
```

使用默认参数，在函数体的检查就不再需要了。

```js
function multiply(a, b = 1) {
	return a * b
}

multiply(5) // 5
```

### 剩余参数 es6

允许将不确定数量的参数表示为数组。

```js
function f(x, ...args) {
	//args是一个数组，只能作为最后一个参数出现
}
```

## 箭头函数 es6

相比函数表达式具有较短的语法并以词法的方式绑定 `this`。箭头函数总是匿名的。有两个因素会影响引入箭头函数：更简洁的函数和 `this`。

**箭头函数表达式**的语法比函数表达式更简洁，并且没有自己的`this`，`arguments`，`super`或`new.target`。

```js
(args)=>{
  函数体
}，
简写
1. 单参数不用写()
2. 函数体只有return语句，不用写{}和return
```

## this

在箭头函数出现之前，每一个新函数都重新定义了自己的 [this](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this) 值（在构造函数中是一个新的对象；在严格模式下是未定义的；在作为“对象方法”调用的函数中指向这个对象；等等）。

## 预定义函数

https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects

```js
 eval(string)                    //对一串字符串形式的 JavaScript 代码字符求值
 isFinite(x):boolean             //判断传入的值是否是有限的数值
 isNaN(x)                        //判断一个值是否是 NaN
 parseFloat(string, radix):number            //解析字符串参数，并返回一个浮点数
 parseInt(string, radix):number              //解析字符串参数，并返回指定的基数（基础数学中的数制）的整数
 decodeURI(encodedURI)              //对先前经过[encodeURI]函数或者其他类似方法编码过的字符串进行解码
 decodeURIComponent(encodedURI)     //方法对先前经过[encodeURIComponent]函数或者其他类似方法编码过的字符串进行解码
 encodeURI(url):string           //*无法*产生能适用于 HTTP GET 或 POST 请求的 URI, 因为 "&", "+", 和 "=" 不会被编码
 encodeURIComponent(str):string  //方法通过用以一个，两个，三个或四个转义序列表示字符的 UTF-8 编码替换统一资源标识符（URI）的每个字符来进行编码（每个字符对应四个转义序列，这四个序列组了两个”替代“字符）。
```

# 对象

​ 一个对象由许多的成员组成，每一个成员都拥有一个名字，和一个值。每一个名字/值（name/value）对被逗号分隔开，并且名字和值之间由冒号（:）分隔，语法规则如下所示：

```js
// 创建对象
// 1. 通过{}创建
let object = {
	name: "name",
	age: 1,
	//...
	eat: function () {},
	drink: function () {},
}
// 2. 通过构造器创建
// 3. 通过Object.create()
// 4. 通过 new Object() 创建一个空对象

// 访问属性和方法
object.name
object["age"]
object.eat()
object.drink()
```

## 构造函数

```js
function Person(first, last, age, eye) {
	//用大写首字母对构造器函数命名是个好习惯。
	this.firstName = first
	this.lastName = last
	this.age = age
	this.eyeColor = eye
}

var myFather = new Person("Bill", "Gates", 62, "blue")
var myMother = new Person("Steve", "Jobs", 56, "green")
```

## this

​ 关键字"this"指向了当前代码运行时的对象，它保证了当代码的上下文(context)改变时变量的值的正确性

​ 它拥有不同的值，具体取决于它的使用位置：

-   在方法中，this 指的是所有者对象。
-   单独的情况下，this 指的是全局对象。[object Window]
-   在函数中，this 指的是全局对象。[object Window]
-   在函数中，严格模式下，this 是 undefined。
-   在事件中，this 指的是接收事件的元素。

## set/get

```js
var person = {
	firstName: "Bill",
	lastName: "Gates",
	language: "",
	set lang(lang) {
		this.language = lang
		this.language = lang.toUpperCase()
	},
	get fullName() {
		return this.firstName + " " + this.lastName
	},
}

// 使用 setter 来设置对象属性：
person.lang = "en"

// 显示来自对象的数据：
document.getElementById("demo").innerHTML = person.language //en
// 使用 getter 来显示来自对象的数据：
document.getElementById("demo").innerHTML = person.fullName //Bill Gates
```

使用 getter 和 setter 时，JavaScript 可以确保更好的数据质量。

-   它提供了更简洁的语法
-   它允许属性和方法的语法相同
-   它可以确保更好的数据质量
-   有利于后台工作

## 对象原型

​JavaScript 常被描述为一种**基于原型的语言 (prototype-based language)**——每个对象拥有一个**原型对象**，对象以其原型为模板、从原型继承方法和属性。原型对象也可能拥有原型，并从中继承方法和属性，一层一层、以此类推。这种关系常被称为**原型链 (prototype chain)**，它解释了为何一个对象会拥有定义在其他对象中的属性和方法。

​ 准确地说，这些属性和方法定义在 Object 的构造器函数(constructor functions)之上的`prototype`属性上，而非对象实例本身。

```js
let x = { a: 1 }
x.a // 1
x的其他属性为继承Object的属性
x.__proto__ // Object 的属性,但是 x.__proto__ 不等于 Object
x.__proto__ == Object.prototype // true

// 构造函数的 prototype 属性与通过构造函数创建的对象的`_proto_`属性指向同一个对象。
```

## 继承

```js
function Brick() {
	this.width = 10
	this.height = 20
}
function BlueGlassBrick() {
	Brick.call(this) //将Brick的属性继承给BlueGlassBrick

	this.opacity = 0.5
	this.color = "blue"
}
```

## delete

删除属性 delete 关键词从对象中删除属性：

```js
var person = { firstName: "Bill", lastName: "Gates", age: 62, eyeColor: "blue" }
delete person.age // 或 delete person["age"];

/*
delete 关键词会同时删除属性的值和属性本身。
删除完成后，属性在被添加回来之前是无法使用的。
delete 操作符被设计用于对象属性。它对变量或函数没有影响。
delete 操作符不应被用于预定义的 JavaScript 对象属性。这样做会使应用程序崩溃。
delete 关键词不会删除被继承的属性，但是如果您删除了某个原型属性，则将影响到所有从原型继承的对象。
*/
```

## Object 根类

### 静态方法(Object.)

```js
assign(target, ...sources):target //方法将所有可枚举的自有（Object.hasOwnProperty() 返回 true）属性从一个或多个源对象复制到目标对象，返回修改后的对象,相同属性则后者覆盖前者,且只会复制值,不能解决深拷贝问题
create(object, propertiesObject):object2 // 方法用于创建一个新对象，使用现有的对象来作为新创建对象的原型,object2相当于object的子类,且object值会影响object2;propertiesObject的自有可枚举属性会被合并
defineProperties(obj, props) //直接在一个对象上定义新的属性或修改现有属性，并返回该对象。
defineProperty(obj, prop, descriptor) //会直接在一个对象上定义一个新属性，或者修改一个对象的现有属性，并返回此对象。
entries(obj) //返回一个给定对象自身可枚举属性的键值对数组
freeze(obj) //方法可以冻结一个对象。
fromEntries(iterable) //方法把键值对列表转换为一个对象
getOwnPropertyDescriptor(obj, prop) //方法返回指定对象上一个自有属性对应的属性描述符。
getOwnPropertyDescriptors(obj) //方法用来获取一个对象的所有自身属性的描述符
getOwnPropertyNames(obj) //返回一个由指定对象的所有自身属性的属性名（包括不可枚举属性但不包括 Symbol 值作为名称的属性）组成的数组。
getOwnPropertySymbols(obj) //返回一个给定对象自身的所有 Symbol 属性的数组
getPrototypeOf(object) //返回指定对象的原型（内部[[Prototype]]属性的值）。
hasOwn(instance, prop):boolean //旨在取代 Object.hasOwnProperty()。
is(value1, value2) //方法判断两个值是否为同一个值
isExtensible(obj) //方法判断一个对象是否是可扩展的（是否可以在它上面添加新的属性）
isFrozen(obj)//判断一个对象是否被冻结
isSealed(obj) //判断一个对象是否被密封,密封对象是指那些不可 扩展 的，且所有自身属性都不可配置且因此不可删除（但不一定是不可写）的对象。
keys(obj) //会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和正常循环遍历该对象时返回的顺序一致。
preventExtensions(obj) //让一个对象变的不可扩展，也就是永远不能再添加新的属性。
seal(obj) //封闭一个对象，阻止添加新属性并将所有现有属性标记为不可配置。当前属性的值只要原来是可写的就可以改变。
setPrototypeOf(obj, prototype) //设置一个指定的对象的原型（即，内部 [[Prototype]] 属性）到另一个对象或 null。
values(obj) //方法返回一个给定对象自身的所有可枚举属性值的数组，
```

### 原型方法(Object.prototype.)可以被继承

```js
// 属性
constructor:function //属性返回 Object 的构造函数（用于创建实例对象）。注意，此属性的值是对函数本身的引用，而不是一个包含函数名称的字符串。
__proto__ //
// 原型方法
hasOwnProperty(key:string|symbol):boolean // 会返回一个布尔值，指示对象自身属性中是否具有指定的属性
isPrototypeOf(object):boolean //方法用于测试一个对象是否存在于另一个对象的原型链上。
propertyIsEnumerable(string?):boolean //方法返回一个布尔值，此方法可以确定对象中指定的属性是否可以被 for...in 循环枚举
toLocaleString() //方法返回一个该对象的字符串表示。
toString() //方法返回一个表示该对象的字符串
valueOf():object // 将 this 值转换为一个对象。
```

## class 类

​ ECMAScript 2015，也称 ES6，引入了 JavaScript 类。JavaScript 类是 JavaScript 对象的模板。

​ **请使用关键字 class 创建类。请始终添加名为 constructor() 的方法，然后添加任意数量的方法：**

1. 使用`class`声明,类首字母大写
2. 添加`constructor()`方法,方法内部使用`this`调用的属性为类的成员属性
3. 添加其他函数为成员方法,成员方法里的`this`指代这个类对象
4. 创建类对象:`let x = new Class_name(constructor方法的参数表)`

### 创建

```js
//定义类
class Person {
	constructor(first, last, age, gender, interests) {
		this.name = {
			first,
			last,
		}
		this.age = age
		this.gender = gender
		this.interests = interests
	}

	greeting() {
		console.log(`Hi! I'm ${this.name.first}`)
	}

	farewell() {
		console.log(`${this.name.first} has left the building. Bye for now!`)
	}
}

//使用
let han = new Person("Han", "Solo", 25, "male", ["Smuggling"])
han.greeting()
// Hi! I'm Han
```

### 子类

```js
class Teacher extends Person {
	constructor(subject, grade) {
		super() // 通过super()调用父类Person的构造函数
		//super可以传参
		this.subject = subject
		this.grade = grade
	}
}
```

### Getter & Setter

​ getter 和 setter 成对工作。Getter 返回变量的当前值，其相应的 Setter 将变量的值更改为其定义的变量。

```js
class Teacher extends Person {
  constructor(first, last, age, gender, interests, subject, grade) {
    super(first, last, age, gender, interests);
    // subject and grade are specific to Teacher
    this._subject = subject;
    this.grade = grade;
  }
  get subject() {
    return this._subject;
  }

  set subject(newSubject) {
    this._subject = newSubject;
  }
}
//类似于数据代理，使用subject代理_subject，
调用：
var teacher = new Teacher()
teacher.subject  //调用get subject()
teacher.subject = 'xx' //调用 set subject(newSubject)
```

### static

**static 类方法是在类本身上定义的。您不能在对象上调用 static 方法，只能在对象类上调用。**

```js
class Car {
	constructor(name) {
		this.name = name
	}
	static hello() {
		return "Hello!!"
	}
}

let myCar = new Car("Ford")

// 您可以在 Car 类上调用 'hello()' ：
document.getElementById("demo").innerHTML = Car.hello()

// 但不能在 Car 对象上调用：
// document.getElementById("demo").innerHTML = myCar.hello();
// 此举将引发错误。
```

# 严格模式

**"use strict"; 定义 JavaScript 代码应该以“严格模式”执行。**

"use strict" 是 JavaScript 1.8.5 中的新指令（ECMAScript version 5）。它不算一条语句，而是一段文字表达式，更早版本的 JavaScript 会忽略它。"use strict"; 的作用是指示 JavaScript 代码应该以“严格模式”执行。在严格模式中，您无法，例如，使用未声明的变量。

​ 通过在脚本或函数的开头添加 **"use strict";** 来声明严格模式。在脚本开头进行声明，拥有全局作用域（脚本中的所有代码均以严格模式来执行）：

禁止：

-   在不声明变量的情况下使用变量，是不允许的
-   在不声明对象的情况下使用对象也是不允许的
-   删除变量（或对象）是不允许的
-   删除函数是不允许的：
-   重复参数名是不允许的
-   八进制数值文本是不允许的
-   转义字符是不允许的
-   写入只读属性是不允许的
-   写入只能获取的属性是不允许的
-   删除不可删除的属性是不允许的
-   字符串 "eval" 不可用作变量
-   字符串 "arguments" 不可用作变量
-   with 语句是不允许的
-   处于安全考虑，不允许 eval() 在其被调用的作用域中创建变量
-   在类似 f() 的函数调用中，this 的值是全局对象。在严格模式中，现在它成为了 undefined。
-   严格模式中不允许使用为未来预留的关键词。它们是：
    -   implements
    -   interface
    -   let
    -   package
    -   private
    -   protected
    -   public
    -   static
    -   yield

warn:

​ "use strict" 指令只能在脚本或函数的*开头*被识别。

# 模块化编程

## 导出 export es6

有两种不同的导出方式，命名导出和默认导出。你能够在每一个模块中定义多个命名导出，但是只允许有一个默认导出。

```js
// 导出单个特性
export let name1, name2, …, nameN; // also var, const
export let name1 = …, name2 = …, …, nameN; // also var, const
export function FunctionName(){...}
export class ClassName {...}
// 导出列表
export { name1, name2, …, nameN };
// 重命名导出
export { variable1 as name1, variable2 as name2, …, nameN };
// 解构导出并重命名
export const { name1, name2: bar } = o;
```

```js
// 默认导出
export default expression;
export default function (…) { … } // also class, function*
export default function name1(…) { … } // also class, function*
export { name1 as default, … };
```

```js
// 导出模块合集
export * from …; // does not set the default export
export * as name1 from …; // Draft ECMAScript® 2O21
export { name1, name2, …, nameN } from …;
export { import1 as name1, import2 as name2, …, nameN } from …;
export { default } from …;
```

## 导入 import es6

静态的 `**import**` 语句用于导入由另一个模块导出的绑定。无论是否声明了 [`strict mode`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Strict_mode)，导入的模块都运行在严格模式下。在浏览器中，`import` 语句只能在声明了 `type="module"` 的 `script` 的标签中使用。

此外，还有一个类似函数的动态 `import()`，它不需要依赖 `type="module"` 的 script 标签。

```js
import defaultExport from "module-name";
import * as name from "module-name";
import { export } from "module-name";
import { export as alias } from "module-name";
import { export1 , export2 } from "module-name";
import { foo , bar } from "module-name/path/to/specific/un-exported/file";
import { export1 , export2 as alias2 , [...] } from "module-name";
import defaultExport, { export [ , [...] ] } from "module-name";
import defaultExport, * as name from "module-name";
import "module-name";
var promise = import("module-name");//这是一个处于第三阶段的提案。
```

## require 与 expotrs(commonjs)

```js
//a.js中
module.export = {
	a: function () {
		console.log(666)
	},
}

//b.js中
var obj = require("../a.js")
obj.a() //666
```

-   [`Number`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Number)
-   [`BigInt`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt)
-   [`Math`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math)
-   [`Date`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Date)

# 数字

## Number

```js
new Number(number):Number //new 返回对象object，由其参数转换而来。如果无法转换数字，则返回 NaN
Number(number):Number // 不加new,返回一个数字number
//属性
Number.EPSILON //表示 1 与Number可表示的大于 1 的最小的浮点数之间的差值,2^-52
Number.MAX_SAFE_INTEGER //最大的安全整数2^53 - 1
Number.MAX_VALUE //最大数值,1.79E+308。大于 MAX_VALUE 的值代表 "Infinity"。
Number.MIN_SAFE_INTEGER // 最小-(2^53 - 1)
Number.MIN_VALUE //最小的正值 5e-324。小于 MIN_VALUE ("underflow values") 的值将会转换为 0。
Number.NaN //表示“非数字”（Not-A-Number）。和 NaN 相同
Number.NEGATIVE_INFINITY //负无穷大
Number.POSITIVE_INFINITY //正无穷大
//方法
Number.isFinite(number) //是否是一个有穷数
Number.isInteger(number) //是否整数
Number.isNaN(number) // 是否是数字
Number.isSafeInteger(number)//是否是安全整数
Number.parseFloat(string)
Number.parseInt(string, radix?:number=10) // 字符串转整数,且可选进制(2-36)
Number.prototype.toExponential(number?):string//一个用幂的形式 (科学记数法) 来表示Number 对象的字符串,小数位数由参数指定
Number.prototype.toFixed(n?:number=0):string //四舍五入保留number位有效数字
Number.prototype.toLocaleString()
Number.prototype.toPrecision(precision?:number) //返回字符串值，它包含了指定长度的数字：
Number.prototype.toString()
Number.prototype.valueOf() //以数值返回数值
//相关全局函数
parseFloat() //解析一段字符串并返回数值。允许空格。只返回首个数字：
parseInt()   //解析一段字符串并返回数值。允许空格。只返回首个数字
```

## BigInt

一种内置对象，它提供了一种方法来表示大于 2^53 - 1 的整数

可以用在一个整数字面量后面加 n 的方式定义一个 BigInt ，如：10n，或者调用函数 BigInt()（但不包含 new 运算符）并传递一个整数值或字符串值。

```js
typeof 1n // bigint
BigInt(number | string)
// 以下操作符可以和 BigInt 一起使用： +、*、-、**、%。除 >>> （无符号右移）之外的 位操作 (en-US) 也可以支持。因为 BigInt 都是有符号的， >>> （无符号右移）不能用于 BigInt。为了兼容 asm.js，BigInt 不支持单目 (+) 运算符。
// 警告： 当使用 BigInt 时，带小数的运算会被取整。
方法
BigInt.asIntN() //将 BigInt 值转换为一个 -2^(width-1) 与 2^(width-1) - 1 之间的有符号整数。
BigInt.asUintN() //将一个 BigInt 值转换为 0 与 2^(width) - 1 之间的无符号整数。
BigInt.prototype.toLocaleString()
BigInt.prototype.toString()
BigInt.prototype.valueOf()
```

对任何 BigInt 值使用 JSON.stringify() 都会引发 TypeError，因为默认情况下 BigInt 值不会在 JSON 中序列化。但是，如果需要，可以实现 toJSON 方法：

```js
BigInt.prototype.toJSON = function () {
	return this.toString()
}
```

## Math

Math 用于 Number 类型。它不支持 BigInt。

与其他全局对象不同的是，Math 不是一个构造器。Math 的所有属性与方法都是静态的。

```js
Math
//属性
Math.E // e=2.718
Math.LN10 //ln10  2.303
Math.LN2 // ln2  0.693
Math.LOG10E // 以 10 为底的 E 的对数，约等于 0.434。
Math.LOG2E //以 2 为底的 E 的对数，约等于 1.443。
Math.PI // 圆周率，一个圆的周长和直径之比，约等于 3.14159。
Math.SQRT1_2 // 二分之一 ½ 的平方根，同时也是 2 的平方根的倒数 约等于 0.707。
Math.SQRT2 // 2 的平方根，约等于 1.414。
//方法
Math.abs(x) // 绝对值
Math.acos(x) // 反余弦
Math.acosh(x) //反双曲余弦值
Math.asin(x)
Math.asinh(x)
Math.atan(x)
Math.atan2(y, x) //返回 y/x 的反正切值。
Math.atanh(x)
Math.cbrt(x) //立方根
Math.ceil(x) //返回大于一个数的最小整数，即一个数向上取整后的值。
Math.clz32(x) //返回一个 32 位整数的前导零的数量。
Math.cos(x)
Math.cosh(x)
Math.exp(x) //返回欧拉常数的参数次方，E^x，其中 x 为参数，E 是欧拉常数（2.718...，自然对数的底数）
Math.expm1(x) // 返回 exp(x) - 1 的值。
Math.floor(x) //返回小于一个数的最大整数，即一个数向下取整后的值。
Math.fround(x) // 返回最接近一个数的单精度浮点型表示。
Math.hypot(x1,x2,x3,...) //返回其所有参数平方和的平方根。
Math.imul(x,y) //返回 32 位整数乘法的结果
Math.log(x) // 返回一个数的自然对数（㏒e，即 ㏑）
Math.log10(x)//lg
Math.log1p(x)//返回一个数加 1 的和的自然对数（㏒e，即 ㏑）。
Math.log2(x)//
Math.max(x,y,z,....) // 返回零到多个数值中最大值。
Math.min(x)//返回零到多个数值中最小值。
Math.pow(x,y) // x^y
Math.random() // 返回一个 0 到 1 之间的伪随机数
Math.round(x) // 返回四舍五入后的整数。
Math.sign(x)
Math.sin(x)
Math.sinh(x)
Math.sqrt(x) // 返回一个数的平方根。
Math.tan(x)
Math.tanh(x)
Math.trunc(x) // 返回一个数的整数部分，直接去除其小数点及之后的部分。
```

# Date 日期

创建一个 JavaScript Date 实例，该实例呈现时间中的某个时刻。Date 对象则基于 Unix Time Stamp，即自 1970 年 1 月 1 日（UTC）起经过的毫秒数。

```js
new Date();
new Date(value);
new Date(dateString);
new Date(year, monthIndex(0-11) [, day(1-31) [, hours(0-23) [, minutes [, seconds [, milliseconds]]]]]);
//方法
Date.now() // 返回自 1970-1-1 00:00:00 UTC（世界标准时间）至今所经过的毫秒数。
Date.parse() // 解析一个表示日期的字符串，并返回从 1970-1-1 00:00:00 所经过的毫秒数
Date.UTC() // 接受和构造函数最长形式的参数相同的参数（从 2 到 7），并返回从 1970-01-01 00:00:00 UTC 开始所经过的毫秒数。
Date.prototype[@@toPrimitive]
Date.prototype.getDate()
Date.prototype.getDay()
Date.prototype.getFullYear()
Date.prototype.getHours()
Date.prototype.getMilliseconds()
Date.prototype.getMinutes()
Date.prototype.getMonth()
Date.prototype.getSeconds()
Date.prototype.getTime()
Date.prototype.getTimezoneOffset()
Date.prototype.getUTCDate()
Date.prototype.getUTCDay()
Date.prototype.getUTCFullYear()
Date.prototype.getUTCHours()
Date.prototype.getUTCMilliseconds()
Date.prototype.getUTCMinutes()
Date.prototype.getUTCMonth()
Date.prototype.getUTCSeconds()
Date.prototype.getYear() //已弃用
Date.prototype.setDate()
Date.prototype.setFullYear()
Date.prototype.setHours()
Date.prototype.setMilliseconds()
Date.prototype.setMinutes()
Date.prototype.setMonth()
Date.prototype.setSeconds()
Date.prototype.setTime()
Date.prototype.setUTCDate()
Date.prototype.setUTCFullYear()
Date.prototype.setUTCHours()
Date.prototype.setUTCMilliseconds()
Date.prototype.setUTCMinutes()
Date.prototype.setUTCMonth()
Date.prototype.setUTCSeconds()
Date.prototype.setYear() //已弃用
Date.prototype.toDateString()
Date.prototype.toISOString()
Date.prototype.toJSON()
Date.prototype.toLocaleDateString()
Date.prototype.toLocaleString()
Date.prototype.toLocaleTimeString()
Date.prototype.toString()
Date.prototype.toTimeString()
Date.prototype.toUTCString()
Date.prototype.valueOf()

```

## 格式化

```javascript
Date.prototype.format = function (fmt) {
	var o = {
		"M+": this.getMonth() + 1, //月份
		"d+": this.getDate(), //日
		"h+": this.getHours(), //小时
		"m+": this.getMinutes(), //分
		"s+": this.getSeconds(), //秒
		"q+": Math.floor((this.getMonth() + 3) / 3), //季度
		S: this.getMilliseconds(), //毫秒
	}
	if (/(y+)/.test(fmt)) {
		fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length))
	}
	for (var k in o) {
		if (new RegExp("(" + k + ")").test(fmt)) {
			fmt = fmt.replace(RegExp.$1, RegExp.$1.length == 1 ? o[k] : ("00" + o[k]).substr(("" + o[k]).length))
		}
	}
	return fmt
}
```

# 文本处理

## String

字符串对于保存以文本形式表示的数据很有用。一些最常用的字符串操作是检查他们的长度，使用 `+` 和 `+=` 字符串操作符构建和连接它们，使用 `indexOf()` 方法检查子字符串的存在或者位置，或使用 `substring()` 方法提取子字符串。

比较字符串可以用 < >

模板字符串：`${x}` 为嵌入的表达式执行上面的字符串强制转换步骤。

String() (en-US) 函数：String(x) 使用相同的算法去转换 x，只是 Symbol 不会抛出 TypeError，而是返回 "Symbol(description)"，其中 description 是对 Symbol 的描述。

使用 + 运算符："" + x 将其操作数强制转为原始值，而不是字符串，并且对于某些对象，其行为与普通字符串强制转换完全不同。有关更多细节，请参见其参考页。

```js
\0	  null 字符（U+0000 NULL）
\'	单引号（U+0027 APOSTROPHE）
\"	双引号（U+0022 QUOTATION MARK）
\\	反斜杠（U+005C REVERSE SOLIDUS）
\n	换行符（U+000A LINE FEED; LF）
\r	回车符（U+000D CARRIAGE RETURN; CR）
\v	垂直制表符（U+000B LINE TABULATION）
\t	制表符（U+0009 CHARACTER TABULATION）
\b	退格键（U+0008 BACKSPACE）
\f	换页符（U+000C FORM FEED）
\uXXXX    …其中 XXXX 恰好是 0000–FFFF 范围内的 4 个十六进制；例如，\u000A 与 \n（换行）相同；\u0021 是 !	U+0000 和 U+FFFF 之间的 Unicode 码位（Unicode 基于多平台语言）
\u{X}…\u{XXXXXX}    …其中 X…XXXXXX 是 0–10FFFF 范围内的 1-6 个十六进制数字；例如，\u{A} 与 \n（换行符）相同； \u{21} 是 !	U+0000 和 U+10FFFF 之间的 Unicode 码位（整个 Unicode）
\xXX …    其中 XX 恰好是 00–FF 范围内的 2 个十六进制数字；例如，\x0A 与 \n（换行符）相同；\x21 是 !	U+0000 和 U+00FF 之间的 Unicode 码位 （Basic Latin 和 Latin-1 Supplement 块；相当于 ISO-8859-1）
```

多行字符串

```js
const longString = "This is a very long string which needs " + "to wrap across multiple lines because " + "otherwise my code is unreadable."

const longString =
	"This is a very long string which needs \
to wrap across multiple lines because \
otherwise my code is unreadable."
```

字符串基本上表示为 `UTF-16` 码元的序列。然而，整个 Unicode 字符集比 65536 大得多。额外的字符作为代理对（surrogate pairs）存储在 UTF-16 中，代理对是表示单个字符的 16 位码元对。为了避免起义，该对的两个部分必须介于 0xD800 和 0xDFFF 之间，并且这些码元不用于对单码元（single-code-unit）进行编码。因此，“单独的代理项”通常不是操作字符串的有效值——例如 encodeURI() 将为单独的代理项抛出 URIError。**每个 Unicode 字符由一个或者两个 UTF-16 码元组成，也称为 Unicode 码位（codepoint）**。每个 Unicode 码位都可以使用 \u{xxxxxx} 写成一个字符串，其中 xxxxxx 表示 1–6 个十六进制数字。

```js
String
属性
length // 反映字符串的 length。只读
方法
String.raw() // 返回从原始模板字符串创建的字符串。
String.fromCharCode() // 返回使用指定的 Unicode 值序列创建的字符串。
String.fromCodePoint() // 返回使用指定的码位序列创建的字符串
String.prototype[@@iterator]()
String.prototype.big()
String.prototype.charAt() //返回指定 index 处的字符（正好是一个 UTF-16 码元）。
String.prototype.charCodeAt()
String.prototype.codePointAt()
String.prototype.concat()
String.prototype.endsWith()
String.prototype.includes()
String.prototype.indexOf()
String.prototype.lastIndexOf()
String.prototype.localeCompare()
String.prototype.match()
String.prototype.matchAll()
String.prototype.normalize()
String.prototype.padEnd()
String.prototype.padStart()
String.prototype.repeat()
String.prototype.replace()
String.prototype.replaceAll()
String.prototype.search()
String.prototype.slice()
String.prototype.split()
String.prototype.startsWith()
String.prototype.substring()
String.prototype.toLocaleLowerCase()
String.prototype.toLocaleUpperCase()
String.prototype.toLowerCase()
String.prototype.toString()
String.prototype.toUpperCase()
String.prototype.trim()
String.prototype.trimEnd()
String.prototype.trimStart()
String.prototype.valueOf()
String.prototype.sup()//已弃用
String.prototype.strike()//已弃用
String.prototype.sub()//已弃用
String.prototype.substr()//已弃用
String.prototype.small()//已弃用
String.prototype.link()//已弃用
String.prototype.italics()//已弃用
String.prototype.fixed()//已弃用
String.prototype.fontcolor()//已弃用
String.prototype.fontsize()//已弃用
String.prototype.blink()//已弃用
String.prototype.bold()//已弃用
String.prototype.anchor()//已弃用
String.prototype.at()//已弃用 返回指定索引处的字符（正好是一个 UTF-16 码元）。接受负整数，从最后一个字符串字符开始倒数。
```

## RegExp

正则表达式是用于匹配字符串中字符组合的模式。在 JavaScript 中，正则表达式也是对象。这些模式被用于 RegExp 的 exec 和 test 方法，以及 String 的 match、matchAll、replace、search 和 split 方法

```js
// 1. 使用一个正则表达式字面量，其由包含在斜杠之间的模式组成
let regexp = /abc/g
// 2. 使用构造函数
var myRe = new RegExp("ab+c") // myRe.source = ab+c
var myRe = new RegExp("d(b+)d", "g") // 开启g,myRe.lastIndex开始下一个匹配的起始索引值

i 执行对大小写不敏感的匹配
g 执行全局匹配（查找所有匹配而非在找到第一个匹配后停止）。
m 执行多行匹配。

let array = /abc/.exec("abcccc") // 返回字符串中所有匹配的子串组成的类数组结构,array.index 第一个子串索引值,array.input初始字符串,
let boolean = /abc/.test("abcccc") //一个在字符串中测试是否匹配的 RegExp 方法，它返回 true 或 false。
let array = "cdbbdbsbz".match(/d(b+)d/g) //一个在字符串中执行查找匹配的 String 方法，它返回一个数组，在未匹配到时会返回 null。
let iterator = "cdbbdbsbz".matchAll(/d(b+)d/g) //一个在字符串中执行查找所有匹配的 String 方法，它返回一个迭代器（iterator）。
let number = "cdbbdbsbz".search(/d(b+)d/g) //一个在字符串中测试匹配的 String 方法，它返回匹配到的位置索引，或者在失败时返回-1。
let new = "cdbbdbsbz".replace(/d(b+)d/g)  //一个在字符串中执行查找匹配的 String 方法，并且使用替换字符串替换掉匹配到的子字符串。
let array = "cdbbdbsbz".split(/d(b+)d/g) //一个使用正则表达式或者一个固定字符串分隔一个字符串，并将分隔后的子字符串存储到数组中的 String 方法。
```

```js
RegExp
属性
get RegExp[@@species]
RegExp.prototype.dotAll
RegExp.prototype.flags
RegExp.prototype.global
RegExp.prototype.hasIndices
RegExp.prototype.ignoreCase
// 已弃用
// RegExp.input ($_)
RegExp: lastIndex // 该索引表示从哪里开始下一个匹配
// 已弃用
// RegExp.lastMatch ($&)
// 已弃用
// RegExp.lastParen ($+)
// 已弃用
// RegExp.leftContext ($`)
RegExp.prototype.multiline
// 已弃用
// RegExp.$1, …, RegExp.$9
// 已弃用
// RegExp.rightContext ($')
RegExp.prototype.source
RegExp.prototype.sticky
RegExp.prototype.unicode
// 方法
// RegExp.prototype[@@match]()
RegExp.prototype[@@matchAll]()
RegExp.prototype[@@replace]()
RegExp.prototype[@@search]()
RegExp.prototype[@@split]()
// 已弃用
// RegExp.prototype.compile()
RegExp.prototype.exec()
RegExp.prototype.test()
RegExp.prototype.toString()

```

# 数组

-   [`Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Array)
-   [`Int8Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int8Array)
-   [`Uint8Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint8Array)
-   [`Uint8ClampedArray`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint8ClampedArray)
-   [`Int16Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int16Array)
-   [`Uint16Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint16Array)
-   [`Int32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Int32Array)
-   [`Uint32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Uint32Array)
-   [`Float32Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Float32Array)
-   [`Float64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Float64Array)
-   [`BigInt64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigInt64Array)
-   [`BigUint64Array`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/BigUint64Array)

## Array

​ 数组用方括号书写。数组的项目由逗号分隔。数组索引基于零，这意味着第一个项目是 [0]，第二个项目是 [1]，以此类推。

定义,空格和折行并不重要。声明可横跨多行,请不要最后一个元素之后写逗号

```js
var array_name = [array_value1,....];

// 不建议使用new
var cars = new Array("Saab", "Volvo", "BMW");
```

使用

```
array_name[index]
```

数组是一种特殊类型的对象。在 JavaScript 中对数组使用 typeof 运算符会返回 "object"。

您可以在数组保存对象。您可以在数组中保存函数。你甚至可以在数组中保存数组.

## 属性

`length` 属性返回数组的长度（数组元素的数目）。

## 遍历

### for

```js
var fruits, text, fLen, i

fruits = ["Banana", "Orange", "Apple", "Mango"]
fLen = fruits.length
text = "<ul>"
for (i = 0; i < fLen; i++) {
	text += "<li>" + fruits[i] + "</li>"
}
```

### foreach

**Array.foreach()** 函数，为每个数组元素调用一次函数（回调函数）

参数为只有一个参数的函数；

**注释：**该函数接受 3 个参数：

-   项目值
-   项目索引
-   数组本身

```js
Array.foreach(function (value, index, array) {
	//....
})
```

```js
var fruits, text
fruits = ["Banana", "Orange", "Apple", "Mango"]

text = "<ul>"
fruits.forEach(myFunction)
text += "</ul>"

function myFunction(value) {
	text += "<li>" + value + "</li>"
}
```

### map

map() 方法通过对每个数组元素执行函数来创建新数组。

map() 方法不会对没有值的数组元素执行函数。

map() 方法不会更改原始数组。

```js
// 这个例子将每个数组值乘以2：
var numbers1 = [45, 4, 9, 16, 25]
var numbers2 = numbers1.map(myFunction)

function myFunction(value, index, array) {
	return value * 2
}
```

请注意，该函数有 3 个参数：

-   项目值
-   项目索引
-   数组本身

当回调函数仅使用 value 参数时，可以省略索引和数组参数

### filter

filter() 方法创建一个包含通过测试的数组元素的新数组。

```js
var numbers = [45, 4, 9, 16, 25]
var over18 = numbers.filter(myFunction) // 45 28

function myFunction(value, index, array) {
	return value > 18
}
```

请注意此函数接受 3 个参数：

-   项目值
-   项目索引
-   数组本身

在上面的例子中，回调函数不使用 index 和 array 参数，因此可以省略它们

### reduce

reduce() 方法在每个数组元素上运行函数，以生成（减少它）单个值。

reduce() 方法在数组中从左到右工作。另请参阅 reduceRight()。

reduce() 方法不会减少原始数组。

```js
var numbers1 = [45, 4, 9, 16, 25]
var sum = numbers1.reduce(myFunction) // 99
// 相当于 var sum = numbers1.reduce(myFunction, 0);
function myFunction(total, value, index, array) {
	return total + value
}
```

请注意此函数接受 4 个参数：

-   总数（初始值/先前返回的值）
-   项目值
-   项目索引
-   数组本身

上例并未使用 index 和 array 参数。

reduce() 方法能够接受一个初始值：

```js
var sum = numbers1.reduce(myFunction, 100)
```

### reduceRight

reduceRight() 方法在每个数组元素上运行函数，以生成（减少它）单个值。

reduceRight() 方法在数组中从右到左工作。另请参阅 reduce()。

reduceRight() 方法不会减少原始数组。

```js
var numbers1 = [45, 4, 9, 16, 25]
var sum = numbers1.reduceRight(myFunction) //99

function myFunction(total, value, index, array) {
	return total + value
}
```

请注意此函数接受 4 个参数：

-   总数（初始值/先前返回的值）
-   项目值
-   项目索引
-   数组本身

### every

every() 方法检查所有数组值是否通过测试。

```js
var numbers = [45, 4, 9, 16, 25]
var allOver18 = numbers.every(myFunction) //false

function myFunction(value, index, array) {
	return value > 18
}
```

请注意此函数接受 3 个参数：

-   项目值
-   项目索引
-   数组本身

### some

some() 方法检查某些数组值是否通过了测试。

```js
var numbers = [45, 4, 9, 16, 25]
var someOver18 = numbers.some(myFunction) //true

function myFunction(value, index, array) {
	return value > 18
}
```

请注意此函数接受 3 个参数：

-   项目值
-   项目索引
-   数组本身

## 添加

### push

**push() 方法**：添加一个或多个要添加到数组末尾的元素

```js
var fruits = ["Banana", "Orange", "Apple", "Mango"]
fruits.push("Lemon") // 向 fruits 添加一个新元素 (Lemon)
```

### unshift

unshift(new_element) 添加一个或多个要添加到数组开头的元素

```
myArray.unshift('Edinburgh');
myArray;
```

### length

```js
fruits[fruits.length] = "Lemon" //警告：添加最高索引的元素可在数组中创建未定义的“洞”
```

### splice

**splice()** 方法可用于向数组添加新项,splice() 方法返回一个包含已删除项的数组

```js
var fruits = ["Banana", "Orange", "Apple", "Mango"]
fruits.splice(2, 0, "Lemon", "Kiwi") //fruits:  Banana,Orange,Lemon,Kiwi,Apple,Mango
/*
第一个参数（2）定义了应添加新元素的位置（拼接）。
第二个参数（0）定义应删除多少元素。
其余参数（“Lemon”，“Kiwi”）定义要添加的新元素。
*/

//替换
var res = fruits.splice(2, 2, "Lemon", "Kiwi")
// res:["Apple", "Mango"]  fruits:["Banana", "Orange","Lemon", "Kiwi"]

// 删除
var res = fruits.splice(2, 2)
// res:[]  fruits:["Banana", "Orange"]

// 添加
var res = fruits.splice(fruits.length, 0, "sb")
// fruits:["Banana", "Orange", "Apple", "Mango","sb"]
```

### concat

**concat()** 方法通过合并（连接）现有数组来创建一个新数组

```js
var myGirls = ["Cecilie", "Lone"]
var myBoys = ["Emil", "Tobias", "Linus"]
var myChildren = myGirls.concat(myBoys) // 连接 myGirls 和 myBoys
/*
concat() 方法不会更改现有数组。它总是返回一个新数组。
concat() 方法可以使用任意数量的数组参数
*/
```

## 删除

### delete

既然 JavaScript 数组属于对象，其中的元素就可以使用 JavaScript delete 运算符来*删除*：

```js
var fruits = ["Banana", "Orange", "Apple", "Mango"]
delete fruits[0] // 把 fruits 中的首个元素改为 undefined
```

使用 delete 会在数组留下未定义的空洞。请使用 pop() 或 shift() 取而代之。

### slice

**slice()** 方法用数组的某个片段切出新数组。

```js
var fruits = ["Banana", "Orange", "Lemon", "Apple", "Mango"]
var citrus = fruits.slice(1) //Orange,Lemon,Apple,Mango
var citrus = fruits.slice(3) // Apple,Mango
var citrus = fruits.slice(1, 3) //Orange,Lemon

//slice() 方法创建新数组。它不会从源数组中删除任何元素
```

### pop

从数组中删除最后一个元素的话直接使用 `pop()`

```js
// pop返回删除的元素
let removedItem = myArray.pop()
myArray
removedItem
```

### shift

从数组中删除第一个元素的话直接使用 `shift()`

```js
// shift返回删除的元素
let removedItem = myArray.shift()
```

## isArray

### isArray

ECMAScript 5 定义了新方法 Array.isArray()

```js
Array.isArray(fruits) // 返回 true
```

创建您自己的 isArray() 函数以解决此问题

```js
function isArray(x) {
	return x.constructor.toString().indexOf("Array") > -1
}
/*
假如参数为数组，则上面的函数始终返回 true。
或者更准确的解释是：假如对象原型包含单词 "Array" 则返回 true。
*/
```

### instanceof

假如对象由给定的构造器创建，则 _instanceof_ 运算符返回 true

```js
var fruits = ["Banana", "Orange", "Apple", "Mango"]

fruits instanceof Array // 返回 true
```

## 转换

### toString

JavaScript 方法 **toString()** 把数组转换为数组值（逗号分隔）的字符串。

### join

**join()** 方法也可将所有数组元素结合为一个字符串。

```js
var fruits = ["Banana", "Orange", "Apple", "Mango"]
document.getElementById("demo").innerHTML = fruits.join(" * ") //Banana * Orange * Apple * Mango
```

### 排序

### sort

​ **sort()**方法以字母顺序对数组进行排序;

数字排序

```js
/*
	默认地，sort() 函数按照字符串顺序对值进行排序。该函数很适合字符串（"Apple" 会排在 "Banana" 之前）。不过，如果数字按照字符串来排序，则 "25" 大于 "100"，因为 "2" 大于 "1"。正因如此，sort() 方法在对数值排序时会产生不正确的结果。
	我们通过一个比值函数来修正此问题：
*/

var points = [40, 100, 1, 5, 25, 10]
// 升序
points.sort(function (a, b) {
	return a - b
})
// 降序
points.sort(function (a, b) {
	return b - a
})
```

### recerse

​ **reverse()** 方法反转数组中的元素;

### 比值函数

​ 比较函数的目的是定义另一种排序顺序。返回一个负，零或正值，这取决于参数：

```js
function(a, b){return a-b}
// 当 sort() 函数比较两个值时，会将值发送到比较函数，并根据所返回的值（负、零或正值）对这些值进行排序。
```

eg：

```js
// 随机排序
points.sort(function (a, b) {
	return 0.5 - Math.random()
})
// 最值
points.sort(function (a, b) {
	return a - b
}) /*min=points[0],max=points[points.length-1]*/
//如果您仅仅需要找到最高或最低值，对整个数组进行排序是效率极低的方法。
// max
function myArrayMax(arr) {
	return Math.max.apply(null, arr)
}
// min
function myArrayMin(arr) {
	return Math.min.apply(null, arr)
}
// 最快
function myArrayMax(arr) {
	var len = arr.length
	var max = -Infinity
	while (len--) {
		if (arr[len] > max) {
			max = arr[len]
		}
	}
	return max
}
function myArrayMin(arr) {
	var len = arr.length
	var min = Infinity
	while (len--) {
		if (arr[len] < min) {
			min = arr[len]
		}
	}
	return min
}
```

### 查找

### indexOf

indexOf() 方法在数组中搜索元素值并返回其位置。

**注释：**第一个项目的位置是 0，第二个项目的位置是 1，以此类推。

```js
var fruits = ["Apple", "Orange", "Apple", "Mango"]
var a = fruits.indexOf("Apple") //0
var a = fruits.lastIndexOf("Apple") //2
```

```
array.indexOf(item, start)
```

| _item_  | 必需。要检索的项目。                                                 |
| ------- | -------------------------------------------------------------------- |
| _start_ | 可选。从哪里开始搜索。负值将从结尾开始的给定位置开始，并搜索到结尾。 |

如果未找到项目，Array.indexOf() 返回 -1。

如果项目多次出现，则返回第一次出现的位置。

特殊

```js
"abc".indexOf("") // 0
"abc".indexOf("d") // -1
"abc".indexOf("a") // 0
```

### lastIndexOf

Array.lastIndexOf() 与 Array.indexOf() 类似，但是从数组结尾开始搜索。

### find

find() 方法返回通过测试函数的第一个数组元素的值。

```js
var numbers = [4, 9, 16, 25, 29]
var first = numbers.find(myFunction) //25

function myFunction(value, index, array) {
	return value > 18
}
```

请注意此函数接受 3 个参数：

-   项目值
-   项目索引
-   数组本身

### findIndex

findIndex() 方法返回通过测试函数的第一个数组元素的索引。

```js
var numbers = [4, 9, 16, 25, 29]
var first = numbers.findIndex(myFunction) //3

function myFunction(value, index, array) {
	return value > 18
}
```

# 键值对

-   [`Map`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Map)
-   [`Set`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Set)
-   [`WeakMap`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WeakMap)
-   [`WeakSet`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WeakSet)

## Map

​ Map 对象存有键值对，其中的键可以是任何数据类型。Map 对象记得键的原始插入顺序。Map 对象具有表示映射大小的属性。

方法

```js
new Map() //创建新的 Map 对象。
set(key, value) //为 Map 对象中的键设置值。
get(k) //获取 Map 对象中键的值。
entries() //返回 Map 对象中键/值对的数组。
keys() //返回 Map 对象中键的数组。
values() //返回 Map 对象中值的数组。
clear() //删除 Map 中的所有元素。
delete key //删除由键指定的元素。
has(key) //如果键存在，则返回 true。
forEach() //为每个键/值对调用回调。
```

属性

```js
size //获取 Map 对象中某键的值。
```

例

```js
// 创建对象
const apples = { name: "Apples" }
const bananas = { name: "Bananas" }
const oranges = { name: "Oranges" }

// 创建新的 Map
const fruits = new Map()

// Add new Elements to the Map
fruits.set(apples, 500)
fruits.set(bananas, 300)
fruits.set(oranges, 200)

// 创建新的 Map
const fruits2 = new Map([
	[apples, 500],
	[bananas, 300],
	[oranges, 200],
])

fruits.get(apples) // 返回 500
```

## Set

​ Set 是唯一值的集合。每个值在 Set 中只能出现一次。一个 Set 可以容纳任何数据类型的任何值。

```js
new Set()	//创建新的 Set 对象。
add()		//向 Set 添加新元素。
clear()		//从 Set 中删除所有元素。
delete()	//删除由其值指定的元素。
entries()	//返回 Set 对象中值的数组。
has()		//如果值存在则返回 true。
forEach()	//为每个元素调用回调。
keys()		//返回 Set 对象中值的数组。
values()	//与 keys() 相同。
size		//返回元素计数。

```

```js
// 创建新的变量
const a = "a"
const b = "b"
const c = "c"

// 创建 Set
const letters = new Set()

// Add the values to the Set
letters.add(a)
letters.add(b)
letters.add(c)

// 创建新的 Set
const letters = new Set(["a", "b", "c"])

typeof letters // 返回 object。

letters instanceof Set // 返回 true
```

# 结构化数据

-   [`ArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/ArrayBuffer)
-   [`SharedArrayBuffer`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/SharedArrayBuffer)
-   [`Atomics`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Atomics)
-   [`DataView`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/DataView)
-   [`JSON`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/JSON)

## JSON

一个 JSON 对象可以被储存在它自己的文件中，这基本上就是一个文本文件，扩展名为 `.json`， 还有 [MIME type](https://developer.mozilla.org/zh-CN/docs/Glossary/MIME_type) 用于 `application/json`.

```JS
//JSON
//以文本字符串形式接受JSON对象作为参数，并返回相应的对象。
//用来序列化对象、数组、数值、字符串、布尔值和 null
function parse(str string): Object
//接收一个对象作为参数，返回一个对应的JSON字符串。
function stringify(obj Object):string

"{\"text\":\"afagaf\"}"
```

# 异步

## 事件监听与回调函数

​ 异步 callbacks 其实就是函数，只不过是作为参数传递给那些在后台执行的其他函数. 当那些后台运行的代码结束，就调用 callbacks 函数，通知你工作已经完成，或者其他有趣的事情发生了。

```js
function loadAsset(url, type, callback) {
	let xhr = new XMLHttpRequest()
	xhr.open("GET", url)
	xhr.responseType = type

	xhr.onload = function () {
		callback(xhr.response)
	}

	xhr.send()
}

function displayImage(blob) {
	let objectURL = URL.createObjectURL(blob)

	let image = document.createElement("img")
	image.src = objectURL
	document.body.appendChild(image)
}

loadAsset("coffee.jpg", "blob", displayImage)
```

请注意，不是所有的回调函数都是异步的 — 有一些是同步的。一个例子就是使用 `Array.prototype.forEach()`

## Promises

1. promise 是一种中间态,根据内部的执行情况返回相应状态
    1. 创建 promise 时，它既不是成功也不是失败状态。这个状态叫作**pending**（待定）。
    2. 当 promise 返回时，称为 resolved（已解决）.
        1. 一个成功**resolved**的 promise 称为**fullfilled**（**实现**）。它返回一个值，可以通过将`.then()`块链接到 promise 链的末尾来访问该值。` .then()`块中的执行程序函数将包含 promise 的返回值。
        2. 一个不成功**resolved**的 promise 被称为**rejected**（**拒绝**）了。它返回一个原因（**reason**），一条错误消息，说明为什么拒绝 promise。可以通过将`.catch()`块链接到 promise 链的末尾来访问此原因。
2. 不能与`try...catch`一起工作
3. 可以与 async/await 一起工作

```js
// new Promise() 之后不用调用就会执行，所以一般用函数包一下
const myFirstPromise = new Promise((resolve, reject) => {
	// do something asynchronous which eventually calls either:
	//
	//   resolve(someValue)        // fulfilled
	// or
	//   reject("failure reason")  // rejected
})
myFirstPromise()
	.then(
		res => {},
		err => {}
	)
	.catch(e => {})
	.finally(() => {})

function runAsync() {
	var p = new Promise(function (resolve, reject) {
		//做一些异步操作
		setTimeout(function () {
			console.log("执行完成")
			resolve("随便什么数据")
		}, 2000)
	})
	return p
}
// runAsync()
```

## 超时和间隔

很长一段时间以来，web 平台为 JavaScript 程序员提供了许多函数，这些函数允许您在一段时间间隔过后异步执行代码，或者重复异步执行代码块，直到您告诉它停止为止。

1. `let flag = setTimeout(function,delay,arg1,arg2,...)` 定时器,毫秒,0 表示尽快执行
2. `clearTimeout(flag)` 关闭定时器
3. `let flag = setInterval(func, [delay, arg1, arg2, ...])`间隔循环,delay 执行代码之前等待的最少时间间隔,函数执行时间不小于 delay 设置的时间
4. `clearInterval(flag)` 关闭时间间隔

## async/await

1. `async` 修饰函数,将函数变为异步函数,函数返回值为`Promises`
2. `await` 只能在 `async` 函数内部使用,修饰函数调用,他会暂停 async 函数以等待所修饰的异步函数执行完毕,然后再继续执行;在 await 表达式之后的代码可以被认为是存在在链式调用的 then 回调中，多个 await 表达式都将加入链式调用的 then 回调中，返回值将作为最后一个 then 回调的返回值。
3. `async`可以有 0 个或多个`await`函数

```js
async function myFetch() {
	let response = await fetch("coffee.jpg") //获取资源是，暂停
	return await response.blob()
}

myFetch()
	.then(blob => {
		let objectURL = URL.createObjectURL(blob)
		let image = document.createElement("img")
		image.src = objectURL
		document.body.appendChild(image)
	})
	.catch(e => console.log(e))
```

# 反射 Reflect es6

Reflect 是一个内置的对象，它提供拦截 JavaScript 操作的方法。这些方法与 proxy handlers (en-US)的方法相同。Reflect 不是一个函数对象，因此它是不可构造的。

-   [`Reflect`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Reflect)

```js
Reflect.apply(target, thisArgument, argumentsList)//对一个函数进行调用操作，同时可以传入一个数组作为调用参数。和 Function.prototype.apply() 功能类似。
Reflect.construct(target, argumentsList[, newTarget])//对构造函数进行 new 操作，相当于执行 new target(...args)。
Reflect.defineProperty(target, propertyKey, attributes)//和 Object.defineProperty() 类似。如果设置成功就会返回 true
Reflect.deleteProperty(target, propertyKey)//作为函数的delete操作符，相当于执行 delete target[name]。
Reflect.get(target, propertyKey[, receiver])//获取对象身上某个属性的值，类似于 target[name]。
Reflect.getOwnPropertyDescriptor(target, propertyKey)//类似于 Object.getOwnPropertyDescriptor()。如果对象中存在该属性，则返回对应的属性描述符，否则返回 undefined。
Reflect.getPrototypeOf(target)//类似于 Object.getPrototypeOf()。
Reflect.has(target, propertyKey)//判断一个对象是否存在某个属性，和 in 运算符 的功能完全相同。
Reflect.isExtensible(target)//类似于 Object.isExtensible().
Reflect.ownKeys(target)//返回一个包含所有自身属性（不包含继承属性）的数组。(类似于 Object.keys(), 但不会受enumerable 影响).
Reflect.preventExtensions(target)// 似于 Object.preventExtensions()。返回一个Boolean。
Reflect.set(target, propertyKey, value[, receiver])//将值分配给属性的函数。返回一个Boolean，如果更新成功，则返回true。
Reflect.setPrototypeOf(target, prototype)//设置对象原型的函数。返回一个 Boolean，如果更新成功，则返回 true。
```

# 代理 Proxy es6

Proxy 对象用于创建一个对象的代理，从而实现基本操作的拦截和自定义（如属性查找、赋值、枚举、函数调用等）。

-   [`Proxy`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)

```js
const p = new Proxy(target, handler)

Proxy.revocable() //创建一个可撤销的Proxy对象。

//handler 对象是一个容纳一批特定属性的占位符对象。它包含有 Proxy 的各个捕获器（trap）。所有的捕捉器是可选的。如果没有定义某个捕捉器，那么就会保留源对象的默认行为。
handler.getPrototypeOf() //Object.getPrototypeOf 方法的捕捉器。
handler.setPrototypeOf() //Object.setPrototypeOf 方法的捕捉器。
handler.isExtensible() //Object.isExtensible 方法的捕捉器。
handler.preventExtensions() //Object.preventExtensions 方法的捕捉器。
handler.getOwnPropertyDescriptor() //Object.getOwnPropertyDescriptor 方法的捕捉器。
handler.defineProperty() //Object.defineProperty 方法的捕捉器。
handler.has() //in 操作符的捕捉器。
handler.get() //属性读取操作的捕捉器。
handler.set() //属性设置操作的捕捉器。
handler.deleteProperty() //delete 操作符的捕捉器。
handler.ownKeys() //Object.getOwnPropertyNames 方法和 Object.getOwnPropertySymbols 方法的捕捉器。
handler.apply() //函数调用操作的捕捉器。
handler.construct() //new 操作符的捕捉器。
```

## 国际化

-   [`Intl`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl)
-   [`Intl.Collator`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/Collator)
-   [`Intl.DateTimeFormat`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/DateTimeFormat)
-   [`Intl.DisplayNames`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/DisplayNames)
-   [`Intl.ListFormat`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/ListFormat)
-   [`Intl.Locale`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/Locale)
-   [`Intl.NumberFormat`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/NumberFormat)
-   [`Intl.PluralRules`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/PluralRules)
-   [`Intl.RelativeTimeFormat`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Intl/RelativeTimeFormat)

## WebAssembly

-   [`WebAssembly`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly)
-   [`WebAssembly.Module`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Module)
-   [`WebAssembly.Instance`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Instance)
-   [`WebAssembly.Memory`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Memory)
-   [`WebAssembly.Table`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/Table)
-   [`WebAssembly.CompileError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/CompileError)
-   [`WebAssembly.LinkError` (en-US)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/LinkError)
-   [`WebAssembly.RuntimeError`](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/WebAssembly/RuntimeError)

# http 与 websocket

## Ajax

Ajax 的核心是 XMLHttpRequest 对象。所有现代浏览器都支持 XMLHttpRequest 对象。

XMLHttpRequest 对象用于同幕后服务器交换数据。这意味着可以更新网页的部分，而不需要重新加载整个页面。

```js
/**
 * 创建请求对象
 */
let request = new XMLHttpRequest()
// 兼容ie6
if (window.XMLHttpRequest) {
  // Mozilla, Safari, IE7+ ...
  httpRequest = new XMLHttpRequest()
} else if (window.ActiveXObject) {
  // IE 6 and older
  httpRequest = new ActiveXObject("Microsoft.XMLHTTP")
}
/**
 * 配置请求
 */
//配置请求
//method：请求类型 GET 或 POST
//url： 请求地址
//async：true（异步）或 false（同步）
//user：可选的用户名称
//psw：可选的密码
request.open(method, url, async, user, psw)
//设置返回值类型 text blob json arraybuffer document
request.responseType = "text"
// 超时时间 毫秒
request.timeout = 1000
//向要发送的报头添加标签/值对
//'Content-Type', '' 分别为
// 表单: multipart/form-data、可以直接发送FormData类型的数据而不用写请求头
// 表单: application/x-www-form-urlencoded、 模仿表单提交,请求头+"a=1&b=2&"
//Cache-Control: no-cache  防止get请求缓存
request.setRequestHeader(key, value)
/**
 * 发送数据
 */
//将请求发送到服务器，用于 GET 请求
request.send()
//将请求发送到服务器，用于 POST 请求
request.send(ArrayBuffer data);
request.send(ArrayBufferView data);
request.send(Blob data);
request.send(Document data);
request.send(DOMString? data);
request.send(FormData data);
/**
 * 处理响应,​ 发送一个请求后，你会收到响应。在这一阶段，你要告诉 XMLHttp 请求对象是由哪一个 JavaScript 函数处理响应，在设置了对象的 `onreadystatechange `属性后给他命名，当请求状态改变时调用函数。
 */
request.onreadystatechange = function(){
  //处理响应
  //request.readyState XMLHttpRequest 的状态
  //request.status  返回请求的状态号
  //request.statusText 返回状态文本
  //request.responseText 	以字符串返回响应数据
	//request.responseXML		以 XML 数据返回响应数据
}

readyState = number	保存 XMLHttpRequest 的状态。
			0：请求未初始化
			1：服务器连接已建立
			2：请求已收到
			3：正在处理请求
			4：请求已完成且响应已就绪
status	返回请求的状态号
		200: "OK"
		403: "Forbidden"
		404: "Not Found"
statusText	返回状态文本（比如 "OK" 或 "Not Found"）
response      返回数据内容
responseText 	以字符串返回响应数据
responseXML		以 XML 数据返回响应数据
/**
 * 事件处理
 */
onabort //当 request 被停止时触发，例如当程序调用 XMLHttpRequest.abort() 时。 也可以使用 onabort 属性。
onerror //当 request 遭遇错误时触发。 也可以使用 onerror 属性
onload //XMLHttpRequest请求成功完成时触发。 也可以使用 onload 属性。
onloadend//当请求结束时触发，无论请求成功 ( load) 还是失败 (abort 或 error)。 也可以使用 onloadend 属性。
onloadstart//接收到响应数据时触发。 也可以使用 onloadstart 属性。
onprogress//当请求接收到更多数据时，周期性地触发。 也可以使用 onprogress 属性。
ontimeout//在预设时间内没有接收到响应时触发。 也可以使用 ontimeout 属性。
/**
 * 其他方法
 */
request.getAllResponseHeaders() //返回所有来自服务器响应的头部信息
request.getResponseHeader() //返回特定的头部信息
request.abort() // 中止已经发送的请求
```

### GET

```js
//创建XHR对象
let request = new XMLHttpRequest()
//设置http方法，与访问URL
request.open("GET", url)
//设置返回值类型
request.responseType = "text"
//处理返回值，异步
request.onload = function () {
	//处理 request.response;
}
//发送请求
request.send()
```

### POST

```js
//创建XHR对象
let request = new XMLHttpRequest()
//设置http方法，与访问URL
request.open("post", url)
//设置返回值类型
request.responseType = "text"
//处理返回值，异步
request.onload = function () {
	//处理 request.response;
}
//发送请求
// 1. 表单
request.setRequestHeader("Content-Type", "application/x-www-form-urlencoded")
request.send("name=value&anothername=1&so=on")
// 2. 表单
request.send(new FormData())
// 3. 表单
const params = new URLSearchParams()
params.append("name", "tom")
params.append("age", 24)
params.append("someNumberString", "18")
xhr.send(params)
//    const params = new URLSearchParams('name=tom&age=24&someNumberString=19')
// 4.
```

## Fetch

[fetch 文档](https://developer.mozilla.org/zh-CN/docs/Web/API/fetch)
Fetch API 基本上是 XHR 的一个现代替代品——它是最近在浏览器中引入的，它使**异步 HTTP 请求**在 JavaScript 中更容易实现，对于开发人员和在 Fetch 之上构建的其他 API 来说都是如此。

`fetch()` 返回一个 promise 对象,只用网络异常或请求被阻止时,promise 为 reject;其他的都为 resolve ;如果响应的 HTTP 状态码不在 200 - 299 的范围内，则设置 resolve 返回值的 ok 属性为 false.
fetch 不会发送跨域 cookie，除非你使用了 credentials 的初始化选项。（自 2018 年 8 月以后，默认的 credentials 政策变更为 same-origin。Firefox 也在 61.0b13 版本中进行了修改）

```js
// Example POST method implementation:
async function postData(url = "", data = {}) {
	// Default options are marked with *
	const response = await fetch(url, {
		method: "POST", // *GET, POST, PUT, DELETE, etc.
		mode: "cors", // no-cors, *cors, same-origin
		cache: "no-cache", // *default, no-cache, reload, force-cache, only-if-cached
		credentials: "same-origin", // include, *same-origin, omit
		headers: {
			"Content-Type": "application/json",
			// 'Content-Type': 'application/x-www-form-urlencoded',
		},
		redirect: "follow", // manual, *follow, error
		referrerPolicy: "no-referrer", // no-referrer, *no-referrer-when-downgrade, origin, origin-when-cross-origin, same-origin, strict-origin, strict-origin-when-cross-origin, unsafe-url
		body: JSON.stringify(data), // body data type must match "Content-Type" header
	})
	return response.json() // parses JSON response into native JavaScript objects
}

postData("https://example.com/answer", { answer: 42 }).then(data => {
	console.log(data) // JSON data parsed by `data.json()` call
})
```

## WebSocket

[WebSocket](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)
客户端与服务端建立 WebSocket 长连接

建立连接和断开连接
发送数据和接收数据
处理错误

```js
WebSocket.binaryType //使用二进制的数据类型连接。
WebSocket.bufferedAmount //只读 未发送至服务器的字节数。
WebSocket.extensions //只读 服务器选择的扩展。
WebSocket.onclose //用于指定连接关闭后的回调函数。
WebSocket.onerror// 用于指定连接失败后的回调函数。
WebSocket.onmessage// 用于指定当从服务器接受到信息时的回调函数。
WebSocket.onopen// 用于指定连接成功后的回调函数。
WebSocket.protocol //只读 服务器选择的下属协议。
WebSocket.readyState// 只读 当前的链接状态。
WebSocket.url //只读 WebSocket 的绝对路径。
WebSocket.close([code[, reason]])//关闭当前链接。
WebSocket.send(data)//对要传输的数据进行排队。

/**
 * 使用 addEventListener() 或将一个事件监听器赋值给本接口的 oneventname 属性，来监听下面的事件。
 */
close//当一个 WebSocket 连接被关闭时触发。 也可以通过 onclose 属性来设置。
error//当一个 WebSocket 连接因错误而关闭时触发，例如无法发送数据时。 也可以通过 onerror 属性来设置。
message//当通过 WebSocket 收到数据时触发。 也可以通过 onmessage 属性来设置。
open//当一个 WebSocket 连接成功时触发。 也可以通过 onopen 属性来设置。


// 检查浏览器是否支持WebSocket，使用的方法是查看window对象是否具有WebSocket属性
if (window.WebSocket != undefined) {
  // WebSocket代码
  var aWebSocket = new WebSocket(url [, protocols]);

}

// Create WebSocket connection.
const socket = new WebSocket('ws://localhost:8080'); // http->ws  https->wss

// Connection opened
socket.addEventListener('open', function (event) {
    socket.send('Hello Server!');
});

// Listen for messages
socket.addEventListener('message', function (event) {
    console.log('Message from server ', event.data);
});
```

## 状态消息

-   [HTML 国家代码](https://www.w3school.com.cn/tags/html_ref_country_codes.asp)
-   [HTTP 方法](https://www.w3school.com.cn/tags/html_ref_httpmethods.asp)

### HTML 错误消息

当浏览器向 Web 服务器请求服务时，可能会发生错误，并且服务器可能会返回错误代码，例如 "404 Not Found"。

通常这些错误被称为 HTML 错误消息。

但是这些消息应称为 HTTP 状态消息。实际上，服务器总会为每个请求返回一条消息。最常见的消息是 200 OK。

以下是可能返回的 HTTP 状态消息的列表：

### 1xx: 信息

| 消息:                   | 描述:                                                                                  |
| :---------------------- | :------------------------------------------------------------------------------------- |
| 100 Continue            | 服务器仅接收到部分请求，但是一旦服务器并没有拒绝该请求，客户端应该继续发送其余的请求。 |
| 101 Switching Protocols | 服务器转换协议：服务器将遵从客户的请求转换到另外一种协议。                             |

### 2xx: 成功

| 消息:                             | 描述:                                                                                                                         |
| :-------------------------------- | :---------------------------------------------------------------------------------------------------------------------------- |
| 200 OK                            | 请求成功（其后是对 GET 和 POST 请求的应答文档。）                                                                             |
| 201 Created                       | 请求被创建完成，同时新的资源被创建。                                                                                          |
| 202 Accepted                      | 供处理的请求已被接受，但是处理未完成。                                                                                        |
| 203 Non-authoritative Information | 文档已经正常地返回，但一些应答头可能不正确，因为使用的是文档的拷贝。                                                          |
| 204 No Content                    | 没有新文档。浏览器应该继续显示原来的文档。如果用户定期地刷新页面，而 Servlet 可以确定用户文档足够新，这个状态代码是很有用的。 |
| 205 Reset Content                 | 没有新文档。但浏览器应该重置它所显示的内容。用来强制浏览器清除表单输入内容。                                                  |
| 206 Partial Content               | 客户发送了一个带有 Range 头的 GET 请求，服务器完成了它。                                                                      |

### 3xx: 重定向

| 消息:                  | 描述:                                                                                                                                                                           |
| :--------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 300 Multiple Choices   | 多重选择。链接列表。用户可以选择某链接到达目的地。最多允许五个地址。                                                                                                            |
| 301 Moved Permanently  | 所请求的页面已经转移至新的 url。                                                                                                                                                |
| 302 Found              | 所请求的页面已经临时转移至新的 url。                                                                                                                                            |
| 303 See Other          | 所请求的页面可在别的 url 下被找到。                                                                                                                                             |
| 304 Not Modified       | 未按预期修改文档。客户端有缓冲的文档并发出了一个条件性的请求（一般是提供 If-Modified-Since 头表示客户只想比指定日期更新的文档）。服务器告诉客户，原来缓冲的文档还可以继续使用。 |
| 305 Use Proxy          | 客户请求的文档应该通过 Location 头所指明的代理服务器提取。                                                                                                                      |
| 306 _Unused_           | 此代码被用于前一版本。目前已不再使用，但是代码依然被保留。                                                                                                                      |
| 307 Temporary Redirect | 被请求的页面已经临时移至新的 url。                                                                                                                                              |

### 4xx: 客户端错误

| 消息:                             | 描述:                                                                                                      |
| :-------------------------------- | :--------------------------------------------------------------------------------------------------------- |
| 400 Bad Request                   | 服务器未能理解请求。                                                                                       |
| 401 Unauthorized                  | 被请求的页面需要用户名和密码。                                                                             |
| 402 Payment Required              | 此代码尚无法使用。                                                                                         |
| 403 Forbidden                     | 对被请求页面的访问被禁止。                                                                                 |
| 404 Not Found                     | 服务器无法找到被请求的页面。                                                                               |
| 405 Method Not Allowed            | 请求中指定的方法不被允许。                                                                                 |
| 406 Not Acceptable                | 服务器生成的响应无法被客户端所接受。                                                                       |
| 407 Proxy Authentication Required | 用户必须首先使用代理服务器进行验证，这样请求才会被处理。                                                   |
| 408 Request Timeout               | 请求超出了服务器的等待时间。                                                                               |
| 409 Conflict                      | 由于冲突，请求无法被完成。                                                                                 |
| 410 Gone                          | 被请求的页面不可用。                                                                                       |
| 411 Length Required               | "Content-Length" 未被定义。如果无此内容，服务器不会接受请求。                                              |
| 412 Precondition Failed           | 请求中的前提条件被服务器评估为失败。                                                                       |
| 413 Request Entity Too Large      | 由于所请求的实体的太大，服务器不会接受请求。                                                               |
| 414 Request-url Too Long          | 由于 url 太长，服务器不会接受请求。当 post 请求被转换为带有很长的查询信息的 get 请求时，就会发生这种情况。 |
| 415 Unsupported Media Type        | 由于媒介类型不被支持，服务器不会接受请求。                                                                 |
| 416                               | 服务器不能满足客户在请求中指定的 Range 头。                                                                |
| 417 Expectation Failed            |                                                                                                            |

### 5xx: 服务器错误

| 消息:                          | 描述:                                              |
| :----------------------------- | :------------------------------------------------- |
| 500 Internal Server Error      | 请求未完成。服务器遇到不可预知的情况。             |
| 501 Not Implemented            | 请求未完成。服务器不支持所请求的功能。             |
| 502 Bad Gateway                | 请求未完成。服务器从上游服务器收到一个无效的响应。 |
| 503 Service Unavailable        | 请求未完成。服务器临时过载或当机。                 |
| 504 Gateway Timeout            | 网关超时。                                         |
| 505 HTTP Version Not Supported | 服务器不支持请求中指明的 HTTP 协议版本。           |

# 参考

1. [w3school JavaScript 参考手册](https://www.w3school.com.cn/jsref/index.asp)
2. [术语表](https://developer.mozilla.org/zh-CN/docs/Glossary)
3. [教程](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript)
4. [资料库](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference)
