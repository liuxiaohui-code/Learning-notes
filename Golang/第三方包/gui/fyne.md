# fyne

## 官方文档

https://developer.fyne.io/started/

## 创建

```go
go mode init fynewh
go get fyne.io/fyne/v2
```

## fyne 命令

```shell
# 下载
go install fyne.io/fyne/v2/cmd/fyne
#
fyne install
# android 打包
$ fyne package -os android -appID my.domain.appname
$ fyne install -os android
# ios
$ fyne package -os ios -appID my.domain.appname
$ fyne package -os iossimulator -appID my.domain.appname
```

## 包结构

### fyne 应用整体

```go
import "fyne.io/fyne/v2"
```

#### 应用程序 App

```go
type App interface { //应用程序主体
	NewWindow(title string) Window // 创建窗口
	OpenURL(url *url.URL) error // 打开网页,在默认的浏览器
	Icon() Resource
	SetIcon(Resource)
	Run() // 运行
	Quit() // 退出
	Driver() Driver // 驱动
	UniqueID() string // 程序唯一ID
	SendNotification(*Notification) // 系统通知
	Settings() Settings // 全局设置
	Preferences() Preferences // 首选项
	Storage() Storage // 数据存储
	Lifecycle() Lifecycle // 生命周期
	Metadata() AppMetadata // 元数据
}
import "fyne.io/fyne/v2/app"
app.New() App


type AppMetadata struct //应用元数据
var BuildType // 应用构建方式,测试,标准,版本
type Device struct // 应用所在的设备信息
type DeviceOrientation int // 设备方向代表可以持有移动设备的不同方式
type Driver struct //驱动
type LegacyTheme struct //  定义了任何 Fyne 主题的要求。这是以前称为主题的，并保留为更简单的 v2.0.0 之前构建的应用程序过渡。
type Lifecycle struct // 生命周期
type ListableURI struct // 子目录
type URI interface // 表示目标系统上资源的标识符。此资源可以是文件或其他数据源，如应用程序或文件共享系统。一般来说，URI实现应该遵循IETF RFC3896。强烈建议实现使用net/url来实现URI解析方法，
type URIReadCloser interface
type URIWriteCloser interface
type Preference struct // 首选项描述了应用程序可以保存和加载用户偏好的方式
type Resource struct // 单个二进制资源，例如图像或字体。
```

#### 窗口

```go
type Window interface {
	Title() string //窗口标题
	SetTitle(string) // 设置标题
	FullScreen() bool // 全屏
	SetFullScreen(bool) // 设置全屏
	Resize(Size) // 调整窗口大小
	RequestFocus() // 移动焦点
	FixedSize() bool // 固定大小
	SetFixedSize(bool) // 设置固定大小
	CenterOnScreen() // 窗口位置在屏幕只能中心
	Padded() bool
	SetPadded(bool)
	Icon() Resource // 图标
	SetIcon(Resource)
	SetMaster()
	MainMenu() *MainMenu // 菜单栏
	SetMainMenu(*MainMenu)
	SetOnClosed(func())
	SetCloseIntercept(func())
	Show() // 显示窗口
	Hide() // 隐藏
	Close() // 关闭
	ShowAndRun() // 显示并启动程序,如果窗口是第一个窗口的话
	Content() CanvasObject // 窗口内容
	SetContent(CanvasObject)
	Canvas() Canvas // 画布
	Clipboard() Clipboard // 剪切板
}

```

### 组件 widget

```go
import "fyne.io/fyne/v2/widget"

type ButtonImportance int // 按钮高亮程度
```

#### 标签 Label

#### 文本输入框 Entry

```go
func NewEntry() *Entry // 单行输入框
NewMultiLineEntry() *Entry // 多行输入框
NewEntryWithData(data binding.String) *Entry // 连接指定数据源的输入框
func NewPasswordEntry() *Entry // 密码框

type Entry struct {
	DisableableWidget // 可禁用
	Text     string // 内容
	TextStyle   fyne.TextStyle // 文本杨树
	PlaceHolder string
	OnChanged   func(string) `json:"-"` // 监听文本变动
	OnSubmitted func(string) `json:"-"` // 监听提交
	Password    bool // 是否显示密码
	MultiLine   bool // 多行
	Wrapping    fyne.TextWrap // 文本溢出
	Validator           fyne.StringValidator `json:"-"` // 文本校验
	CursorRow, CursorColumn int // 鼠标指针行数,列数
	OnCursorChanged         func() `json:"-"` // 监听鼠标移动
	ActionItem      fyne.CanvasObject `json:"-"`
  // 其他不暴露属性
}

```

#### 富文本编辑器 RichText

