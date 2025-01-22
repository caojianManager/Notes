# 一.C\#
## 值类型和引用类型的区别？

来源：值类型-->继承自System.ValueType;引用类型-->继承自System.Object;
位置：值类型-->栈；引用类型-->堆。
类型：值类型-->存放的实际数据；引用类型-->存放的数据地址，即对象的引用。
速度：值类型存取速度相比引用类型会快一点。

**值类型（value type）**：byte，short，int，long，float，double，decimal，char，bool 和 struct 统称为值类型。值类型变量声明后，不管是否已经赋值，编译器为其分配内存。
**引用类型（reference type）**：string 和 class统称为引用类型。当声明一个类时，只在栈中分配一小片内存用于容纳一个地址，而此时并没有为其分配堆上的内存空间。当使用 new 创建一个类的实例时，分配堆上的空间，并把堆上空间的地址保存到栈上分配的小片空间中。

## 什么是拆箱装箱？

装箱：值类型-->转换-->引用类型
拆箱：引用类型-->转换-->值类型

## ArrayList和List的区别？

Array类：是C#所有数组的基类，在System命名空间中定义。
### 数组：

在内存中是连续存储的，所以它的索引速度非常快，而且赋值与修改元素也很简单。
不足之处：1.插入数据麻烦；2.声明时候必须指定数组长度，有可能造成内存浪费。
针对以上不足C#引入ArrayList;
### ArrayList:

ArrayList是命名空间System.Collections下的一部分.同时继承了IList接口，提供数据存储和检索。
ArrayList对象大小是按照其中存储的数据来动态扩充与收缩。所以，声明ArrayList对象时并不需要指定它的长度。

```c++
ArrayList list = new ArrayList();

//添加数据
list.Add("A");
list.Add(5678);

//修改数据
list[2] = 34;
//移除数据
list.RemoveAt(0);
//插入数据
list.Insert(0,123);
```

>从上面的示例，可以看出来ArrayList解决了数组大部分的缺点。那么为什么还要List?

因为ArrayList会把所有插入其中的数据当作为object类型来处理,所以在使用ArrayList的时候，很容易造成类型错误，即ArrayList不是类型安全的。并且在存储和检索值时候，会发生装箱和拆箱，带来很大的性能损耗。

### 泛型List

ArrayList存在不安全类型与装箱拆箱的缺点，所以出现了泛型的概念。List类是ArrayList类的泛型等效类，它的大部分用法都与ArrayList相似，因为List类也继承了IList接口，（和ArrayList的区别在于，在声明List对象的时候，必须声明类型）

```c++
List<string> list = new List<string>();

//添加数据
list.Add("adb");
//修改数据
list[0] = "ccc";
//移除数据
list.RemoveAt(0);
```

## 字典集合HashTable、Dictionary、ConcurrentDictionary三者区别?

### HashTable

HashTable表示键/值\<key-value>的集合。在.NET Framework中，System.Collections命名空间提供的一个容器。

key区分大小写，value用于存储对应key的值。

Key-value均为Object类型。所以HashTable可以支持任何类型的key-value键值对，
任何非null对象都可以用作键或值。

HashTable是一种散列表，内部维护很多key-value键值对，其中还有一个索引的值叫散列值(HashCode),它根据GetHashCode方法对Keyt通过一定算法获取得到的，
所有的查找操作定位操作都是基于散列值来实现找到对应的Key和Value值的。

散列函数(GetHashCode)让散列值对应HashTable的空间地址尽量不重复。

当一个HashTable被占用一大半的时候我们通过计算散列值取得的地址值可能会重复指向同一地址，这就造成哈希冲突。

C#中键值对在HashTable中的位置Position= (HashCode& 0x7FFFFFFF) % HashTable.Length，C#是通过探测法解决哈希冲突的，当通过散列值取得的位置Postion以及被占用的时候，就会增加一个位移x值判断下一个位置Postion+x是否被占用，如果仍然被占用就继续往下位移x判断Position+2*x位置是否被占用，如果没有被占用则将值放入其中。当HashTable中的可用空间越来越小时，则获取得到可用空间的难度越来越大，消耗的时间就越多。
### Dictionary

