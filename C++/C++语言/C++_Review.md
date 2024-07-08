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

### const关键字

const 关键字，用于表示常量

const 可以用于修饰变量、函数、指针等，主要作用有以下几种：

#### 修饰变量

**const修饰变量：变量被视为只读，不可修改(Only-Read);**

对于确定不会被修改的变量，应该加上 const，这样可以**保证变量的值不会被无意中修改，也可以使编译器在代码优化时更加智能**。

```C++
const int a = 10;
a = 20; // 编译错误，a 是只读变量，不能被修改

```

>这里的变量只读，其实只是编译器层面的保证，实际上可以通过指针在运行时去间接修改这个变量的值，当然这个方法比较trick。

对 const int 类型取指针，就是 const int* 类型的指针，将其强制转换为 int* 类型，就去掉了 const 限制，从而修改变量的值。

在 C++ 中，将 const 类型的指针强制转换为非const 类型的指针被称为类型强制转换（Type Casting），这种行为称为 **const_cast**。

>关于 const_cast 可以看下这篇文章: C++几种类型转换的区别(opens new window)

虽然可以这样操作，但这违反了 const的语义，可能会导致程序崩溃或者产生未定义行为(undefined behavior)，大家学习了解即可，实际编程中切莫如此操作。

因为编译器可能会做一些优化！！也就是在你用到 const 变量的地方，编译器可能生成的代码直接就替换为常量的值，而不是访问一遍常量的指令。

所以极大可能你虽然修改了值，但是却不起作用！

下面👇这个例子，展示了使用 const_cast 修改 const变量的值却不会起作用：

```c++
const int a = 10;
const int* p = &a;
int* q = const_cast<int*>(p);
*q = 20;  // 通过指针间接修改 const 变量的值
std::cout << "a = " << a << std::endl;  // 输出 a 的值，结果为 10

```

在上面的例子中，将 p 声明为 const int* 类型，指向只读变量 a 的地址。

然后使用 const_cast 将 p 强制转换为 int* 类型的指针 q，从而去掉了 const限制。

接下来，通过指针 q 间接修改了变量 a 的值。

**但是请注意，即使 a 的值被修改了，但在程序中输出a 的值仍然是 10，**

正如前面所有，因为 a 是只读变量，所以编译器做了优化，早就把代码实际替换为了👇下面这样:

std::cout << "a = " << 10 << std::endl;

所以，咱们还是要老老实实按照语言标准编程，切莫搞各种骚操作。

总之，使用 const_cast 去掉 const 限制是不推荐的，这会破坏程序的正确性和稳定性。

**我们应该遵循 C/C++ 语言中 const 的语义，尽量不修改只读变量的值**.

#### 修饰函数参数，表示函数不会修改参数

当 const 修饰函数参数时，表示函数内部不会修改该参数的值。这样做可以使代码更加安全，避免在函数内部无意中修改传入的参数值。

尤其是 引用 作为参数时，如果确定不会修改引用，那么一定要使用 const 引用。

```C++
void func(const int a) {
    // 编译错误，不能修改 a 的值
    a = 10;
}
```

#### 修饰函数返回值

当 const 修饰函数返回值时，表示函数的返回值为只读，不能被修改。这样做可以使函数返回的值更加安全，避免被误修改。

```C++

const int func() {
    int a = 10;
    return a;
}

int main() {
    const int b = func(); // b 的值为 10，不能被修改
    b = 20; // 编译错误，b 是只读变量，不能被修改
    return 0;
}

```

#### 修饰指针或引用

const 关键字可以用来修饰指针，用于声明指针本身为只读变量或者指向只读变量的指针。

根据 const 关键字的位置和类型，可以将 const 指针分为以下几种情况：

##### 1.指向只读变量的指针

const 关键字修饰的是指针所指向的变量，而不是指针本身。

因此，指针本身可以被修改（意思是指针可以指向新的变量），但是不能通过指针修改所指向的变量。

```C++
const int* p;  // 声明一个指向只读变量的指针，可以指向 int 类型的只读变量
int a = 10;
const int b = 20;
p = &a;  // 合法，指针可以指向普通变量
p = &b;  // 合法，指针可以指向只读变量
*p = 30;  // 非法，无法通过指针修改只读变量的值

```

##### 2.只读指针

const 关键字修饰的是指针本身，使得指针本身成为只读变量。

因此，指针本身不能被修改（即指针一旦初始化就不能指向其它变量），但是可以通过指针修改所指向的变量。

```C++
int a = 10;
int b = 20;
int* const p = &a;  // 声明一个只读指针，指向 a
*p = 30;  // 合法，可以通过指针修改 a 的值
p = &b;  // 非法，无法修改只读指针的值
```

##### 3.只读指针指向只读变量

这种情况下，const 关键字同时修饰了指针本身和指针所指向的变量，使得指针本身和所指向的变量都成为只读变量。

因此，指针本身不能被修改，也不能通过指针修改所指向的变量。

```c++
const int a = 10;
const int* const p = &a;  // 声明一个只读指针，指向只读变量 a
*p = 20;  // 非法，无法通过指针修改只读变量的值
p = nullptr;  // 非法，无法修改只读指针的值
```