```go
func NewRichText(segments ...RichTextSegment) *RichText // 返回一个新的富文本小部件，用于呈现给定的文本和句段
func NewRichTextWithText(text string) *RichText //该字符串将使用默认文本设置转换为单个文本段。
func NewRichTextFromMarkdown(content string) *RichText // 将文本解析为 markdown 语法

type RichText struct {
	BaseWidget
	Segments []RichTextSegment
	Wrapping fyne.TextWrap
	Scroll   widget.ScrollDirection
}

func (*RichText).CreateRenderer() fyne.WidgetRenderer
func (*RichText).MinSize() fyne.Size
func (*RichText).ParseMarkdown(content string)
func (*RichText).Refresh()
func (*RichText).Resize(size fyne.Size)
func (*RichText).String() string
func (*RichText).cachedSegmentVisual(seg RichTextSegment, offset int) fyne.CanvasObject
func (*RichText).charMinSize(concealed bool, style fyne.TextStyle) fyne.Size
func (*RichText).deleteFromTo(lowBound int, highBound int) string
func (*RichText).insertAt(pos int, runes string)
func (*RichText).len() int
func (*RichText).lineSizeToColumn(col int, row int) fyne.Size
func (*RichText).row(row int) []rune
func (*RichText).rowBoundary(row int) *rowBoundary
func (*RichText).rowLength(row int) int
func (*RichText).rows() int
func (*RichText).updateRowBounds()
```

#### 按钮 Button

### 加载用户偏好 Preference

首选项描述了应用程序可以保存和加载用户偏好的方式

### 资源文件 Resource

资源代表单个二进制资源，例如图像或字体。资源具有识别名称和字节数组内容。可以获得资源的串行路径，这可能导致封锁文件系统写操作。

```java
type Resource interface {
	Name() string
	Content() []byte
}
func LoadResourceFromPath(path string) (Resource, error)
func LoadResourceFromURLString(urlStr string) (Resource, error)

type StaticResource struct { // 捆绑资源
	StaticName    string
	StaticContent []byte
}
func NewStaticResource(name string, content []byte) *StaticResource
```

### 配置 Settings

设置描述了可用的应用程序配置

### 快捷方式 Shortcut

快捷方式是用于描述快捷操作的界面

```go
type Shortcut interface {
	ShortcutName() string
}
type ShortcutCopy struct {
	Clipboard Clipboard
} // ShortcutCopy描述了一种快捷方式复制操作。
func (se *ShortcutCopy) Key() KeyName
func (se *ShortcutCopy) Mod() KeyModifier
func (se *ShortcutCopy) ShortcutName() string
type ShortcutCut struct {
	Clipboard Clipboard
}
func (se *ShortcutCut) Key() KeyName
func (se *ShortcutCut) Mod() KeyModifier
func (se *ShortcutCut) ShortcutName() string

ShortcutHandler //shortcuthandler是CanvasObject的快捷方式处理程序的默认实现
ShortcutPaste // 快捷方式粘贴描述了快捷方式粘贴操作。
ShortcutSelectAll//描述了一种快捷方式selectAll操作
Shortcutable //快捷方式描述任何可以响应快捷命令（退出，切割，复制和粘贴）的CanvasObject。
```

### 存储 Storage

```go
type Storage interface {
	RootURI() URI

	Create(name string) (URIWriteCloser, error)
	Open(name string) (URIReadCloser, error)
	Save(name string) (URIWriteCloser, error)
	Remove(name string) error

	List() []string
}
```

Storage is used to manage file storage inside an application sandbox. The files managed by this interface are unique to the current application.

### 主题 Theme

```go
type Theme interface {
	Color(ThemeColorName, ThemeVariant) color.Color //颜色 themevariant表示主题的变化，例如光或黑暗。
	Font(TextStyle) Resource //字体
	Icon(ThemeIconName) Resource
	Size(ThemeSizeName) float32 // 尺寸
}
```

## 组件

### 创建窗体 Window

窗口描述用户界面窗口。根据平台的不同，应用程序可能有多个窗口，也可能只有一个窗口。

```go
//
a := app.New()
w := a.NewWindow("Hello")
// 显示窗体,并运行app
w.ShowAndRun()
```

```go
//
a := app.New()
```

### 组件 Widget

```go
type Widget interface {
	CanvasObject

	// CreateRenderer returns a new WidgetRenderer for this widget.
	// This should not be called by regular code, it is used internally to render a widget.
	CreateRenderer() WidgetRenderer
}

WidgetRender定义小部件实现的行为。这是通过CreateRenderer（）函数从小部件的声明性对象返回的，应该是内存中每个小部件一个实例。
```

小部件定义了任何小部件的标准行为。这扩展了 CanvasObject——一个小部件以相同的基本方式工作，但将封装许多子对象以创建渲染的小部件。

### 动画 Animation

### 画布 Canvas

画布定义了可以添加 CanvasObject 或容器的图形画布。每个画布都有一个在渲染过程中自动应用的比例。

#### 布局容器 Container

容器是包含子对象集合的 CanvasObject。孩子的布局由指定的布局设置。

#### 双敲击 DoubleTappable

DoubleTappable 描述了任何也可以双击的 CanvasObject。

#### 画布叠加 OverlayStack

#### SecondaryTappable

SecondaryTappable 描述了一个可以直接点击或长时间敲击的 CanvasObject。

### 系统剪切板 Clipboard

### 二维坐标 Delta

增量是通用 X，Y 坐标，大小或运动表示。

```go
type Delta struct {
	DX, DY float32
}
func NewDelta(dx float32, dy float32) Delta
```