Dictionary<TKey, TValue> 泛型类提供了从一组键到一组值的映射。通过键来检索值的速度是非常快的，接近于 O(1)，这是因为 Dictionary<TKey, TValue> 类是作为一个哈希表来实现的。检索速度取决于为 TKey 指定的类型的哈希算法的质量。TValue可以是值类型，数组，类或其他。

Dictionary是一种变种的HashTable,它采用一种分离链接散列表的数据结构来解决哈希冲突的问题。

### ConcurrentDictionary

表示可由多个线程同时访问的键/值对的线程安全集合。

ConcurrentDictionary<TKey, TValue> framework4出现的，可由多个线程同时访问，且线程安全。用法同Dictionary很多相同，但是多了一些方法。ConcurrentDictionary 属于System.Collections.Concurrent 命名空间按照MSDN上所说：

System.Collections.Concurrent 命名空间提供多个线程安全集合类。当有多个线程并发访问集合时，应使用这些类代替 System.Collections 和 System.Collections.Generic 命名空间中的对应类型。

## 抽象类和接口的区别？

**相同点:**

1.都不能被实例化
2.接口的实现类或抽象类的子类都只有实现了接口或者抽象类中的方法后才能被实例化

**不同点：**

1.**接口**中，只能声明方法，不能实现，只有继承了接口的类才能实现声明的方法。**抽象类**的方法可以在抽象类中实现。
2.**接口**需使用implements实现，**抽象类**只能被继承(extends);
3.接口中定义的成员变量默认修饰符(public static final),而且必须赋初始值。

**抽象类**可以有自己的数据成员变量，也可以有非抽象的成员变量，而且抽象类中的成员默认为default(本包可见)。抽象类中的方法前面有abstract修饰，不能用private,static,synchronize,native修饰，同时方法必须以分号结尾，不带花括号。

> 使用规则：

- 1、抽象类主要用于关系密切的对象，而接口最适合为不相关的类提供通用功能
- 2、如果要设计大的功能单元，则使用抽象类；如果要设计小而简练的功能块，则使用接口。
- 3、如果预计要创建组件的多个版本，则创建抽象类。接口一旦创建就不能更改。如果需要接口的新版本，必须创建一个全新的接口。
- 4、如果创建的功能将在大范围的全异对象间使用，则使用接口；如果要在组件的所有实现间提供通用的已实现功能，则使用抽象类。
- 5、分析对象，提炼内部共性形成抽象类，用以表示对象本质，即“是什么”。为外部提供调用或功能需要扩充时优先使用接口
- 6、好的接口定义应该是具有专一功能性的，而不是多功能的，否则造成接口污染。如果一个类只是实现了这个接口的中一个功能，而不得不去实现接口中的其他方法，就叫接口污染
- 7、尽量避免使用继承来实现组建功能，而是使用黑箱复用，即对象组合。因为继承的层次增多，造成最直接的后果就是当你调用这个类群中某一类，就必须把他们全部加载到栈中！后果可想而知。(结合堆栈原理理解)。同时，有心的朋友可以留意到微软在构建一个类时，很多时候用到了对象组合的方法。比如 asp.net中，Page类，有Server Request等属性，但其实他们都是某个类的对象。使用Page类的这个对象来调用另外的类的方法和属性，这个是非常基本的一个设计原则

## in/out/ref区别？

**in关键字**：通过引用传递参数，让形参成为实参别名，修饰必须是变量。即对形参执行的任何操作都是对实参执行的。类似ref/out，不同在于in参数无法通过调用的方法进行修改；
**out关键字**：通过引用传递参数，让形参成为实参别名，修饰必须是变量。与ref相似，只不过ref要求在传递之前就初始化变量。也与in类似，只不过in不允许通过方法来修改参数值。若使用out，方法的定义和调用都必须显示使用out;
**ref关键字**：该类型的参数传递变量地址给方法（引用传递），传递前变量必须初始化，该类型与out型的区别在与： 1）.ref型传递变量前，变量必须初始化，否则编译器会报错,而out型则不需要初始化 2）.ref型传递变量，数值可以传入方法中，而out型无法将数据传入方法中。换而言之，ref型有进有出，out型只出不进。