##### 4.常量引用

常量引用是指引用一个只读变量的引用，因此不能通过常量引用修改变量的值。

```C++
const int a = 10;
const int& b = a;  // 声明一个常量引用，引用常量 a
b = 20;  // 非法，无法通过常量引用修改常量 a 的值

```

#### 修饰成员函数

当 const 修饰成员函数时，表示该函数不会修改对象的状态（就是不会修改成员变量）。

这样有个好处是，const 的对象就可以调用这些成员方法了，因为 const 对象不允许调用非 const 的成员方法。

也很好理解，既然对象是 const 的，那我怎么保证调用完这个成员方法，你不会修改我的对象成员变量呢？那就只能你自己把方法声明未 const 的呢~

```C++
class A {
public:
    int func() const {
        // 编译错误，不能修改成员变量的值
        m_value = 10;
        return m_value;
    }
private:
    int m_value;
};


```

这里还要注意，**const 的成员函数不能调用非 const 的成员函数**，原因在于 const 的成员函数保证了不修改对象状态，但是如果调用了非 const 成员函数，那么这个保证可能会被破坏。

总之，const 关键字的作用是为了保证变量的安全性和代码可读性。

### static关键字

static 是一个非常重要的关键字，它可以用于变量、函数和类中。

**1.static修饰全局变量**.

static 修饰全局变量可以将变量的作用域限定在当前文件中，使得其他文件无法访问该变量.

同时，static 修饰的全局变量在程序启动时被初始化（可以简单理解为在执行 main 函数之前，会执行一个全局的初始化函数，在那里会执行全局变量的初始化），生命周期和程序一样长.

```C++
// a.cpp 文件
static int a = 10;  // static 修饰全局变量
int main() {
    a++;  // 合法，可以在当前文件中访问 a
    return 0;
}

// b.cpp 文件
extern int a;  // 声明 a
void foo() {
    a++;  // 非法，会报链接错误，其他文件无法访问 a ,为什么非法？--详细看看extern的作用。
}

```

**2.static修饰局部变量**.

static 修饰局部变量可以使得变量在函数调用结束后不会被销毁，而是一直存在于内存中，下次调用该函数时可以继续使用。

同时，由于 static 修饰的局部变量的作用域仅限于函数内部，所以其他函数无法访问该变量。

```C++
void foo() {
    static int count = 0;  // static 修饰局部变量
    count++;
    cout << count << endl;
}

int main() {
    foo();  // 输出 1
    foo();  // 输出 2
    foo();  // 输出 3
    return 0;
}
```

**3.static修饰函数**.

static 修饰函数可以将函数的作用域限定在当前文件中，使得其他文件无法访问该函数。

同时，由于 static 修饰的函数只能在当前文件中被调用，因此可以避免命名冲突和代码重复定义。

```C++
// a.cpp 文件
static void foo() {  // static 修饰函数
    cout << "Hello, world!" << endl;
}

int main() {
    foo();  // 合法，可以在当前文件中调用 foo 函数
    return 0;
}

// b.cpp 文件
extern void foo(); // 声明 foo
void bar() {
    foo();  // 非法，会报链接错误，找不到 foo 函数，其他文件无法调用 foo 函数
}

```

**4.static修饰类成员变量和函数**.

static 修饰类成员变量和函数可以使得它们在所有类对象中共享，且不需要创建对象就可以直接访问。

```C++
class MyClass {
public:
    static int count;  // static 修饰类成员变量
    static void foo() {  // static 修饰类成员函数
        cout << count << endl;
    }
};
// 访问：

MyClass::count;
MyClass::foo();

```

### volatile的作用

volatile是 C 语言中的一个关键字，用于修饰变量，表示该变量的值可能在任何时候被外部因素更改，例如硬件设备、操作系统或其他线程

当一个变量被声明为volatile时，编译器会禁止对该变量进行优化，以确保每次访问变量时都会从内存中读取其值，而不是从寄存器或缓存中读取。

避免因为编译器优化而导致出现不符合预期的结果

```C++
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

volatile int counter = 0;

void *increment(void *arg) {
    for (int i = 0; i < 100000; i++) {
        counter++;
    }
    return NULL;
}

int main() {
    pthread_t thread1, thread2;

    // 创建两个线程，分别执行increment函数
    pthread_create(&thread1, NULL, increment, NULL);
    pthread_create(&thread2, NULL, increment, NULL);

    // 等待两个线程执行完毕
    pthread_join(thread1, NULL);
    pthread_join(thread2, NULL);

    printf("Counter: %d\n", counter);

    return 0;
}


```

上面声明了一个volatile int类型的全局变量counter，并创建了两个线程。

每个线程都会对counter变量进行100000次自增操作。

由于counter变量被声明为volatile，编译器不会对其进行优化，确保每次访问都会从内存中读取值。

当然啦，即便是volatile关键字可以确保编译器不对变量进行优化，但上面任然存在并发问题，counter++操作仍然可能导致数据不一致。

为了解决这个问题，需要使用互斥锁、原子操作或其他同步机制。


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