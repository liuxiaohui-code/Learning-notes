# hello world

```c++
#include <iostream>
using namespace std;
int main()
{
    cout << "Hello, world!" << endl;
    return 0;
}
```

# 与 c 的区别

1. C 语言可以在 C++编译器上完美运行，即 C 属于 C++
2. C++比 C 多出来一些函数库,c++的库一般不包含`.h`
3. C++是面向对象编程（即有 class 以及相关工具）
4. c++ 具有命名空间`using namespace std;`
5. C 把 `char` 常量视为 int 类型，而 C++将其视为 char 类型。
6. 在 C 中，全局的 `const` 具有外部链接，但是在 C++中，具有内部链接,另外，在 C++中，可以用 const 来声明普通数组的大小
7. 声明一个有标记的结构 `struct` 或联合 `union` 后，就可以在 C++中使用这个标记作为类型名
8. C++使用枚举比 C 严格。特别是，只能把 `enum` 常量赋给 enum 变量，然后把变量与其他值作比较。不经过显式强制类型转换，不能把 int 类型值赋给 enum 变量，而且也不能递增一个 enum 变量;另外，在 C++中，不使用关键字 enum 也可以声明枚举变量
9. C++可以把任意类型的指针赋给指向 void 的指针，这点与 C 相同。但是不同的是，只有使用显式强制类型转换才能把指向 void 的指针赋给其他类型的指针。
10. 在 C++中，布尔类型是 `bool`,而且 ture 和 false 都是关键字。在 C 中，布尔类型是`_Bool`，但是要包含 `stdbool.h` 头文件才可以使用 bool、true 和 false。
11. 在 C++中，可以用 `or` 来代替`||`，还有一些其他的可选拼写，它们都是关键字。在 C99 和 C11 中，这些可选拼写都被定义为宏，要包含 `iso646.h` 才能使用它们。
12. 在 C++中，`wchar_t` 是内置类型，而且 wchar_t 是关键字。在 C99 和 C11 中，wchar_t 类型被定义在多个头文件中(stddef.h、stdlib.h、wchar.h、wctype.h)。与此类似，char16_t 和 char32_t 都是 C++11 的关键字，但是在 C11 中它们都定义在 uchar.h 头文件中。C++通过 iostream 头文件提供宽字符 I/O 支持(wchar_t、char16_t 和 char32_t)，而 C99 通过 wchar.h 头文件提供一种完全不同的 I/O 支持包。
13. C++在 `complex` 头文件中提供一个复数类来支持复数类型。C 有内置的复数类型，并通过 `complex.h` 头文件来支持。这两种方法区别很大，不兼容。C 更关心数值计算社区提出的需求。
14. C99 支持了 C++的内联函数特性。但是，C99 的实现更加灵活。在 C++中，内联函数默认是内部链接。在 C++中，如果一个内联函数多次出现在多个文件中，该函数的定义必须相同，而且要使用相同的语言记号。
15. 虽然在过去 C 或多或少可以看作是 C++的子集，但是 C99 标准增加了一些 C++没有的新特性。
    1. 指定初始化器
    2. 首先指针`restrict`
    3. 变长数组
    4. 伸缩型数组成员
    5. 带可变数量参数的宏
16. c++ 具有 `string` 类型标识字符串常量,c 使用`char[]`表示字符串

```c++
// 多行字符串
string greeting2 = "hello, \
                    runoob";
```

# 类与对象

## 类定义

```c++
class Box
{
  protected:
  // 受保护成员,在派生类（即子类）中是可访问的
  private:
  // 私有成员,变量或函数在类的外部是不可访问的，甚至是不可查看的。只有类和友元函数可以访问私有成员.
  // 默认情况下，类的所有成员都是私有的
  public:
  // 共有成员,在程序中类的外部是可访问的 .
  double length;         // 长度
  double breadth;        // 宽度
  double height;         // 高度
  // 成员函数
  double getVolume(void);// 返回体积
  // 构造函数,创建对象时使用
  Box();
  Box(double length);
  //析构函数,对象销毁时调用
  ~Box();
  // 拷贝构造函数
  Box( const Box &obj);
  // 友元函数,不是成员函数,可以定义在类外部
  friend void printWidth( Box box );
  // 在类定义中的定义的函数都是内联函数，即使没有使用 inline 说明符。
  // 在成员函数内部，它可以用来指向调用对象 this
  // 一个指向 C++ 类的指针与指向结构的指针类似，访问指向类的指针的成员，需要使用成员访问运算符 ->，就像访问指向结构的指针一样

  // 静态成员,Box::objectCount
  static int objectCount;
  // 静态成员函数只能访问静态成员变量, Box::getCount()
  static int getCount();

};
double Box::getVolume(void)
{
    return length * breadth * height;
}

```

## 使用对象

```c++
Box myBox;          // 创建一个对象

myBox.getVolume();  // 调用该对象的成员函数

Box myBox(1.1); // 使用有参构造函数创建对象
```

## 继承

```c++
class derived-class: access-specifier base-class{}
// 访问修饰符 access-specifier 是 public、protected 或 private 其中的一个，base-class 是之前定义过的某个类的名称。如果未使用访问修饰符 access-specifier，则默认为 private。
// 当一个类派生自基类，该基类可以被继承为 public、protected 或 private 几种类型。继承类型是通过上面讲解的访问修饰符 access-specifier 来指定的。
// 我们几乎不使用 protected 或 private 继承，通常使用 public 继承。当使用不同类型的继承时，遵循以下几个规则：
//公有继承（public）：当一个类派生自公有基类时，基类的公有成员也是派生类的公有成员，基类的保护成员也是派生类的保护成员，基类的私有成员不能直接被派生类访问，但是可以通过调用基类的公有和保护成员来访问。
//保护继承（protected）： 当一个类派生自保护基类时，基类的公有和保护成员将成为派生类的保护成员。
//私有继承（private）：当一个类派生自私有基类时，基类的公有和保护成员将成为派生类的私有成员。
// 多继承
class <派生类名>:<继承方式1><基类名1>,<继承方式2><基类名2>,…
{
<派生类类体>
};
// 基类
class Animal {
    // eat() 函数
    // sleep() 函数
};


//派生类
class Dog : public Animal {
    // bark() 函数
};
```

# 标准库

1. `<ctime>` 时间与日期
2. `<iostream>` 该文件定义了 cin|cout|cerr | clog 对象，分别对应于标准输入流、标准输出流、非缓冲标准错误流和缓冲标准错误流。
3. `<iomanip>` 该文件通过所谓的参数化的流操纵器（比如 setw 和 setprecision），来声明对执行标准化 I/O 有用的服务。
4. `<fstream>` 该文件为用户控制的文件处理声明服务。
