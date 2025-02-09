C++11

1. 如果声明一个变量而非定义它，就在变量名前添加关键字extern， 而且不要显示地初始化变量，变量能且只能被定义一次，但是可以被多次声明。 
2. 如果要在多个文件中使用同一个变量，就必须将声明和定义分离，此时，变量的定义必须出现在且只能出现在一个文件中，而其他用到该变量的文件必须对其进行声明

3. 引用本身并非一个对象，一旦定义了引用，就无法令其再绑定到另外的对象，之后每次使用这个引用都是访问它最初绑定的那个对象

4. 如果想在多个文件之间共享const对象，必须在变量的定义之前添加extern关键字

5. 允许为一个常量引用绑定非常量的对象、字面值，甚至是个一般表达式，与引用一样，也可以令指针指向常量或非常量。

6. 为了确保各个文件中类的定义一致，类通常被定义在头文件中，而且类所在头文件的名字应与类的名字一样。头文件通常包含那些只能被定义一次的实体，如类、const和constexpr变量

7. 头文件一旦改变，相关的源文件必须重新编译以获取更新过的声明

8. 避免在头文件中使用using,防止命名污染和冲突；显示使用命名空间，提高代码可读性和可维护性；在源文件中使用using，简化代码，同时限制作用域；使用命名空间别名或局部using,在需要时简化长命名空间名称

9. 如果一条表达式中已经有了size()函数，就不要再使用int了，这样可以避免混用int和unsigned可能带来的问题

10. 在 switch 的 case 后使用大括号 {} 是避免作用域冲突和编译错误的关键，尤其在涉及变量定义时。这一实践能显著提升代码的可靠性和可维护性。

11. 不要返回局部对象的引用或指针，因为函数完成后，它所占用的存储空间也随之释放掉

12. 递归函数中，一定有某条路径是不包含递归调用的







##

建议使用 for(;;) 结构来表示一个无限循环

Lambda

#### 引用和指针
1. 不存在空引用。引用必须连接到一块合法的内存。
2. 一旦引用被初始化为一个对象，就不能被指向到另一个对象。指针可以在任何时候指向到另一个对象。
3. 引用必须在创建时被初始化。指针可以在任何时间被初始化。

### vector
vector 是基于数组的数据结构，但它可以自动管理内存

### 类

一个派生类继承了所有的基类方法，但下列情况除外：

1. 基类的构造函数、析构函数和拷贝构造函数。
2. 基类的重载运算符。
3. 基类的友元函数。

当一个类派生自基类，该基类可以被继承为 public、protected 或 private 几种类型，几乎不使用 protected 或 private 继承，通常使用 public 继承

1. 公有继承（public）：当一个类派生自公有基类时，基类的公有成员也是派生类的公有成员，基类的保护成员也是派生类的保护成员，基类的私有成员不能直接被派生类访问，但是可以通过调用基类的公有和保护成员来访问。
2. 保护继承（protected）： 当一个类派生自保护基类时，基类的公有和保护成员将成为派生类的保护成员。
3. 私有继承（private）：当一个类派生自私有基类时，基类的公有和保护成员将成为派生类的私有成员。

#### 函数重载与运算符重载
在同一个作用域内，可以声明几个功能类似的同名函数，但是这些同名函数的形式参数（指参数的个数、类型或者顺序）必须不同。您不能仅通过返回类型的不同来重载函数

#### 多态
C++ 多态意味着调用成员函数时，会根据调用函数的对象的类型来执行不同的函数

#### 虚函数与纯虚函数
虚函数 是在基类中使用关键字 virtual 声明的函数。在派生类中重新定义基类中定义的虚函数时，会告诉编译器不要静态链接到该函数。

我们想要的是在程序中任意点可以根据所调用的对象类型来选择调用的函数，这种操作被称为动态链接，或后期绑定

想要在基类中定义虚函数，以便在派生类中重新定义该函数更好地适用于对象，但在基类中又不能对虚函数给出有意义的实现，这个时候就会用到纯虚函数

定义一个函数为虚函数，不代表函数为不被实现的函数。

定义他为虚函数是为了允许用基类的指针来调用子类的这个函数。

定义一个函数为纯虚函数，才代表函数没有被实现。

定义纯虚函数是为了实现一个接口，起到一个规范的作用，规范继承这个类的程序员必须实现这个函数

凡是含有纯虚函数的类叫做抽象类，这种类不能定义对象，只是作为基类为派生类服务，但可以定义指针和引用

