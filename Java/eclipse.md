# eclipse

## 项目创建、导入

## eclipse 导入 dynamic web project

1. file-->import-->General -->Existing Projects into Workspace -->Browse-->选定你要导入的 JAVA 项目-->点击确定按钮
2. 右击导入的项目-->properties-->ProjectFacets
3. 勾选左侧 dynamic web module、java、JavaScript 与右侧 runtimes 中的 tomcat
4. apply --> ok

## 运行 dynamic web project

1. run as server
2. window -> show view ->servers
3. 双击 servers 中的 tomcat 服务器，解决端口冲突
4. `JRebel: ERROR Class 'org.apache.tomcat.util.buf.MessageBytes' could not be processed by org.zeroturnaround.javarebel.integration.catalina.cbp.MessageBytesCBP@java.net.URLClassLoader@67b64c45: org.zeroturnaround.bundled.javassist.CannotCompileException: [source error] no such field: hasStrValue` 去掉`enable jrebel agent`

## 项目启动

tomcat7:run

## 快捷键

window->preferences->general->keys（或直接搜索 keys），进入快捷键管理界面

### 快捷键管理

Ctrl + Shift + L 打开快捷键列表

### 内容辅助

Ctrl+1 快速修复（最经典的快捷键,就不用多说了，可以解决很多问题，比如 import 类、try catch 包围等）
Ctrl+Shift+F 格式化当前代码
Ctrl+Shift+M 添加类的 import 导入
Ctrl+Shift+O 组织类的 import 导入（既有 Ctrl+Shift+M 的作用，又可以帮你去除没用的导入，很有用）
Ctrl+Y 重做（与撤销 Ctrl+Z 相反）
Alt+/ 内容辅助（帮你省了多少次键盘敲打，太常用了）
Ctrl+D 删除当前行或者多行
Alt+↓ 当前行和下面一行交互位置（特别实用,可以省去先剪切,再粘贴了）
Alt+↑ 当前行和上面一行交互位置（同上）
Ctrl+Alt+↓ 复制当前行到下一行（复制增加）
Ctrl+Alt+↑ 复制当前行到上一行（复制增加）
Shift+Enter 在当前行的下一行插入空行（这时鼠标可以在当前行的任一位置,不一定是最后）
Ctrl+/ 注释当前行,再按则取消注释

### 选择

Alt+Shift+↑ 选择封装元素
Alt+Shift+← 选择上一个元素
Alt+Shift+→ 选择下一个元素
Shift+← 从光标处开始往左选择字符
Shift+→ 从光标处开始往右选择字符
Ctrl+Shift+← 选中光标左边的单词
Ctrl+Shift+→ 选中光标右边的单词

### 移动

Ctrl+← 光标移到左边单词的开头，相当于 vim 的 b
Ctrl+→ 光标移到右边单词的末尾，相当于 vim 的 e

### 搜索

Ctrl+F 搜索当前页
Ctrl+K 参照选中的 Word 快速定位到下一个（如果没有选中 word，则搜索上一次使用搜索的 word）
Ctrl+Shift+K 参照选中的 Word 快速定位到上一个
Ctrl+J 正向增量查找（按下 Ctrl+J 后,你所输入的每个字母编辑器都提供快速匹配定位到某个单词,如果没有,则在状态栏中显示没有找到了,查一个单词时,特别实用,要退出这个模式，按 escape 建）
Ctrl+Shift+J 反向增量查找（和上条相同,只不过是从后往前查）
Ctrl+Shift+U 列出所有包含字符串的行
Ctrl+H 打开搜索对话框
Ctrl+G 工作区中的声明
Ctrl+Shift+G 工作区中的引用

### 导航

Ctrl+Shift+T 搜索类（包括工程和关联的第三 jar 包）
Ctrl+Shift+R 搜索工程中的文件
Ctrl+E 快速显示当前 Editer 的下拉列表（如果当前页面没有显示的用黑体表示）
F4 打开类型层次结构
F3 跳转到声明处
Alt+← 前一个编辑的页面
Alt+→ 下一个编辑的页面（当然是针对上面那条来说了）
Ctrl+PageUp/PageDown 在编辑器中，切换已经打开的文件

### 调试

F5 单步跳入
F6 单步跳过
F7 单步返回
F8 继续
Ctrl+Shift+D 显示变量的值
Ctrl+Shift+B 在当前行设置或者去掉断点
Ctrl+R 运行至行(超好用，可以节省好多的断点)

### 重构

