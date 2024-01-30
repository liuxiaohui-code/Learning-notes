# vba

Excel 是一个应用程序（Application）
一个 Excel 文件称为工作薄（Workbook）
工作薄中的每张表都称为工作表（Worksheet）
工作表中的格子称为单元格（Cell），多个格子叫单元格区域（Range）

打开 Excel，依次找到`“文件”->“选项”->“自定义功能区”`，在右侧主选项卡下面的选项中，找到“`开发工具`”，在前面的`小框打勾`，`确定`。返回 Excel，即可在`菜单栏`右侧看到有`开发工具`.

VBE 即 VBA 的编程环境。通常有两种方式可以进入:`菜单栏 -> 开发工具 -> Visual Basic`或`快捷键: Alt + F11`

进入 VBE 后，在菜单栏依次选择`“插入”->“模块”`，然后光标会自动定位到代码窗口中，VBA 中的代码即在此编写。

1. 面对过程编程
2. 关键字 `Sub 过程名() ...  End Sub`;VBA 中允许使用中文作为过程名称
3. 注释 `'单行注释`
4. 包含有 VBA 代码的 Excel 文件不能再保存为.xls 或.xlsx 文件，应保存为`.xlsm`文件

## 变量

### 声明

VBA 中声明变量不是必须的,但是不建议

```vb
Option Explicit '强制变量声明，即在模块头部写上以下语句
Dim [变量名] As [数据类型]

Dim name As String '声明
name = "张三" '赋值
Range("A1") = name '调用
```

变量名规范:

1. 首字母必须以字母开头。
2. 不能包含空格、.(英文句号)、!(感叹号)、@、&、$、# 等字符。
3. 长度不能超过 255 个字符。
4. 不能使用 VBA 中保存的关键词作为变量名。

### 变量类型

#### 数字类型

```vb
Byte	字节	0 至 255
Integer	整数	-32,768 至 32,767
Long	长整数	-2,147,483,648 至 2,147,483,648
Single	单精度浮点数	在表示负数时： -3.402823E38 ~ -1.401298E-45
在表示正数时： 1.401298E-45 ~ 3.402823E38
Double	双精度浮点数	在表示负数时： -1.79769313486231E308 ~ -4.94065645841247E-324
在表示正数时： 4.94065645841247E-324 ~ 1.79769313486231E308
Currency	货币	-922,337,203,685,477.5808 至 922,337,203,685,477.5807
Decimal	定点数	未放置定点数： +/- 79,228,162,514,264,337,593,543,950,335
放置定点数： +/- 7.9228162514264337593543950335
```

#### 非数字类型

```vb
String	'文本类型	0 至 20亿字符
Boolean	'逻辑值	True 或 False
Date	'日期和时间	时间：00:00:00 至 23:59:59 日期： 100-1-1 至 9999-12-31
Object	'对象	VBA 和 Excel 对象

Dim birthday As Date
Dim time As Date

birthday = #2018-1-1#
birthday = 43101
birthday = "2018-1-1"

time = #12:00:00#
time = 0.5
time = "12:00:00"
```

#### 通用类型

通用数据类型，指的是可存储任何类型的数据。在程序运行过程，VBA 可以自动识别数据类型，参与计算。

```vb
Variant	任意类型	不限

Dim [变量名] As Variant
Dim [变量名]

```

Variant 类型虽然灵活，但是它会占用更多内存空间，执行效率也会受影响。因此建议，在明确知道数据是何种类型时，指定数据类型；如果数据类型是可变的或不明确，使用 Variant 类型。

### 常量

```vb
Const [常量名] As [数据类型] = [值]

'声明 π 常量
Const Pi As Double = 3.14159
'声明半径 r 和周长 C 变量
Dim r As Double
Dim C As Double
'从单元格 A1 读取半径值
r = Range("A1").Value
'计算周长
C = 2 * Pi * r

MsgBox "周长为：" & C
```

### 运算符

```vb
'赋值
=
'算数
+ - * /
Mod 取余
\ 整除
^ 幂运算
- 负数
'比较
= < <= > >= <>
'逻辑运算符
And
Or
Not :Not a -> False
Xor 异或 :a Xor b -> True
'字符串连接
&	连接两个文本	“Zhang” & ” ” & “San” -> “Zhang San”
'其他
_ (下划线)	将一行代码分解成两行
: ( 英文冒号)	将两行代码放置在一行
```

### String 工具函数

```vb
Format ' 格式化数据，并以文本类型返回
InStr ' 返回指定字符的位置
InStrRev ' 反方向返回指定字符位置
Left ' 返回左侧指定长度文本
Len ' 返回文本长度
LCase ' 大写字母转换成小写字母
LTrim ' 清除开头的空格
Mid ' 返回指定的开始和结束位置之间的文本
Replace ' 替换文本中的指定字符
Right ' 返回右侧指定长度文本
RTrim ' 清除末尾处的空格
Space ' 返回指定重复数的空格文本
StrComp ' 返回比较两个文本的结果
StrConv ' 将文本转换成指定格式
String ' 返回指定重复数的文本
StrReverse ' 逆转提供的字符串
Trim ' 清除开头和结尾处的文本
UCase ' 将小写字母转换成大写字母
```

