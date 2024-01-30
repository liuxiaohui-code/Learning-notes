# 选择器


```css
选择器 {
  属性: 值;
  ...;
}
```

1. 元素选择器:`标签名{}`,选择页面上的所有此标签
2. id 选择器:`#id{}`,选择 id 为 id 的元素,id 名称不能以数字开头
3. 自定义类名选择器:`.class_name{}`,选择元素属性 class 中具有 class_name 的属性
4. 组合选择器:
   1. `选择器1选择器2{}`,多个选择器并列,表示必须同时满足多个选择条件的元素
   2. `选择器1,选择器2{}`,满足任意选择器的元素
5. 伪类选择器:`选择器:伪类{}`,伪类用于定义元素的特殊状态.
6. 伪元素选择器:`选择器::伪元素{}`,伪元素用于设置元素指定部分的样式
   1. `::after` 在每个元素之后插入内容。
   2. `::before` 在每个元素之前插入内容。
   3. `::first-letter` 选择每个元素的首字母。 块级元素
   4. `::first-line` 块级元素,用于向文本的首行添加特殊样式。
   5. `::selection` 选择用户选择的元素部分。
   6. `content` 属性与 :before 及 :after 伪元素配合使用，来插入生成内容。
7. 通用选择器:`*{}`,匹配所有元素
8. 属性选择器:`[属性]`,选择带有该属性的所有元素
   1. `[属性=值]`,属性为值
   2. `[属性~=值]`,属性值中包含指定单词
   3. `[属性^=值]`,属性值以指定值开头
   4. `[属性$=值]`,属性值以指定值结尾
   5. `[属性*=值]`,属性值包含指定值
   6. `[属性|=值]`,`[lang|="en"]`选择 lang 属性等于 en 或以 en- 开头的所有元素。该值必须是整个单词。
9. 后代选择器:`选择器1 选择器2 ...`,中间有空格,选择器 1 后端元素中选择器 2 的元素,可以是子代、孙子代、、、、
10. 子代选择器:`选择器1 > 选择器2`,
11. 相邻兄弟选择器:
    1. `选择器1 + 选择器2`,相同父元素,紧跟选择器 1 的首个选择器 2
    2. `选择器1 ~ 选择器2`,相同父元素,紧跟选择器 1 的所有选择器 2

## 伪类:

伪类用于定义元素的特殊状态。

```css
:link    未访问的链接
:visited 已访问的链接
:hover   鼠标悬停链接
:active  已选择的链接

:first-child 伪类与指定的元素匹配：该元素是另一个元素的第一个子元素
:lang 伪类允许您为不同的语言定义特殊的规则。

所有 CSS 伪类
选择器	例子	例子描述
:link			a:link	选择所有未被访问的链接。
:visited		a:visited	选择所有已访问的链接。
:hover			a:hover	选择鼠标悬停其上的链接。
:active			a:active	选择活动的链接。

:enabled		input:enabled	选择每个已启用的 <input> 元素。
:checked		input:checked	选择每个被选中的 <input> 元素。
:disabled		input:disabled	选择每个被禁用的 <input> 元素。
:focus			input:focus		选择获得焦点的 <input> 元素。
:in-range		input:in-range	选择具有指定范围内的值的 <input> 元素。
:invalid		input:invalid	选择所有具有无效值的 <input> 元素。
:optional		input:optional	选择不带 "required" 属性的 <input> 元素。
:out-of-range	input:out-of-range	选择值在指定范围之外的 <input> 元素。
:read-only		input:read-only		选择指定了 "readonly" 属性的 <input> 元素。
:read-write		input:read-write	选择不带 "readonly" 属性的 <input> 元素。
:required		input:required		选择指定了 "required" 属性的 <input> 元素。
:valid			input:valid			选择所有具有有效值的 <input> 元素。

:empty			p:empty			选择没有子元素的每个 <p> 元素。
:first-child	p:first-child	选择作为其父的首个子元素的每个 <p> 元素。
:first-of-type	p:first-of-type	选择作为其父的首个 <p> 元素的每个 <p> 元素。
:lang(language)	p:lang(it)		选择每个 lang 属性值以 "it" 开头的 <p> 元素。
:last-child		p:last-child	选择作为其父的最后一个子元素的每个 <p> 元素。
:last-of-type	p:last-of-type	选择作为其父的最后一个 <p> 元素的每个 <p> 元素。
:nth-child(n)	p:nth-child(2)	选择作为其父的第二个子元素的每个 <p> 元素。
:nth-of-type(n)	p:nth-of-type(2)	选择作为其父的第二个 <p> 元素的每个 <p> 元素。
:only-of-type	p:only-of-type	选择作为其父的唯一 <p> 元素的每个 <p> 元素。
:only-child		p:only-child	选择作为其父的唯一子元素的 <p> 元素。
:nth-last-child(n)		p:nth-last-child(2)	选择作为父的第二个子元素的每个<p>元素，从最后一个子元素计数。
:nth-last-of-type(n)	p:nth-last-of-type(2)	选择作为父的第二个<p>元素的每个<p>元素，从最后一个子元素计数

:not(selector)	:not(p)	选择每个非 <p> 元素的元素。

:root			root	选择元素的根元素。

:target			#news:target	选择当前活动的 #news 元素（单击包含该锚名称的 URL）。
```

## content

content 属性与 :before 及 :after 伪元素配合使用，来插入内容。

```css
none			设置 content 为空值。
normal			在 :before 和 :after 伪类元素中会被视为 none，即也是空值。
counter			设定计数器，格式可以是 counter(name) 或 counter(name,style) 。产生的内容是该伪类元素指定名称的最小范围的计数；格式由style指定（默认是'decimal'——十进制数字）
attr(attribute)	将元素的 attribute 属性以字符串形式返回。。
string			设置文本内容
open-quote		设置前引号
close-quote		设置后引号
no-open-quote	移除内容的开始引号
no-close-quote	移除内容的闭合引号
url(url)		设置某种媒体（图像，声音，视频等内容）的链接地址
inherit			指定的 content 属性的值，应该从父元素继承
```