```C++
class Test  
{  
    static void Main(string[] args)  
    {  
  
        InOutRef test = new InOutRef();  
        string outString = "10"; // out在函数外也可以赋值  
        string refString = "1"; // ref不初始化会报错  
        test.Test(1, out outString,ref refString);  
    }  
}  
  
class InOutRef  
{  
    public void Test(in int inTest ,out string outTest,ref string refTest)  
    {  
        outTest = "10"; // out在函数内不赋值会报错  
    }  
}
```

## C#中的委托

委托，使得可以将方法当作另一个方法的参数来进行传递。事件是一种特殊的委托。
### 1.委托的声明

**Delegate:**
Delegate 至少 0 个参数，至多 32 个参数，可以无返回值，也可以指定返回值类型。
```C++
public delegate int MethodtDelegate(int x, int y);//表示有两个参数，并返回int型。
```

**Action:**
Action 是无返回值的泛型委托。
Action 表示无参，无返回值的委托。
Action<int,string> 表示有传入参数 int,string 无返回值的委托。
Action<int,string,bool> 表示有传入参数 int,string,bool 无返回值的委托。
Action<int,int,int,int> 表示有传入 4 个 int 型参数，无返回值的委托。
Action 至少 0 个参数，至多 16 个参数，无返回值。

```C++
public void Test<T>(Action<T> action,T p)
{
 action(p);
}
```

**Func：**
Func 是有返回值的泛型委托
Func 表示无参，返回值为 int 的委托
Func<object,string,int> 表示传入参数为 object, string 返回值为 int 的委托
Func<object,string,int> 表示传入参数为 object, string 返回值为 int 的委托
Func<T1,T2,,T3,int> 表示传入参数为 T1,T2,,T3(泛型)返回值为 int 的委托
Func 至少 0 个参数，至多 16 个参数，根据返回值泛型返回。必须有返回值，不可 void

```C++
public int Test<T1,T2>(Func<T1,T2,int>func,T1 a,T2 b)
{
 return func(a, b);
}
```

**predicate：**
predicate 是返回 bool 型的泛型委托
predicate 表示传入参数为 int 返回 bool 的委托
Predicate 有且只有一个参数，返回值固定为 bool
```C++
public delegate bool Predicate<T> (T obj)
```

### 2.委托的使用

**Delegate:**
```C++
public delegate int MethodDelegate(int x, int y);
private static MethodDelegate method;
static void Main(string[] args)
{
    method = new MethodDelegate(Add);
    Console.WriteLine(method(10,20));
    Console.ReadKey();
}
private static int Add(int x, int y)
{
    return x + y;
}
```

**Action:**
```C++
static void Main(string[] args)
{
    Test<string>(Action,"Hello World!");
    Test<int>(Action, 1000);
    Test<string>(p => { Console.WriteLine("{0}", p); }, "Hello World");//使用Lambda表达式定义委托
    Console.ReadKey();
}

public static void Test<T>(Action<T> action, T p)
{
    action(p);
}

private static void Action(string s)
{
    Console.WriteLine(s);
}
```

可以使用 Action<T1, T2, T3, T4> 委托以参数形式传递方法，而不用显式声明自定义的委托。 封装的方法必须与此委托定义的方法签名相对应。 也就是说，封装的方法必须具有四个均通过值传递给它的参数，并且不能返回值。 （在 C# 中，该方法必须返回 void）通常，这种方法于执行某个操作。

**Func:**
```C++
static void Main(string[] args)
{
    Console.WriteLine(Test<int,int>(Fun,100,200));
    Console.ReadKey();
}

public static int Test<T1, T2>(Func<T1, T2, int> func, T1 a, T2 b)
{
    return func(a, b);
}

private static int Fun(int a, int b)
{
    return a + b;
}
```