## 数组

```vb
'固定长度数组声明
Dim [变量名](开始序号 to 结束序号) As [数据类型]
'动态数组声明
Dim [变量名]() As [数据类型]

'创建数组
Dim s(1 to 4) As String
'给数组的元素赋值
s(1) = "Excel"
s(2) = "Word"
s(3) = "PowerPoint"
s(4) = "Outlook"
```

## 对象

其实 Excel 本身是就是一个对象，是 Excel 中的最大的对象，使用 Application 表示。Application 对象又包含工作簿(Workbook)对象，工作簿(Workbook)对象又包含工作表(Worksheet)对象，而工作表(Worksheet)对象又包含其他的子对象。

```vb
'前期绑定声明语法
Dim [变量名] As [对象类型]
'后期绑定声明语法,后期绑定，即声明时不指定对象类型，后期指定。
Dim [变量名] As Object

'实例
Dim sh As Worksheet
Dim car As Object

' 对象类型变量赋值时，不同于基本类型变量使用Let（可以忽略）关键词，对象使用 Set 关键词，并且Set关键词不能省略。
Set [变量名] = [对象类型数据]
'声明工作表类型的对象
Dim sheet As Worksheet
'将名称为“绩效表”的工作表，赋到 sheet 变量
Set sheet = Worksheets("绩效表")
' With+对象和End With关键词中间,同时给多个属性赋值
With sheet
    .Name = "旧绩效"
    .Visible = False
End With
```

## 模块

模块是包含一个或多个过程或函数的内部组件。一个工作簿内包含的模块数量没有限制，一个模块内包含的过程或函数数量也没有限制。模块用来作为保存过程或函数的容器，这些过程和函数通常应用于整个工作簿。

通过把多个过程和函数，合理的放置在不同的模块，可以使整个 VBA 代码逻辑更清晰、更易于阅读和理解。

## 用户窗体

用户窗体是 VBA 代码与使用者交互的用户界面。Excel VBA 提供很多基本的窗体控件，可以制作复杂的用户界面。

最基本的窗体控件包括：

文本控件
按钮控件
列表控件
输入控件

## VBA 编辑器

VBA 编辑器是 Excel 中写 VBA 代码的地方。编辑器中可以进行下列操作：

编写代码
修改已有的代码
插入新的模块，编辑模块中的代码
插入用户窗体，设计窗体界面
运行代码
调试代码

`Visual Basic`：打开 VBA 编辑器，快捷键是 Alt + F11。
`宏`：打开宏列表，并且可以对列表中的宏进行编辑，例如运行、修改、删除等。这里的宏，就是我们在前一篇中说到的过程。
`录制宏`：将键盘和鼠标操作，自动转换成 VBA 代码。这个功能在实际的开发过程中非常有用。
`使用相对引用`：录制宏时的设置选项。
`宏安全性`：设置 Excel 如何对待包含 VBA 代码的工作簿。因为存在一些恶意的代码，所以一般将宏安全性设置为禁用。

首选，将光标放置在要运行的代码的任意一处，再使用快捷键 F5，即可运行代码。

### 录制宏

打开录制宏之后,将自动生成对 excel 的交互代码

### 运行宏

- 工具栏运行
- 开发工具->插入->按钮->指定宏
- 插入->形状->指定宏

## 打印输出

```vb
Debug.Print "Hello, World!" ' 在立即窗口打印 视图->立即窗口
MsgBox "Hello, World!" ' 输出到对话框
```

## 语句

顺序结构、循环结构、判断结构

### 分支

#### If ... Then ... End If

```vb
If isBlank Then
    Cells(i, 1) = Cells(i - 1, 1)
End If
```

#### If ... Then ... Else ... End If

```vb
If 条件表达式 Then
    '真时执行的代码
Else
    '假时执行的代码
End If
```

#### If ... Then ... ElseIf ... Else ... End If

```vb
If 条件表达式1 Then
    '表达式1真时，执行的代码
ElseIf 条件表达式2 Then
    '表达式2真时，执行的代码
ElseIf 条件表达式3 Then
    '表达式3真时，执行的代码
    ...
ElseIf 条件表达式n Then
    '表达式n真时，执行的代码
Else
    '以上表达式都不为真时，执行的代码
End If
```

#### select...case

相对于 If ElseIf Else 结构，它把条件表达式中的变量提取出来，使得代码结构更简洁，也更易于阅读。

```vb
Select Case 变量
  Case 判断条件 1
  '条件 1 真时，执行的代码
  Case 判断条件 2
  '条件 2 真时，执行的代码
  Case 判断条件 3
  '条件 3 真时，执行的代码
  Case Else
  '之前的所有条件都不为真时，执行的代码
End Select

Select Case Cells(i, "B").Value
    Case Is >= 85
        Cells(i, "D") = "优"
    Case Is >= 75
        Cells(i, "D") = "良"
    Case Is >= 60
        Cells(i, "D") = "及格"
    Case Else
        Cells(i, "D") = "不及格"
End Select
```

### 循环

#### For … Next

