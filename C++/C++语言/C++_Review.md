# C++

## 基础

### sizeof关键字

sizeof 是 C 语言中的运算符，用来计算一个类型/对象的所占用的内存大小。

>有些 C/C++ 面试官会出一些 sizeof(xxx) 的大小是多少的问题。

**sizeof计算大小技巧**.

+ 指针的大小永远是固定的，取决于处理器位数，32位就是 4 字节，64位就是 8 字节
+ 数组作为函数参数时会退化为指针，大小要按指针的计算， 详细请看这篇文章数组退化为指针(问题)
+ struct 结构体要考虑字节对齐，字节对齐的具体规则请看这篇文章: 字节对齐(问题)
+ 字符串数组要算上末尾的 '\0'

```C++
// 前提: 64 位电脑上
char  str[] = "Hello World" ;
char   *p = str;
double *dp;
int n = 10;

sizeof(str )＝___12_____  // 11个字符 + 末尾'\0'，总结第四点
sizeof ( p ) =  ___8 ___  // 64 位电脑，指针 8 个字节
sizeof ( n ) = ___4______  // int 一般 4 个字节
  
void Func (char str[10])
{
   sizeof( str ) = _8__  // 数组做参数退化为 char类型指针，即 8 个字节，总结第2点
}
void *vp = malloc( 100 );

sizeof ( vp )＝__8____  // vp 是一个 void 类型指针，还是 8 个字节

struct AlignedStruct {
    char a;   // 本来1字节，padding 3 字节
    int b;    //  4 字节
    short c;  // 本来 short 2字节，但是整体需要按照 4 字节对齐(成员对齐边界最大的是int 4) ，
    //所以需要padding 2，总共: 4 + 4 + 4
};

sizeof(AlignedStruct) = ___12__
```

### sizeof和strlen

strlen 是cstring头文件中的函数，而 sizeof 是 C++ 中的运算符。

**strlen**.

strlen 是一个 C 标准库中的函数，用于计算 C 风格字符串（以空字符 '\0' 结尾的字符数组）的长度，即不包括结尾的空字符的字符个数。

```C++

#include <iostream>
#include <cstring>

int main() {
    char str[] = "Hello, world!";
    std::cout << "Length of str: " << strlen(str) << std::endl; // 输出字符串 str 的长度
}

//strlen原码如下：
size_t strlen(const char *str) {
    size_t length = 0;
    while (*str++)
        ++length;
    return length;
}

```

**sizeof**.

sizeof 是一个 C++ 编译期间计算的操作符，用于计算数据类型或对象所占用的字节数。

```c++
#include <iostream>

int main() {
    int a = 42;
    std::cout << "Size of int: " << sizeof(int) << std::endl;    // 输出 int 类型的大小
    std::cout << "Size of a: " << sizeof(a) << std::endl;        // 输出变量 a 的大小
    std::cout << "Size of double: " << sizeof(double) << std::endl; // 输出 double 类型的大小
}
 
```

### 数组做参数->退还为指针

>C++ 面试中还有一个比较常见的考题，就是会将一个数组做参数，然后在函数内部用 sizeof 去判断这个数组参数的大小，如下:

```C++
int func(char array[]) {
    printf("sizeof=%d\n", sizeof(array));
    printf("strlen=%d\n", strlen(array));
}

int main() {
    char array[] = "Hello World";
    printf("sizeof=%d\n", sizeof(array));
    printf("strlen=%d\n", strlen(array));
    func(array);
}

//对于64位机器来说:
sizeof=12
strlen=11
sizeof=8
strlen=11

//sizeof(array) 为什么会是 8 呢？-->数组作为参数时候
```

**数组作为参数退化为指针**.

**数组退化**:在 C++ 中，数组在作为函数参数时会退化为指向其首元素的指针。

退化的原因是因为数组作为函数参数时，**实际传递的是指向数组首元素的指针**，不可能逐个拷贝整个数组然后在栈上传递，所以编译器只知道参数是一个指针，而不知道它的长度信息。

但是，当数组直接作为 sizeof 的参数时，它不会退化，因为 sizeof 是编译器在编译期间计算的结果，这个时候编译器是有信息知道数组的大小。

为了在函数中获取数组的长度，需要将数组的长度作为另一个参数传递给函数，或者使用模板实现。