**Predicate:**
泛型委托：表示定义一组条件并确定指定对象是否符合这些条件的方法。此委托由 Array 和 List 类的几种方法使用，用于在集合中搜索元素。
```C++
static void Main(string[] args)
{

    Point[] points = { new Point(100, 200),new Point(150, 250), new Point(250, 375),new Point(275, 395), new Point(295, 450) };
    Point first = Array.Find(points, ProductGT10);
    Console.WriteLine("Found: X = {0}, Y = {1}", first.X, first.Y);
    Console.ReadKey();

}

private static bool ProductGT10(Point p)
{
    if (p.X * p.Y > 100000)
    {
        return true;
    }
    else
    {
        return false;
    }

}
```

### 3.委托的特点

委托类似于 C++ 函数指针，但它们是类型安全的。
委托允许将方法作为参数进行传递。
委托可用于定义回调方法。
委托可以链接在一起；例如，可以对一个事件调用多个方法。
方法不必与委托签名完全匹配。

### 4.总结

Delegate 至少 0 个参数，至多 32 个参数，可以无返回值，也可以指定返回值类型
Func 可以接受 0 个至 16 个传入参数，必须具有返回值
Action 可以接受 0 个至 16 个传入参数，无返回值
Predicate 只能接受一个传入参数，返回值为 bool 类型

## 结构体和类的区别？

1.类-->引用类型，结构体-->值类型。
2.结构体不支持继承。
3.结构不能声明默认的构造函数。

常用数据结构时间复杂度
![[im_0.png]]

## 闭包

**概念：**
内层的函数可以引用包含在它外层的函数的变量，即使外层函数的执行已经终止。但该变量提供的值并非变量创建时的值，而是在父函数范围内的最终值

**条件：**
闭包是将一些执行语句的封装，可以将封装的结果像对象一样传递，在传递时,这个封装依然能够访问到原上下文

非必要条件： 1、嵌套定义的函数。 2、匿名函数。 3、将函数作为参数或者返回值。 4、在.NET中，可以通过匿名委托形成闭包：函数可以作为参数传递，也可以作为返回值返回，或者作为函数变量。而在.NET中，这都可以通过委托来实现。这些是实现闭包的前提。

## 反射

反射是.NET中的重要机制,通过反射,可以在运行时获得程序或程序集中每一个类型(包括类、结构、委托、接口和枚举等)的成员和成员的信息。有了反射,即可对每一个类型了如指掌。另外我还可以直接创建对象,即使这个对象的类型在编译时还不知道。

>反射的用途简要介绍

反射的用途大体总结如下,我们会在下面详细的进行介绍。

(1)使用Assembly定义和加载程序集,加载在程序集清单中列出模块,以及从此程序集中查找类型并创建该类型的实例。
(2)使用Module了解包含模块的程序集以及模块中的类等,还可以获取在模块上定义的所有全局方法或其他特定的非全局方法。
(3)使用ConstructorInfo了解构造函数的名称、参数、访问修饰符(如pulic 或private)和实现详细信息(如abstract或virtual)等。
(4)使用MethodInfo了解方法的名称、返回类型、参数、访问修饰符(如pulic 或private)和实现详细信息(如abstract或virtual)等。
(5)使用FiedInfo了解字段的名称、访问修饰符(如public或private)和实现详细信息(如static)等,并获取或设置字段值。
(6)使用EventInfo了解事件的名称、事件处理程序数据类型、自定义属性、声明类型和反射类型等,添加或移除事件处理程序
(7)使用PropertyInfo了解属性的名称、数据类型、声明类型、反射类型和只读或可写状态等,获取或设置属性值。
(8)使用ParameterInfo了解参数的名称、数据类型、是输入参数还是输出参数,以及参数在方法签名中的位置等。

**反射用到的主要类:**

**System.Type** 类--通过这个类可以访问任何给定数据类型的信息。
**System.Reflection.Assembly**类--它可以用于访问给定程序集的信息,或者把这个程序集加载到程序中。