```vb
'[步长] 是每次循环时，变量的增量。如果为正值，变量增大；如果为负值，变量减小。'
For [变量] = [初始值] To [结束值] Step [步长]
    '这里是循环执行的语句
Next

'For 循环的 Step 值如果是 1，则 Step 关键词可省略
For i = 2 To 10

Next i
```

#### For Each

```vb
For Each [元素] In [元素集合]
    '循环执行的代码
Next [元素]

Dim sh As Worksheet
For Each sh In Worksheets
    Debug.Print sh.Name
Next sh
```

#### Exit For

Exit For 语句用于跳出循环过程，一般在提前结束循环时使用，均适用于 For Next 循环和 For Each 循环

```vb
Dim i As Integer
Dim sum As Integer

For i = 1 To 10

    sum = sum + i

    If sum > 30 Then
        Exit For
    End If

Next
```

#### Do While … Loop

```vb
Do While [条件表达式]
    '循环执行的代码
Loop

i = 1
Do While i <= 10
    sum = sum + i
    i = i + 1
Loop
```

#### Do … Loop While

```vb
Do
    '循环执行的代码
Loop While [条件表达式]
```

#### Exit Do

与 Exit For 语句类似，Exit Do 语句用于跳出 Do While 循环。

```vb
'循环开始
For i = 2 To 10
  '这里是循环的代码
Next i
```

#### Do Until … Loop

循环开始前判断 Until 后条件表达式的值，如果是真，停止循环；如果是假，继续执行循环。

```vb
Do Until [条件表达式]
    '循环执行的代码
Loop
```

#### Do … Loop Until

```vb
Do
    '循环执行的代码
Loop Until [条件表达式]
```

### With 对象属性赋值语法糖

```vb
With [对象]
    .[属性] = [数据]
    .[方法]
    '其他属性和方法
End With

Worksheets("Sheet1").Name = "新名称"
Worksheets("新名称").Tab.ThemeColor = xlThemeColorLight1
Worksheets("新名称").Visible = xlSheetHidden

With Worksheets("Sheet1")
    .Name = "新名称"
    .Tab.ThemeColor = xlThemeColorLight1
    .Visible = xlSheetHidden
End With
```

With 结构还能嵌套编写，即一个 With 结构中，如果父对象的属性是另一个对象，则针对这个子对象，继续使用 With 结构。

```vb
With Worksheets("Sheet1")
    .Name = "新名称"
    .Tab.ThemeColor = xlThemeColorLight1
    .Visible = xlSheetHidden
    '将 Sheet1 工作表中，A1:A10 单元格区域设置背景颜色，调整字体和字体大小
    With .Range("A1:A10")
        .Interior.ThemeColor = xlThemeColorAccent1
        .Font.Size = 12
        .Font.Name = "等线"
    End With

End With
```

### GoTo

跳转的位置由 Goto 关键词后的 [标签] 告诉程序，VBA 会在代码中查找对应的 [标签]: 关键词，从标签下一行继续执行程序。

需要注意的是，跳转处的标签，后接冒号 ( : ) 。

```vb
GoTo [标签]
'被跳过的代码
...
[标签]:
'被执行的代码

'使用 VBA 作除法，如果除数是零，则跳转到程序末尾，提示除数不符合规范
Sub MyCode()
  Dim num1 As Double
  Dim num2 As Double
  Dim result As Double
  num1 = 100
  num2 = 0
  If num2 = 0 Then GoTo error
  result = num1 / num2
  Exit Sub
error:
  MsgBox "除数不能为零"
End Sub
```

## 过程

### 声明与定义

过程是 VBA 中，程序实际运行的最小结构。单独的一行或多行代码无法运行，必须把它们放置在一个过程里，才能运行。

```vb
'以 Sub 语句开始，以 End Sub 语句结束'
Sub sub_name()
  '中间这里是我们要实现各种操作的VBA代码
End Sub
'调用无参数过程
sub_name
'调用多个参数'
sub_name agr1,arg2,arg3
CustomLog 100, 10 '方式一
CustomLog num:=100, base:=10 '方式二
CustomLog base:=10, num:=100 '方式三
```

### 可选参数

- 可选参数定以后，如果在子过程中使用，需要判断参数有无提供。否则未提供而直接使用时，程序会出错
- 当子过程有多个参数时，其中的可选参数需写在参数列表的末尾，否则 VBA 提示错误。

```vb
Optional [参数名] As [数据类型]
Optional [参数名] As [数据类型] = [默认值]

'声明一个带可选参数的子过程
Sub CustomLog(num As Double, Optional base As Integer)
    '子过程代码
End Sub
```

### Call 调用子过程

```vb
'无参数
Call sub_name
Call sub_name()
'多个参数
Call sub_name(a,b,c)
```

### End 提前结束所有程序

```vb
Sub SayHello(name As String)
  ...
  End
  ...
End Sub
```

## 函数

### 声明定义

```vb
Function [函数名]() As [返回值类型]
    语句1
    语句2
    ...
    语句n
    [函数名] = [返回值]
End Function

'声明函数，该函数随机返回 true 或 false。函数需指定返回值类型。
Function RandomLogic() As Boolean
    RandomLogic = Rnd() > 0.5
End Function

'有参数函数
Function [函数名]([变量名1] As [数据类型1],...[变量名n] As [数据类型n]) As [返回值类型]
    语句1
    语句2
    ...
    语句3
    [函数名] = [返回值]
End Function
Function Add2Number(num1 As Double, num2 As Double) As Double
    Add2Number = num1 + num2
End Function
```

