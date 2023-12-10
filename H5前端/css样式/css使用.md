# css 使用

- CSS 指的是层叠样式表 (Cascading Style Sheets)
- CSS 描述了*如何在屏幕、纸张或其他媒体上显示 HTML 元素*
- CSS _节省了大量工作_。它可以同时控制多张网页的布局
- 外部样式表存储在 *CSS 文件*中

## 注释

```css
/*  */
```

## css 引入

1. 外部.css 文件
   1. 在 head 部分的 `<link>` 元素内包含对外部样式表文件的引用。
   2. `.css` 文件
2. 内部,在 head 部分的 `<style>` 元素中进行定义。
3. 行内,使用元素属性`style="css属性"`

```html
<head>
  <link rel="stylesheet" type="text/css" href="cssfile_name.css" />
  <style>
    /*....css_code*/
    .class {
      backound-color: red !important;
    }
  </style>
</head>
<body>
  <p style="css属性"></p>
</body>
```

注意:

1. 如果在不同样式表中为同一选择器（元素）定义了一些属性，则将使用最后读取的样式表中的值。
2. 页面中的所有样式将按照以下规则“层叠”为新的“虚拟”样式表，其中第一优先级最高：
   1. 行内样式（在 HTML 元素中）
   2. 外部和内部样式表（在 head 部分）
   3. 浏览器默认样式
   4. `!important` 拥有最高优先级

# @import 导入其他样式表

```css
@import urlstring list-of-mediaqueries; /**引用其他样式表,顶行 */

@import "navigation.css";
@import url("navigation.css");
@import "printstyle.css" print;
@import "mobstyle.css" screen and (max-width: 768px);
```