除非在派生类中完全实现基类中所有的纯虚函数，否则，派生类也变成了抽象类，不能实例化对象。在派生类实现该纯虚函数后，定义抽象类对象的指针，并指向或引用子类对象。



面向对象的系统可能会使用一个抽象基类为所有的外部应用程序提供一个适当的、通用的、标准化的接口。然后，派生类通过继承抽象基类，就把所有类似的操作都继承下来。

外部应用程序提供的功能（即公有函数）在抽象基类中是以纯虚函数的形式存在的。这些纯虚函数在相应的派生类中被实现。

这个架构也使得新的应用程序可以很容易地被添加到系统中，即使是在系统被定义之后依然可以如此

### 模板



## string

在传递参数的时候，如果参数是string类型，可以用string类型的引用，减少内存的拷贝。


### 枚举类

限定作用域的枚举类

特点
1. 避免发生隐式转换

不允许发生任何隐式转换。如果非要转换，按就只能使用static_cast进行强制转换
2. 降低命名空间污染

在其他地方使用枚举中的变量就要声明命名空间

3. 可以前置声明

```
enum Color;			//错误
enum class Color;	//正确
```

### 类和数据

#### static类成员
每个static数据成员是与类关联的对象，并不与该类的对象关联

其优点如下：
1. static成员的名字在类的作用域中，可以避免与其他类的成员或全局对象名字冲突
2. 可以实施封装，static成员可以是私有成员，而全局对象不可以
3. 阅读程序清晰地显示static成员与特定类关联


类成员函数可以不用作用域操作符来引用类的static成员

static函数没有this指针，因为其是类的组成部分而不是对象的组成部分

static成员不是通过类构造函数进行初始化，而是应该在定义时进行初始化

像使用任意的类成员一样，在类定义体外部引用类的static成员时，必须指定成员是在哪个类中定义的，然而，static关键字只能用于
类定义体内部的声明中，定义不能标识为static

static成员数据成员通常在定义时初始化

const static数据成员在类的定义体中初始化时，该数据成员仍必须在类的定义体之外进行定义



### c++11 for循环

如果要在循环中修改数组或容器中的元素，可以使用引用类型
、、、
std::vector<int> iv(3);
for (auto &v : iv)
{
    v = std::rand();
}
、、、


所有容器的共通操作

1. == 和 != 运算符，返回true或false

2. = 将某个容器赋值给另一个容器

3. empty(), size(), clear()

4. begin(), end()

5. insert(), erase()


### deque
同vector相似，连续内存存储元素，对于最前端以及最后端元素的插入和删除操作，效率较高

push_back()和pop_back()会在末端插入和删除一个元素，push_front()和pop_front()在最前端插入和删除一个元素；pop_back()和pop_front()并不会获取元素的值，要想获取末端和最前端的元素的值，是用back()和front()函数


如果有必要为某个class编写copy constructor，那么同样有必要为它编写copy assignment operator

const对象和非const对象的调用规则

const对象只能调用const成员函数，不能调用非const成员函数。这是因为const对象的this指针是const类型的，无法传递给非const成员函数

非const对象可以调用const成员函数和非const成员函数。这是因为非const对象的this指针可以传递给任何类型的成员函数

成员函数只有在“不访问任何non-static 成员 ”的条件下才能被声明为static

保护成员不能被类的对象直接访问

任何类如果声明有一个（或多个）纯虚函数，那么，由于其接口的不完整性（纯虚函数没有函数定义，是谓不完整），程序无法为它产生任何对象。
这种类只能作为派生类的子对象使用，而且前提是这些派生类必须为所有虚函数提供明确的定义

如果父类中的虚函数是纯虚函数，则子类必须实现这个函数，否则子类也会变成抽象类，不能实例化

引用成员变量的初始化必须在构造函数的初始化列表中完成

在派生类中，为了覆盖基类的某个虚函数，而进行生命操作时，不一定得加上关键字virutal,编译器会依据两个函数的原型声明，决定某个函数是否会覆盖某积累中的
同名函数。


c++规范

- 函数入参是使用引用传递
- 尽量使用前缀++
- 构造函数中使用初始化列表
- 循环前尽量将变量定义在循环外面
- 不要将类的数据成员设为public，或者公开指向它们的指针或句柄

- 不要在一条语句中分配一个以上的资源，应该在自己的代码中执行显示的资源分配（如new），而且每次都应该马上将分配的资源赋予管理对象（比如shared_ptr）

- 避免使用宏