### 调用

函数与子过程的区别是，函数可以返回值。如果一个函数不返回值，它与子过程并无区别，其中调用方式与子过程相同。

无参数函数直接书写，有参数函数将参数放在括号内。

```vb
Sub Main()
    '使用变量存储函数返回的值
    Dim result1 As Double
    result1 = Add(12, 345)

    '函数返回值继续参与计算
    Dim result2 As Double
    result2 = RandNum + Add(12, 345)
End Sub

'函数：返回一个随机值
Function RandNum()
    RandNum = Rnd * 100
End Function
'函数：返回两数的和
Function Add(num1 As Double, num2 As Double) As Double
    Add = num1 + num2
End Function
```

### Exit Function 提前退出函数

在一个函数中，当程序运行到 Exit Function 语句时，立即结束当前函数，提前退出。

这里需要注意的是，Exit Function 语句只作用于当前过程，不影响调用它的父过程或函数。

### 函数可在单元格内公式中使用

与 Excel 内置的函数一样，用户自定义编写的函数可在公式中直接使用，其用法与内置函数一样。

## 参数传递的值传递与引用传递

ByVal：传递参数的值

ByRef：传递参数的引用

默认情况下，当省略传递类型时，默认值是 ByRef

```vb
'在定义过程或函数时，如果需要传递变量，则每个参数需要指定传递类型。
'传递类型有 2 种，分别是 ByVal 和 ByRef 。

'ByVal 传递参数的值
Sub TestSub1(ByVal msg As String)
End Sub

'ByRef 传递参数的引用
Sub TestSub2(ByRef msg As String)
End Sub
```

## 作用域

- 在过程或函数内部声明的变量，只有在当前过程或函数内被使用
- 一个模块中，在任何一个过程和函数外面，使用关键词 `Private` 或 `Dim` 声明的变量，称之为模块变量，其作用域是当前模块。
- Excel VBA 中，一个 Excel 工作簿是一个 VBA 工程。与之对应，工程作用域表示变量在当前工程中的模块、Excel 对象、用户窗体、类模块中均可以被使用
- 当相同名称的变量，多次以不同的作用域声明时，出现作用域冲突。这种情况，VBA 会自动以就近原则使用变量，即优先使用最近定义的变量。

```vb
'工程级别变量，在所在模块顶部声明 Option Private Module 修饰语句前提下，在过程或函数外面，使用关键词 Public 声明的变量，其作用域是当前工程
'工程作用域'
Option Private Module
Public guest As String
'模块作用域'
Dim msg As String
Sub Test()
    '过程作用域'
    Dim message As String
    guest = "张三"
    message = "你好"
    MsgBox message & "！ " & guest

End Sub
```

- 在模块中，使用 `Private` 关键词声明的过程或函数，具备模块作用域，只能在当前**模块**中使用。
- 在模块中，顶部声明 `Option Private Module` 修饰语句，并且直接声明或使用 Public 关键词声明的过程或函数，具备工程作用域，在当前**工程**的所有模块中使用。
- 在模块中，直接声明或使用 `Public` 关键词声明的过程或函数，具备全局作用域。

## 参考