Alt+Shift+R 重命名方法名、属性或者变量名 （是我自己最爱用的一个了,尤其是变量和类的 Rename,比手工方法能节省很多劳动力）
Alt+Shift+M 把一段函数内的代码抽取成方法 （这是重构里面最常用的方法之一了,尤其是对一大堆泥团代码有用）
Alt+Shift+C 修改函数结构（比较实用,有 N 个函数调用了这个方法,修改一次搞定）
Alt+Shift+L 抽取本地变量（ 可以直接把一些魔法数字和字符串抽取成一个变量,尤其是多处调用的时候）
Alt+Shift+F 把 Class 中的 local 变量变为 field 变量 （比较实用的功能）
Alt+Shift+I 合并变量（可能这样说有点不妥 Inline）
Alt+Shift+V 移动函数和变量（不怎么常用）
Alt+Shift+Z 重构的后悔药（Undo）

### 其他

Alt+Enter 显示当前选择资源的属性，windows 下的查看文件的属性就是这个快捷键，通常用来查看文件在 windows 中的实际路径
Ctrl+↑ 文本编辑器 上滚行
Ctrl+↓ 文本编辑器 下滚行
Ctrl+M 最大化当前的 Edit 或 View （再按则反之）
Ctrl+O 快速显示 OutLine（不开 Outline 窗口的同学，这个快捷键是必不可少的）
Ctrl+T 快速显示当前类的继承结构
Ctrl+W 关闭当前 Editer（windows 下关闭打开的对话框也是这个，还有 qq、旺旺、浏览器等都是）
Ctrl+L 文本编辑器 转至行
F2 显示工具提示描述

## 调试

设置断点: 行前双击
启动: source 设置源码 --> debug 启动
设置 hit count 或 conditional 选择第几次循环时触发

## 反编译插件 decomplier

**install step:**

1. help -- Eclipse Marketplace..
2. 输入 Decompiler 搜索并安装此插件
3. 安装 enhanced Class Decomplier 3.0.0
4. 全选所有组件 => confirm => accept ... => finish
5. restart

**set:**

1. window -- Preferences -- java -- decomplier
2. 可设置发编译器类型
   1. 选择编译器 JD-CORE; 编译器打开文件报错,可能原因是本地 jre 版本与反编译器版本不一致导致
   2. 缺省类反编译器(Decompiler Settings)：
      重用缓存代码：只会反编译一次，以后每次打开该类文件，都显示的是缓存的反编译代码。
      忽略已存在的源代码：若未选中，则查看 Class 文件是否已绑定了 Java 源代码，如果已绑定，则显示 Java 源代码，如果未绑定，则反编译 Class 文件。若选中此项，则忽略已绑定的 Java 源代码，显示反编译结果。
      显示反编译器报告：显示反编译器反编译后生成的数据报告及异常信息。
      使用 Eclipse 代码格式化工具：使用 Eclipse 格式化工具对反编译结果重新格式化排版，反编译整个 Jar 包时，此操作会消耗一些时间。
      使用 Eclipse 成员排序：使用 Eclipse 成员排序对反编译结果重新格式化排版，反编译整个 Jar 包时，此操作会消耗大量时间。
      以注释方式输出原始行号信息：如果 Class 文件包含原始行号信息，则会将行号信息以注释的方式打印到反编译结果中。
      根据行号对齐源代码以便于调试：若选中该项，插件会采用 AST 工具分析反编译结果，并根据行号信息调整代码顺序，以便于 Debug 过程中的单步跟踪调试。
      设置类反编译查看器作为缺省的类文件编辑器：默认为选中，将忽略 Eclipse 自带的 Class Viewer，每次 Eclipse 启动后，默认使用本插件提供的类查看器打开 Class 文件。
3. 设置 class 文件默认打开方式: general -- editors -- file associations
   1. `*.class`
   2. `*.class without source`
   3. select `class decomplier viewer` as default

**查看及导出源码**

方法一：右键点中 类 || 接口 || 方法 名，选择 Open Declaration，即可进入源码。
方法二：右键点中 类 || 接口 || 方法 名，直接按 F3 键，即可进入源码。
方法三：常按住 Ctrl 键，然后点击 类 || 接口 || 方法 名，即可进入源码。（我比较喜欢这种操作方式）
进入源码后，在工具栏中会出现反编译器 点击可修改编译器类型 还可导出反编译代码

## 代码提示

1. eclipse→Windows→Preferences→Java→Editor→Content Assist，修改Auto Activation triggers for java的值为：zjava 
2. JavaScript→Editor→Content Assist，修改Auto Activation triggers for javaScript的值为：zjs
3. 继续选择web→html Files→Editor→Content Assist，修改Prompt when these characters are inserted:的值为： zhtml
4. 关闭打开File→Export→Genral→Preferences→按 browse,然后文件名随便取一个，生成一个后缀为.epf的文件，然后用记事本打开此文件 ，Ctrl+F查找 zjava 然后将其值改为 .abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ，再查找 zjs 然后将其值改为 .abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ，再查找 zhtml 然后将其值改为 <=.abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ，保存文件
5. 打开eclipse→File→Import→Genral→Preferences，导入刚刚编辑的文件后，所有设置完毕（导入后，会重启eclipse，之后可删除.epf文件


