# 前言

https://blog.csdn.net/xiaoxianer321/article/details/120407071



1. AWT
   1. Frame
   2. 监听事件(适配器模式)
      1. 鼠标
         1. `MouseListener`
      2. 键盘
         1. `KeyListener`
      3. 窗口 关闭打开
         1. `WindowListener`
      4. 动作事件 
         1. `AnctionListener` 实现此接口，也可以多个组件共用一个事件监听
         2. `AnctionEvent`
2. Swing
   1. 文本框
   2. 标签
   3. 文本域
   4. 面板
   5. 布局方法
   6. 关闭窗口
   7. 列表
3. 贪吃蛇
   1. Timer
   2. 键盘监听
   3. 游戏帧的概念

GUI的核心技术Swing,AWT,AWT是Swing的前身，用AWT实现底层，Swing画一些界面

# AWT
1. 包含很多类和接口
2. 元素:窗口、视图、文本框
3. 组件 Component
   1. 容器 Container
      1. `Window`
         1. `Frame`
         2. `Dialog`
      2. 面板 `Panel` 可以看成一个界面，但不能单独存在，要放在Frame或Dialog中；有布局的概念
         1. `Applet`
   2. 基本组件 存在在容器中
      1. `Button` 按钮
      2. `TextArea` 多行文本框
      3. `TextField` 单行文本框
         1. 设置替换编码 toE
      4. `Lable` 标签，提示文本

## 一般使用
1. new Frame()
   1. 设置布局 `setLayout()`
      1. `FlowLayout` 流式布局; 左中右;默认布局，默认中
      2. `BorderLayout`东西南北中
      3. `GridLayout`表格布局
   2. 设置可见性、大小、背景、位置、可伸缩性、可见性
2. new Pannel()
   1. 坐标(相对)
3. add --- frame 添加 panel
4. 设置监听事件

```java

```
## 监听事件
`addWindowListener`
`WindowListener`
使用适配器模式 `WindowAdapter`

## 画笔
`Graphics`
方法：
- paint()
- repaint()
注意：
- 画笔用完，将它还原为最初的颜色

# Swing
继承了JWT
1. JFrame
   1. 使用:
      1. new JFrame(),
      2. 获取容器`getContentPane()`,设置容器的背景颜色\布局
         1. 绝对布局`setLayout(null)`
      3. 设置可见\大小\坐标... 
      4. 添加一些元素到容器
         1. 在绝对布局中可以直接使用相对坐标布局:`setBounds()`
      5. 关闭事件 `setDefaultCloseOperation(WindowConstants.EXIT_ON_CLOSE)`
   2. 元素
      1. JLable
      2. JDialog 弹窗 默认就有关闭事件


layui

## Swing元素

1. 标签 JLabel
   1. 图标 Icon
   2. 图片图标 imageIcon
2. 面板 JPanel
   1. 滚动条 JScroll
3. 按钮 JButton
   1. 图片按钮
4. 单选框
5. 多选框 JCheckBox
   1. JCheckBoxMenuItem
6. 下拉框
7. 列表
8. 文本框
9.  密码框
10. 文本域