[api 参考手册](https://learn.microsoft.com/zh-cn/office/vba/api/overview/)

```vb
Application '对象，表示 Excel 应用程序。
Workbook '对象，表示工作簿对象。
Worksheet '对象，表示工作表对象
Range '对象，表示单元格区域对象。

Cells(x long,y long) Range 返回第x行第y列的单元格对象
```

### 文件

#### Dir 获取|遍历文件名

```vb
filename = Dir(文件夹路径) '获取文件夹下的下一个文件的文件名,含后坠'
F = Dir("F:\userdata\Desktop\新建文件夹\*.xls") '扫描所有后缀为.xls的文件。
F = Dir("F:\userdata\Desktop\新建文件夹\123.xls") '如果文件123.xls存在，则返回字符串123.xls，如果不存在，则返回空字符串。

'一般情况下，Dir函数只返回文件名，而不返回子文件夹名。如果想要两者都返回，则需要加上vbDirectory参数。
F = Dir("F:\userdata\Desktop\新建文件夹\" , vbDirectory)'特别要注意的是，子文件夹包括“.”和“..”两个特殊名字，分别代表本目录F:\userdata\Desktop\新建文件夹\及其父目录F:\userdata\Desktop\。
'Dir函数只能返回第一层的子文件夹和文件名，子文件夹下的文件与文件夹不返回。'
```

**扫描一个文件夹下所有文件的通用模板**

```vb
Dim filename as string
filename = Dir("F:\userdata\Desktop\新建文件夹\")  '可以更改为任意文件夹
Do while filename <> ""
	相关操作
	filename = Dir  '获取下一个文件名
Loop
```

#### 路径属性

```vb
path = ThisWorkbook.Path 'String 当前工作簿的绝对路径'
```

### Workbook 工作簿对象与 Workbooks 工作簿集合对象

#### 引用

```vb
'利用索引号引用工作簿
Workbook.Item(3)
'Item可以省略
Workbook(3)
'利用工作簿名称引用
Workbook("Book1")
'如果本地文件显示拓展名（且文件已经保存），则文件名必须带拓展名
Workbook("Book1.xls")
```

#### 获取属性

```vb
Range("B2") = ThisWorkbook.Name     '返回当前工作簿名称 练习 -副本.xlsm
Range("B3") = ThisWorkbook.Path     '返回当前工作簿路径 C:\Users\ThinkPad\Desktop
Range("B4") = ThisWorkbook.FullName '返回当期工作簿带名称的路径 C:\Users\ThinkPad\Desktop\练习 - 副本.xlsm

```

#### 创建

```vb
'如果不带任何参数，将创建包含一定数目空白工作表的新工作簿（数目由SheetsInNewWorkbook属性决定）
Workbooks.Add
'参数表示现有Excel名称的字符串，选用该参数，新建的工作簿将以该文件作为模板
Workbooks.Add "C:\Program Files\Microsoft Office\Templates\2052\ADDRESS\ADDRESS.XLS"
'通过参数指定新建工作簿中包含的工作类型
' xlWBATWorksheet 默认,工作表
' xlWBATChart 图表
' xlWBATExcel4MacroSheet 宏表
' xlWBATExcel4IntlMacroSheet 对话框
Workbooks.Add xlWBATChart '新建图表工作表
```

#### 打开工作簿

```vb
Workbooks.Open "file_path" '打开一个工作簿文件,返回一个Workbook对象'
Workbooks.Open "F:\Book1.xls"
```

#### 激活

打开多个工作簿，但是同一时间只能有一个窗口是活动的

保存工作簿调用 Workbooks 的 Save 方法

```vb
Workbooks("Book1.xls").Activate '激活工作簿
```

#### 保存

```vb
ThisWorkbook.Save   '保存代码所在的工作簿
ThisWorkbook.SaveAs Filename:="D:\test.xls" '另存为
'使用SaveAs方法将工作簿另存为新文件后，将自动关闭原文件，打开新文件，
'如果希望继续保留原文件不打开新文件，可以用SaveCopyAs方法'
ThisWorkbook.SaveCopyAs Filename:="D:\test.xls"
```

#### 关闭

```vb
Workbooks.Close '关闭所有打开的工作簿
Workbooks("Book1.xls").Close  '关闭Book1.xls
Workbooks("Book1.xls").Close savechanges:=True '关闭并保存Book1.xls
Workbooks("Book1.xls").Close True '关闭Book1.xls,savechanges可以神略
```

#### ThisWorkbook 与 ActiveWorkbook

同是 Application 对象的属性，同是返回 Workbook 对象，但二者并不是等同的。

ThisWorkbook 是对程序所在的工作簿的引用

ActiveWorkbook 是对活动工作簿的引用

新建的工作簿总会成为活动工作簿

```vb

```

### Workbooks 工作簿集合对象

### Worksheets 对象

```vb
' 创建新的工作表、图表或宏工作表。 新工作表成为活动工作表
' 在那个工作表之前,之后,几个(1),类型(xlWorksheet)
Worksheet = Worksheets.Add Before,After,Count,Type
Worksheets.Add after:=Worksheets("首页")
```

### Application 对象（Excel 顶层对象）

```vb
Application.ScreenUpdating = False '关闭屏幕更新,将看不到程序的执行过程
Application.DisplayAlerts = False '不显示警告信息
Application.EnableEvents = True '启用事件
'使用WorksheetFunction调用Excel内置函数
'统计A1:A50单元格中数值大于1000的单元格有多少个？使用COUNTIF函数
mycount = Application.WorksheetFunction.CountIf(Range("A1:B50"), ">1000")
```

## 例子

```vb
'将列数字转换为字母
Function CNtoW(ByVal num As Long) As String
    CNtoW = Replace(Cells(1, num).Address(False, False), "1", "")
End Function
Sub x()
    Dim col As String
    col = CNtoW("1")
    Debug.Print col
    Debug.Print Cells(1, 1).Address(False, False)
End Sub

'将列字母转换为数字
Function CWtoN(ByVal AB As String) As Long
    CWtoN = Range("a1:" & AB & "1").Cells.Count
End Function
'检查工作铺是否存在
Function CheckIsExistsSheetName(ByVal SheetName) As Boolean
    CheckIsExistsSheetName = False
    Dim sheet As Worksheet
    For Each sheet In ThisWorkbook.Sheets
        If sheet.Name = SheetName Then
            CheckIsExistsSheetName = True
            Exit Function
        End If
    Next
End Function
'合并工作表
Sub combine()
    Dim fileName As String

    Dim fileDir As String '存放excel数据的文件夹
    Dim startCell As String '数据开始列 字母
    Dim startRow As Integer '数据开始行
    Dim rowNum As Long, maxRow As Long
    Dim value As Long
    configSheet = "开始页"
    NewSheet = "合并数据"
    Dim dataSheet As Integer
    dataSheet = Sheets(configSheet).Range("B3")
    fileDir = Sheets(configSheet).Range("B2")
    startRow = Sheets(configSheet).Range("B4")
    startCell = Sheets(configSheet).Range("B5")
    invaildRow = Sheets(configSheet).Range("B9")

    Dim copyCellTitle As Boolean '是否复制列标题
    copyCellTitle = Sheets(configSheet).Range("B6")
    Dim copyRowTitle As Boolean '是否复制行标题
    copyRowTitle = Sheets(configSheet).Range("B7")

    If copyRowTitle = True And CWtoN(startCell) > 1 Then '如果有效数据前一列是列标题的话,将列标题所在的列纳入有效数据
        startCell = "A"
    End If
    '合并数据页是否存在,不存在则创建;并把当前页切换为新建页
    If CheckIsExistsSheetName(NewSheet) = False Then
        Worksheets.Add after:=Worksheets(configSheet) '开始页之后创建一个新的工作簿,并选中
        ActiveSheet.Name = NewSheet '给当前生成的sheet页命名
    End If

    maxRowOri = ActiveSheet.Cells.SpecialCells(xlCellTypeLastCell).Row + 1 '选定已用区域的最后一个单元格的行数+1
    maxRow = maxRowOri

    fileName = Dir(ThisWorkbook.Path & "/" & fileDir & "/")
    Dim fileNum As Long
    While fileName <> ""
        fileNum = fileNum + 1
        fileName = Dir '遍历下一个文件
    Wend

    fileName = Dir(ThisWorkbook.Path & "/" & fileDir & "/") '获取 vba 根目录下子目录fileDir的第一个文件
    Dim i As Long
    'Application.DisplayAlerts = False
    While fileName <> ""
        i = i + 1
        Workbooks.Open ThisWorkbook.Path & "/" & fileDir & "/" & fileName '打开对应的excel文件
        rowNum = Range("A1").CurrentRegion.Rows.Count '共有几行数据
        If i <> fileNum Then
            rowNum = rowNum - invaildRow '删除填报数据下的无效数据
        End If
        cellNum = ActiveSheet.Cells.SpecialCells(xlCellTypeLastCell).Column '共有几列有效数据
        '复制列标题
        If copyCellTitle = True And startRow > 1 Then
            Workbooks(fileName).Activate '激活工作表
            Sheets(dataSheet).Select '选中工作表
            ActiveSheet.Range(startCell & 1, Cells(startRow - 1, cellNum)).Select '选中区域 第1行开始列 到 第(开始行-1)行第有效列,即选中标题列,仅限标题只用一行
            '解决单元格合并标题区域变化问题
            titlestartrow = 1
            While Selection.Columns.Count > cellNum - CWtoN(startCell) + 1 And titlestartrow < startRow '选中的列数大于(有效列-开始列+1)且开始行大于1
                titlestartrow = titlestartrow + 1
                ActiveSheet.Range(startCell & titlestartrow, Cells(startRow - 1, cellNum)).Select '选中区域再向下选中一行
            Wend
            lcellnum = cellNum
            While Selection.Rows.Count > startRow - 1 And lcellnum > 1 '选中区域的行数大于(开始行-1) 且 有效列数大于1
                lcellnum = lcellnum - 1 '列数-1
                ActiveSheet.Range(startCell & titlestartrow, Cells(startRow - 1, lcellnum)).Select '选中区域右侧缩小一列
            Wend

            Selection.Copy '复制选中区域
            ThisWorkbook.Activate '激活vba所在工作簿
            Sheets(NewSheet).Select '选中新建的"合并数据"工作表
            Range(startCell & maxRow).Select '选中开始列第一行
            ActiveSheet.Paste '粘贴
            maxRow = ActiveSheet.Cells.SpecialCells(xlCellTypeLastCell).Row + 1 'maxRow为已有数据行的下一行
            copyCellTitle = False '标题只复制一次
        End If

        Workbooks(fileName).Activate '激活数据表
        Sheets(dataSheet).Select '选中数据页
        ActiveSheet.Range(startCell & startRow, CNtoW(cellNum) & (rowNum)).Select '选中(开始行,开始列)--(有效数据的结束行和结束列)
        lrowNum = rowNum
        While Selection.Columns.Count > cellNum - CWtoN(startCell) + 1 And lrowNum > 1 '选中列数大于(有效列数-开始列+1) 且 有效列大于1
                lrowNum = lrowNum - 1
                ActiveSheet.Range(startCell & startRow, CNtoW(cellNum) & (lrowNum)).Select
        Wend

        Selection.Copy ' 复制有效数据
        ThisWorkbook.Activate
        Sheets(NewSheet).Select
        maxRow = ActiveSheet.Cells.SpecialCells(xlCellTypeLastCell).Row + 1 'maxRow为已有数据行的下一行
        Range(startCell & maxRow, startCell & maxRow + rowNum - startRow).Select 'maxRow,开始列--选中数据的行数+maxrow,开始列,
        ActiveSheet.Paste
        Application.CutCopyMode = False '取消剪切或复制模式并清除移动边框
        Workbooks(fileName).Close False '关闭工作簿
        fileName = Dir '遍历下一个文件
    Wend
    'Application.DisplayAlerts = True
End Sub

'汇总有效数据
Sub statistic()
    Dim fileName As String
    Dim fileDir As String
    Dim startCell As String
    Dim startRow As Integer
    Dim rowNum As Long, maxRow As Long
    Dim i As Long

    configSheet = "开始页"
    NewSheet = "汇总数据"
    Dim dataSheet As Integer
    dataSheet = Sheets(configSheet).Range("B3")
    fileDir = Sheets(configSheet).Range("B2")
    startRow = Sheets(configSheet).Range("B4")
    startCell = Sheets(configSheet).Range("B5")
    Dim copyCellTitle As Boolean
    copyCellTitle = Sheets(configSheet).Range("B6")
    Dim copyRowTitle As Boolean
    copyRowTitle = Sheets(configSheet).Range("B7")

    If CheckIsExistsSheetName(NewSheet) = False Then
        Worksheets.Add after:=Worksheets(configSheet)
        ActiveSheet.Name = NewSheet '给当前生成的sheet页命名
    End If
    Sheets(NewSheet).Select
    maxRowOri = ActiveSheet.Cells.SpecialCells(xlCellTypeLastCell).Row + 1
    maxRow = maxRowOri

    If copyRowTitle = True And CWtoN(startCell) > 1 Then
        startlCell = "A"
    Else
        startlCell = startCell
    End If


    fileName = Dir(ThisWorkbook.Path & "/" & fileDir & "/")
    'Application.DisplayAlerts = False
    While fileName <> ""
        Workbooks.Open ThisWorkbook.Path & "/" & fileDir & "/" & fileName
        rowNum = Range("A1").CurrentRegion.Rows.Count
        cellNum = ActiveSheet.Cells.SpecialCells(xlCellTypeLastCell).Column
        '复制列标题
        If copyCellTitle = True And startRow > 1 Then
            Workbooks(fileName).Activate
            Sheets(dataSheet).Select
            ActiveSheet.Range(startlCell & 1, Cells(startRow - 1, cellNum)).Select

            titlestartrow = 1
            While Selection.Columns.Count > cellNum - CWtoN(startlCell) + 1 And titlestartrow < startRow
                titlestartrow = titlestartrow + 1
                ActiveSheet.Range(startlCell & titlestartrow, Cells(startRow - 1, cellNum)).Select
            Wend
            lcellnum = cellNum
            While Selection.Rows.Count > startRow - 1 And lcellnum > 1
                lcellnum = lcellnum - 1
                ActiveSheet.Range(startlCell & titlestartrow, Cells(startRow - 1, lcellnum)).Select
            Wend

            Selection.Copy
            ThisWorkbook.Activate
            Sheets(NewSheet).Select
            Range(startlCell & maxRow).Select
            ActiveSheet.Paste
            maxRow = ActiveSheet.Cells.SpecialCells(xlCellTypeLastCell).Row + 1
            copyCellTitle = False
        End If
        '复制行标题
        If copyRowTitle = True And CWtoN(startCell) > 1 Then
            Workbooks(fileName).Activate
            Sheets(dataSheet).Select
            ActiveSheet.Range("A1", Cells(rowNum, CWtoN(startCell) - 1)).Select
            copyRow = maxRowOri
            If Selection.Columns.Count > CWtoN(startCell) - 1 Then
                ActiveSheet.Range("A" & startRow & ":" & CNtoW(CWtoN(startCell) - 1) & (rowNum)).Select
                copyRow = maxRowOri + startRow - 1
            End If

            While Selection.Columns.Count > CWtoN(startCell) - 1 And rowNum > 0
                rowNum = rowNum - 1
                ActiveSheet.Range("A" & startRow & ":" & CNtoW(CWtoN(startCell) - 1) & (rowNum)).Select
            Wend

            Selection.Copy
            ThisWorkbook.Activate
            Sheets(NewSheet).Select
            Range("A" & copyRow).Select
            ActiveSheet.Paste
            copyRowTitle = False
        End If

        Workbooks(fileName).Activate
        Sheets(dataSheet).Select
        ActiveSheet.Range(startCell & startRow & ":" & CNtoW(cellNum) & rowNum).Select
        lrowNum = rowNum
        While Selection.Columns.Count > cellNum - CWtoN(startCell) + 1 And lrowNum > 1
                lrowNum = lrowNum - 1
                ActiveSheet.Range(startCell & startRow, CNtoW(cellNum) & (lrowNum)).Select
        Wend
        For Each Rng In Selection
            Row1 = Rng.Row
            Column1 = Rng.Column
            value = Rng.value
            If IsEmpty(value) = False Then
                ThisWorkbook.Activate
                If IsEmpty(Sheets(NewSheet).Range(CNtoW(Rng.Column) & maxRow + Rng.Row - startRow)) Then
                    Sheets(NewSheet).Range(CNtoW(Rng.Column) & maxRow + Rng.Row - startRow) = value
                Else
                    Sheets(NewSheet).Range(CNtoW(Rng.Column) & maxRow + Rng.Row - startRow) = Sheets(NewSheet).Range(CNtoW(Rng.Column) & maxRow + Rng.Row - startRow) + value
                End If
            End If
        Next


        Application.CutCopyMode = False
        Workbooks(fileName).Close False
        fileName = Dir
    Wend
    'Application.DisplayAlerts = True
End Sub

Sub appointStatistic()
Dim fileName As String
    Dim fileDir As String
    Dim startCell As String
    Dim startRow As Integer
    Dim rowNum As Long, maxRow As Long


    Dim appointStr As String

    configSheet = "开始页"
    NewSheet = "汇总指定数据"
    Dim dataSheet As Integer
    dataSheet = Sheets(configSheet).Range("B3")
    fileDir = Sheets(configSheet).Range("B2")
    startRow = Sheets(configSheet).Range("B4")
    startCell = Sheets(configSheet).Range("B5")
    appointStr = Sheets(configSheet).Range("B8")
    Dim copyCellTitle As Boolean
    copyCellTitle = Sheets(configSheet).Range("B6")
    Dim copyRowTitle As Boolean
    copyRowTitle = Sheets(configSheet).Range("B7")


    If CheckIsExistsSheetName(NewSheet) = False Then
        Worksheets.Add after:=Worksheets(configSheet)
        ActiveSheet.Name = NewSheet '给当前生成的sheet页命名
    End If
    Sheets(NewSheet).Select
    maxRowOri = ActiveSheet.Cells.SpecialCells(xlCellTypeLastCell).Row + 1
    maxRow = maxRowOri

    fileName = Dir(ThisWorkbook.Path & "/" & fileDir & "/")

    If copyRowTitle = True And CWtoN(startCell) > 1 Then
        startlCell = "A"
    Else
        startlCell = startCell
    End If

    While fileName <> ""
        Workbooks.Open ThisWorkbook.Path & "/" & fileDir & "/" & fileName
        Workbooks(fileName).Activate
        rowNum = Range("A1").CurrentRegion.Rows.Count
        cellNum = ActiveSheet.Cells.SpecialCells(xlCellTypeLastCell).Column
        '复制列标题
        If copyCellTitle = True And startRow > 1 Then
            Workbooks(fileName).Activate
            Sheets(dataSheet).Select
            ActiveSheet.Range(startlCell & 1, Cells(startRow - 1, cellNum)).Select

            titlestartrow = 1
            While Selection.Columns.Count > cellNum - CWtoN(startlCell) + 1 And titlestartrow < startRow
                titlestartrow = titlestartrow + 1
                ActiveSheet.Range(startlCell & titlestartrow, Cells(startRow - 1, cellNum)).Select
            Wend
            lcellnum = cellNum
            While Selection.Rows.Count > startRow - 1 And lcellnum > 1
                lcellnum = lcellnum - 1
                ActiveSheet.Range(startlCell & titlestartrow, Cells(startRow - 1, lcellnum)).Select
            Wend

            Selection.Copy
            ThisWorkbook.Activate
            Sheets(NewSheet).Select
            Range(startlCell & maxRow).Select
            ActiveSheet.Paste
            maxRow = ActiveSheet.Cells.SpecialCells(xlCellTypeLastCell).Row + 1
            copyCellTitle = False
        End If
        '复制行标题
        If copyRowTitle = True And CWtoN(startCell) > 1 Then
            Workbooks(fileName).Activate
            Sheets(dataSheet).Select
            ActiveSheet.Range("A1", Cells(rowNum, CWtoN(startCell) - 1)).Select
            copyRow = maxRowOri
            If Selection.Columns.Count > CWtoN(startCell) - 1 Then
                ActiveSheet.Range("A" & startRow & ":" & CNtoW(CWtoN(startCell) - 1) & (rowNum)).Select
                copyRow = maxRowOri + startRow - 1
            End If

            While Selection.Columns.Count > CWtoN(startCell) - 1 And rowNum > 0
                rowNum = rowNum - 1
                ActiveSheet.Range("A" & startRow & ":" & CNtoW(CWtoN(startCell) - 1) & (rowNum)).Select
            Wend

            Selection.Copy
            ThisWorkbook.Activate
            Sheets(NewSheet).Select
            Range("A" & copyRow).Select
            ActiveSheet.Paste
            copyRowTitle = False
        End If
        Workbooks(fileName).Activate
        Sheets(dataSheet).Select
        ActiveSheet.Range(appointStr).Select
        For Each Rng In Selection
            Row1 = Rng.Row
            Column1 = Rng.Column
            value = Rng.value
            If IsEmpty(value) = False Then
                ThisWorkbook.Activate
                If IsEmpty(Sheets(NewSheet).Range(CNtoW(Rng.Column) & maxRow + Rng.Row - startRow)) Then
                    Sheets(NewSheet).Range(CNtoW(Rng.Column) & maxRow + Rng.Row - startRow) = value
                Else
                    Sheets(NewSheet).Range(CNtoW(Rng.Column) & maxRow + Rng.Row - startRow) = Sheets(NewSheet).Range(CNtoW(Rng.Column) & maxRow + Rng.Row - startRow) + value
                End If
            End If
        Next

        Application.CutCopyMode = False
        Workbooks(fileName).Close False
        fileName = Dir
    Wend

End Sub


```