System.Type 类对于反射起着核心的作用。但它是一个抽象的基类,Type有与每种数据类型对应的派生类,我们使用这个派生类的对象的方法、字段、属性来查找有关该类型的所有信息；
![[im_1.png]]

**Type类的属性和方法**

**属性：**

Name 数据类型名

FullName 数据类型的完全限定名(包括命名空间名)
Namespace 定义数据类型的命名空间名
IsAbstract 指示该类型是否是抽象类型

IsArray 指示该类型是否是数组

IsClass 指示该类型是否是类
IsEnum 指示该类型是否是枚举
IsInterface 指示该类型是否是接口
IsPublic 指示该类型是否是公有的
IsSealed 指示该类型是否是密封类
IsValueType 指示该类型是否是值类型

**方法：**

GetConstructor(), GetConstructors():返回
ConstructorInfo类型,用于取得该类的构造函数的信息
GetEvent(), GetEvents():返回EventInfo类型,用于取得该类的事件的信息
GetField(), GetFields():返回FieldInfo类型,用于取得该类的字段(成员变量)的信息
GetInterface(), GetInterfaces():返回InterfaceInfo类型,用于取得该类实现的接口的信息
GetMember(), GetMembers():返回MemberInfo类型,用于取得该类的所有成员的信息
GetMethod(), GetMethods():返回MethodInfo类型,用于取得该类的方法的信息
GetProperty(), GetProperties():返回PropertyInfo类型,用于取得该类的属性的信息可以调用这些成员,其方式是调用Type的InvokeMember()方法,或者调用MethodInfo, PropertyInfo和其他类的Invoke()方法。

反射具体使用

**1.定义一个测试反射的类**
![[im_2.png]]

**2.查看类的构造方法**
我们可以根据对象进而查看内部的构造函数以及参数。

```c#
NewClass nc = new NewClass();
Type t = nc.GetType();
ConstructorInof[] ci = t.GetConstructors();
foreach(var c:ConstructorInofo in ci) 
{
	Console.WriteLine("count");
	ParameterInfo[] ps = c.GetParameters();
	foreach(var pi:ParameterInfo in ps)//遍历并打印所有该构造函数的所有参数
	{
		Console.Write(pi.ParameterType.ToString() + "" + pi.Name + ",");
	}
	Console.WriteLine();
}
```

3.用构造函数动态生成对象

```C#
Type t = typeof(NewClass);
//参数数组，第一个参数是string,第二个是int
Type[] pt = new Type[2];
pt[0] = typeof(string);
pt[1] = typeof(int);

//根据参数类型获取构造函数，有可能有多个构造函数
ConstructorInfo ci = t.GetConstructor(pt);
object[] obj = new object[2] {"5","6"};

//调用构造函数,传递参数为obj
object o = ci.Invoke(obj);
//测试调用结果是否成功
((NewClass)o).show();
```

4.用Activator生成对象

```c#
Type t = typeof(NewClass);
//构造函数参数
object[] obj = new object[2]{"hello","world"};
//用Activator的CreateInstance静态方法，生成新的对象
object o = Activator.CreateInstance(t,params args:"grayworm","hi.baidu.com/grayworm");
//object o = Activator.CreateInstance(t,obj);
//object o = Activator.CreateInstance(t);
((NewClass) o).show();
```

上面代码中,Activator.CreateInstance第一个参数为需要创建对象的类型,后面的为调用构造函数的参数,上面的三种格式都可以,只是调用的构造函数不同而已。

5.查看类中的属性

```c#
NewClass nc = new NewClass();
Type t = nc.GetType();
PropertyInfo[] pis = t.GetProperties();
foreach(PropertyInfo pi in pis) {
	Console.WriteLine(pi.Name);
}
```

6.查看类中的Public方法

```c#
NewClass nc = new NewClass();
Type t = nc.GetType();
MethodInfo[] mis = t.GetMethods();

foreach(MethodInfo mi in mis) {
	Console.WriteLine(mi.ReturnType + "" + mi.Name);
}
```

