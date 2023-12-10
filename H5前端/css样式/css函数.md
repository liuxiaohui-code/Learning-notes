# 函数

```css
attr(属性)	/**返回所选元素的属性值。*/
calc()	/**允许您执行计算来确定 CSS 属性值。*/
var(var_name, value)	/**插入自定义属性的值,value 未找到变量时的缺省值*/

a:after {
  content: " (" attr(href) ")";
} /**在 a 标签后插入标签的内容 */

/**定义全局变量 */
:root {
  /* 一般在根元素上定义全局变量,必须以两个破折号（--）开头，且区分大小写！*/
  --blue: #1e90ff;
  --white: #ffffff;
}
body {
  background-color: var(--blue);
}

* {
  height: calc(100vh - 50px);
  /*
	1. 运算符与长度之间必须用空格隔开
	2. 单位可以不一致
	3. 运算符 + - * / ;
	*/
}
```
# 计数器

CSS 计数器就像“变量”。变量值可以通过 CSS 规则递增（将跟踪它们的使用次数）。常用与目录编号

```css
/** 定义一个名字为id的递增计数器,默认增量是 number,匹配的选择器从上到下计数*/
counter-increment: id number;
/** 属性设置某个选择器出现次数的计数器的值。默认为 0。*/
counter-reset: id number;
/**使用counter()函数获取计数器计数 */
counter(name)
```

eg

```css
body {
  counter-reset: section;
}

h2::before {
  counter-increment: section;
  content: "Section " counter(section) ": ";
}
```