```c++
#include <iostream>
#include <cstring>
template <typename T, std::size_t N>
void printSizeAndLength(const T (&arr)[N]) {
    std::cout << "Size of arr in function: " << sizeof(arr) << std::endl; // 计算数组的大小
    std::cout << "Length of arr: " << strlen(arr) << std::endl; // 计算字符串的长度
}
int main() {
    char str[] = "Hello, world!";
    std::cout << "Size of str in main: " << sizeof(str) << std::endl; // 计算整个字符数组的大小
    printSizeAndLength(str);
}

//结果
Size of str in main: 14
Size of arr in function: 14
Length of arr: 13

//这段代码使用了模板函数 printSizeAndLength，它接受一个数组引用作为参数。

//在函数内部，使用 sizeof 计算数组的大小时，数组不会退化为指针。

//引用的作用就在于阻止拷贝的发生，通过传递引用，让形参得到和数组名同样的地址。

```

### 指针和引用的区别

>指针和引用在 C++ 中都用于间接访问变量，但它们有一些区别：

1.**指针**是一个变量，它**保存**了另一个**变量的内存地址**；**引用**是另一个变量的**别名**，与原变量共享内存地址。

2.指针可以被重新赋值，指向不同的变量；引用在初始化后不能更改，始终指向同一个变量。

3.指针可以为 nullptr，表示不指向任何变量；引用必须绑定到一个变量，不能为 nullptr。

4.使用**指针需要对其进行解引用**以获取或修改其指向的变量的值；**引用**可以直接使用，**无需解引用**。

```C++
#include <iostream>

int main() {
    int a = 10;
    int b = 20;

    // 指针
    int *p = &a;
    std::cout << "Pointer value: " << *p << std::endl; // 输出：Pointer value: 10

    p = &b;
    std::cout << "Pointer value: " << *p << std::endl; // 输出：Pointer value: 20

    // 引用
    int &r = a;
    std::cout << "Reference value: " << r << std::endl; // 输出：Reference value: 10

    // r = &b; // 错误：引用不能被重新绑定
    int &r2 = b;
    r = r2; // 将 b 的值赋给 a，r 仍然引用 a
    std::cout << "Reference value: " << r << std::endl; // 输出：Reference value: 20

    return 0;
}

```

### 虚函数和纯虚函数的区别？

>这里需要先了解些概念

+ 定义一个函数为**虚函数**，**不代表函数为不被实现的函数**。
+ 定义他为虚函数是**为了允许用基类的指针来调用子类的这个函数**。
+ 定义一个**函数为纯虚函数，才代表函数没有被实现**。
+ 定义**纯虚函数是为了实现一个接口，起到一个规范的作用**，规范继承这个类的程序员必须实现这个函数。

示例：

```C++
class A
{
public:
    virtual void foo()
    {
        cout<<"A::foo() is called"<<endl;
    }
};
class B:public A
{
public:
    void foo()
    {
        cout<<"B::foo() is called"<<endl;
    }
};
int main(void)
{
    A *a = new B();
    a->foo();   // 在这里，a虽然是指向A的指针，但是被调用的函数(foo)却是B的!
    return 0;
}
```

上面的示例是虚函数的一个典型应用，它虚就虚在所谓"推迟联编"或者"动态联编"上，一个类函数的调用并不是在编译时刻被确定的，而是在运行时刻被确定的。由于编写代码的时候并不能确定被调用的是基类的函数还是哪个派生类的函数，所以被成为"虚"函数。

> 虚函数只能借助于指针或者引用来达到多态的效果。

#### c++纯虚函数

**1.定义**：纯虚函数是在基类中声明的虚函数，它在基类中没有定义，但要求任何派生类都要定义自己的实现方法。在基类中实现纯虚函数的方法是在函数原型后加 =0:

`virtual void funtion1()=0`

**2.引入原因**:

+ 方便使用多态特性，我们常常需要在基类中定义虚拟函数。
+ 很多情况下，基类本身生成对象是不合情理的.(eg:动物作为一个基类可以派生出老虎、孔雀等子类，但动物本身生成对象明显不合常理。)

为了解决上述问题，引入了纯虚函数的概念，将函数定义为纯虚函数（方法：virtual ReturnType Function()= 0;），则编译器要求在派生类中必须予以重写以实现多态性。同时含有纯虚拟函数的类称为抽象类，它不能生成对象。这样就很好地解决了上述两个问题。

声明了纯虚函数的类是一个抽象类。所以，用户不能创建类的实例，只能创建它的派生类的实例。

**纯虚函数最显著的特征是**：它们必须在继承类中重新声明函数（不要后面的＝0，否则该派生类也不能实例化），而且它们在抽象类中往往没有定义。

定义纯虚函数的目的在于，使派生类仅仅只是继承函数的接口。

纯虚函数的意义，让所有的类对象（主要是派生类对象）都可以执行纯虚函数的动作，但类无法为纯虚函数提供一个合理的默认实现。所以类纯虚函数的声明就是在告诉子类的设计者，"你必须提供一个纯虚函数的实现，但我不知道你会怎样实现它"。