7.查看类中Public字段
```c#
NewClass nc = new NewClass();
Type t = nc.GetType();

FieldInfo[] fis = t.GetFields();

foreach(Var fi:FieldInfo in fis) {
	Console.WriteLine(fi.Name);
}
```

8.用反射生成对象,并调用属性、方法和字段进行操作

```c#
NewCalss nc = new NewClass();
Type t = nc.GetType();

object obj = Activator.CreateInstance(t);
//取得ID字段
FieldInfo fi = t.GetField("a");
//给ID字段赋值
fi.SetValue(obj,value:"80");

//取得Name属性
PropertyInfo pi = t.GetProperty(name:"Name");
//给Name属性赋值
pi.SetValue(obj,"zhangsan",index:null);

PropertyInfo pi2 = t.GetProperty("Age");
pi2.SetValue(obj,"200",index:null);

//取得show方法
MethodInfo mi = t.GetMethod("show");
//调用show方法
mi.Invoke(obj,parameters:null)

```

9.System.Reflection.Assembly介绍

Assembly类可以获得程序集的信息,也可以动态的加载程序集,

以及在程序集中查找类型信息,并创建该类型的实例。使用Assembly类可以降低程序集之间的耦合,有利于软件结构的合理化。通过程序集名称返回Assembly对象
![[im_3.png]]

10.System.Reflection.Assembly使用

![[im_4.png]]

11.反射当前项目的类

![[im_5.png]]

## C# 方法和属性的访问权限修饰符

![[im_6.png]]

在C#中默认为private. default == private;

## Const和Static的区别

static 用于定义静态成员，适用于共享数据和逻辑，可被多个实例或对象访问。

const 用于定义编译时常量，适用于不可更改的常量值，不能被修改，也不需要实例化类即可访问。

## 什么是序列化和反序列化

序列化:对象 ——>可传输的格式(eg:json)

反序列化:可传输的格式(eg:json) —>对象

## string字符串相关

1.string为什么不能被继承？

string类无法被继承，因为它的修饰符是sealed（可以阻止其他类继承自该类）。

2.string和stringbuilder的区别

String声明之后在内存中大小是不可修改的（重复对单个字符串进行添加，修改的操作会显著的降低性能），而StringBuilder可以自由扩展大小。（两者的区别是内存空间是否可变）

3.string str1=string.Empty和string str2=""的区别

表示长度为0的空字符串。 此字段为只读。 public static readonly string Empty; string.Empty相当于长度为零的字符串 ""即str1=str2

4.string str=""和string str=null有什么区别？

前者分配内存为空字符串的空间，也就是长度为零的字符串；但后者根本就不分配内存空间。

5.string.Empty，""，null三者直接的区别

从运行结果可以看出string.Empty 和 "" 在栈和堆上都分配了长度为0的字符串空间，而 null 只在栈上分配了空间，在堆上没有分配，也即变量不引用内存中的任何对象。

6.string与System.String有区别吗？

在C#中，string关键字是String的别名。 因此，String 和 string 是等效的，虽然建议使用提供的别名 string，因为即使不使用 using System;，它也能正常工作。

## C#字符串的复制

两种方式Copy和CopyTo;

string.Copy(需要复制的字符串)。

System.CopyTo(要复制字符的起始位置(从第几个字符开始往后复制（不包括第几个字符）)，目标字符数组，目标数组中的开始存放位置，要复制的字符个数).

## 什么是泛型？

泛型是C#2.0版本和公共语言运行库 (CLR) 中的一个非常重要的新功能。

泛型就是类型参数化，用于处理的数据类型不固定的情况下，将类型作为参数传入。

使用泛型的好处？

- 代码复用：我们一套代码可以支持不同的类性。
- 降低了耦合性：代码逻辑和数据类型之间分离，实现了解耦。
- 更好的可读性：我们在使用集合的时候，定义了一个list 如List\<String>，一看便知道这个一个存放String类型的list。
- 程序性能提高：在一定的程度上减少了类型转换装箱与拆箱操作带来的性能损耗。