### 布局尺寸 Layout

布局定义了如何以指定尺寸布置 canvasobjects。

### 菜单栏 MainMenu

```go
type MainMenu struct { // 菜单栏
	Items []*Menu
}
func (m *MainMenu) Refresh() //刷新菜单栏
func NewMainMenu(items ...*Menu) *MainMenu //创建
type Menu struct { //弹出菜单
	Label string
	Items []*MenuItem
}
func NewMenu(label string, items ...*MenuItem) *Menu //创建菜单
func (m *Menu) Refresh() //刷新
type MenuItem struct { //菜单项
	ChildMenu *Menu
	IsQuit      bool 	// Since: 2.1
	IsSeparator bool
	Label       string
	Action      func()
	Disabled bool 	// Since: 2.1
	Checked bool 	// Since: 2.1
	Icon Resource 	// Since: 2.2
	Shortcut Shortcut 	// Since: 2.2
}
func NewMenuItem(label string, action func()) *MenuItem //创建
func NewMenuItemSeparator() *MenuItem // 创建了一个菜单项，该菜单项将用作分离器
```

### 通知 Notification

```go
type Notification struct { //通知表示可以发送到操作系统的用户通知。
	Title, Content string
}
func NewNotification(title, content string) *Notification
```

### 文本

```go
type TextAlign int //TextAlign表示小部件或画布对象中文本的水平对齐。
const (
	TextAlignLeading TextAlign = iota //左对齐
	TextAlignCenter // 中间对齐
	TextAlignTrailing //右对齐
)
type TextStyle struct { // 文本样式
	Bold      bool // Should text be bold
	Italic    bool // Should text be italic
	Monospace bool // Use the system monospace font instead of regular
	// Since: 2.2
	Symbol bool // Use the system symbol font.
	// Since: 2.1
	TabWidth int // Width of tabs in spaces
}
func MeasureText(text string, size float32, style TextStyle) Size // 文本大小
type TextWrap int //TextWrap表示如何包装长于控件宽度的文本。
const (
	TextWrapOff TextWrap = iota //不换行,加控件宽度
	TextTruncate //不换行,截断,加滚动条
	TextWrapBreak //换行,加滚动条
	TextWrapWord // 换行,滚动
)
```

## 事件

### 拖动 DragEvent

```go
type DragEvent struct {
	PointEvent
	Dragged Delta
}
//可拖动表明可以拖动CanvasObject。这用于用户指出的任何项目，应在屏幕上移动。
type Draggable interface {
	Dragged(*DragEvent)
	DragEnd()
}
```

dragevent 定义指针或其他拖动事件的参数。Draggedx 和 Draggedy 字段显示自上次事件以来该项目被拖动了多远。

### 聚焦 Focusable

### 键盘输入事件 KeyEvent

描述了键盘输入事件。

```go
type KeyModifier int //Keymodifier代表与键一起按下的任何修饰符键（Shift等）。
const (
	KeyModifierShift KeyModifier = 1 << iota //shift
	KeyModifierControl // ctrl
	KeyModifierAlt // alt
	KeyModifierSuper // win
)
type KeyName string //表示已按下的键的名称。


type Tabbable interface { // 按下 tab
	// AcceptsTab() is a hook called by the key press handling logic.
	// If it returns true then the Tab key events will be sent using TypedKey.
	AcceptsTab() bool
}
```

### 快捷键 KeyboardShortcut

### 指针输入事件 PointEvent

```go
type PointEvent struct {
	AbsolutePosition Position // The absolute position of the event
	Position         Position // The relative position of the event
}

type Position struct {
	X float32 // The position from the parent's left edge
	Y float32 // The position from the parent's top edge
}
func NewPos(x float32, y float32) Position
func (p Position) Add(v Vector2) Position
func (p Position) AddXY(x, y float32) Position
func (p Position) Components() (float32, float32)
func (p Position) IsZero() bool
func (p Position) Subtract(v Vector2) Position
func (p Position) SubtractXY(x, y float32) Position
```

### 滚动事件 ScrollEvent

```java
type ScrollEvent struct {
	PointEvent
	Scrolled Delta
}

type Scrollable interface {
	Scrolled(*ScrollEvent)
} //可滚动描述所有也可以滚动的CanvasObject。这主要用于实现widget.scrollcontainer。


```

## 其他

### 禁用 Disableable

禁用描述任何可以禁用的 CanvasObject。这主要用于也实现可敲击接口的对象。

### 跨平台兼容 HardwareKey

HardwareKey 包含与物理密钥事件相关的信息，大多数应用程序应使用 Keythame 进行跨平台兼容性。

### 宽高尺寸 Size

```go
type Size struct {
	Width  float32 // The number of units along the X axis.
	Height float32 // The number of units along the Y axis.
}
func NewSize(w float32, h float32) Size
```

### 文本大小 MeasureText

```go
func MeasureText(text string, size float32, style TextStyle) Size
```

MeasureText uses the current driver to calculate the size of text when rendered.

### 字符串校验函数 StringValidator

### Validable 是一个用于指定小部件是否可验证的接口。

### Vector2 标记可以用作坐标向量的几何图形类型。
