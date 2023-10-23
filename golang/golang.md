### 类型
布尔类型不能接受其他类型的赋值，不支持自动或强制的类型转换

```
//浮点数比较
import "math"
//p为用户自定义的比较精度，比如0.00001
func IsEqual(f1,f2,p float64) bool {
    return math.Fdim(f1,f2) < p


}
```

字符串(string)的内容不能在初始化被修改

```
//字符串通过range遍历
for i, v := range array {
    fmt.Println("i v")

}
```

**值类型**

Go中数组是一个值类型。值类型变量在赋值和作为参数传递时都产生一次复制动作，所以在函数体中无法修改传入的数组的内容，因为函数内操作的只是所传入数组的一个副本。



**数组切片**

数组切片的数据结构抽象以下3个变量：
1. 一个指向原生数组的指针；
2. 数组切片中的元素个数；
3. 数组切片已分配的存储空间


创建数组切片：基于数组和直接创建

基于数组----myArray[first:last]
直接创建
1. slice1 := make([]int, 5)
2. slice2 := make([]int,5,10)//预留10个元素的存储空间
3. slice3 := []int{1,2,3,4,5}

数组切片内置cap()和len()函数，cap()返回的是数组切片分配的空间大小

使用append()函数在尾端增加元素

`slice = append(slice,1,2,3)`

等同于
```
slice2 := []int{1,2,3}
slice = append(slice,slice2...)
```
append函数追加数据时，如果追加之后没有超出切片的容量，那么返回原来的切片，如果超出了切片的容量，返回一个新的切片，每次给切片扩容都会按照原有切片容量*2的方式扩容


和数组不同，切片只支持判断是否为nil,不支持==，!=判断

切片声明后不能直接使用，只有通过make或语法糖创建之后才会开辟空间，才能使用

字符串的底层是[]byte数组，所以字符串也支持切片的相关操作


**map**
1. 只要是可以做==、!=判断的数据类型都有可以作为key(数值，字符串，数组，指针，结构体，接口)
2. map的key不能是slice， map , function
3. map和切片一样容量都不是固定的，当容量不足时底层会自动扩容
4. 通过ok-idiom模式判断指定键值对是否存储
   ```
   if value, ok :=dict["key1"]; ok {

       fmt.Println("key1键值",value)
   }

   //遍历
   for key, value := range dict {
       fmt.Println(key,value)

   }
   ```


**匿名属性**
1. 没有指定属性名称，只有属性的类型，称之为匿名属性
2. 任何有命名的数据类型都可以作为匿名属性(int,float,bool,string,struct等)
3. 结构体未匿名属性，访问匿名属性的属性有两种方式：先找到匿名属性，再访问匿名属性中的属性，如stu.Person.name;直接访问匿名属性中的属性，
   如果多个匿名属性名称相同，不能使用第二种方式

```
type attribute struct {
    int
    bool
    float32

}

value := attribute{1,false,2.3}
nextvalue := attribute{
    int:1,
    bool:false,
    float32:2.3,
}

//结构体匿名属性
type Person struct {
    name string
    age  int
}
type Student struct {
    Person
    class string
}

stu := Student{
    Person{"sx",11},
    "one",
}
```




**指针**
1. golang的指针，不支持C语言中的+1 -1和++ --操作
2. 切片的本质就是一个指针指向数组，所以指向切片的指针是一个二级指针
3. 创建结构体指针两种方式： &structname {}; new(structname).
4. 使用结构体指针操作结构体属性：(*p).name; p.name
5. 不能将函数内指向局部变量的指针作为返回值，函数结束指向的空间会被释放；可以将函数内局部变量作为返回值，本质是拷贝一份








### string 与 []byte
[GO中[]byte和string转换](https://zhuanlan.zhihu.com/p/270626496)

要修改字符串(string类型)，可先将其转换成[]rune或[]byte，修改后的字符串存在于一块新分配内存空间
```
s := "abcd"
ns := []byte(s)
ns[0] = 'A'
fmt.Println(s)
fmt.Prinln(ns)

u := "字符"
us := []rune(u)
us[1] = '母'

fmt.Println(u)
fmt.Println(us)

输出：
abcd
Abcd

字符
字母

```

切片是共享内存的，即没有数据的复制
copy在两个切片之间复制数据，复制长度以len小的为准

copy第二个参数也可以是数组

```
s1 := []byte("hello golang")
s2 := make([]byte,5,10)
s3 := s1[:]
copy(s2,s1)
s3[5] = '+'
fmt.Println(string(s2))
fmt.Println(string(s1))
输出：
hello
hello+golang
```


### 运算符

**`:=` 只能用于定义局部变量，不能用于定义全局变量**



### 字符串操作
函数来自strings包

```
func Contains(s, substr string) bool
func ContainsAny(s, chars string) bool
func HasPrefix(s, prefix string) bool
func HasSuffix(s, suffix string) bool

```
如：
```
fmt.Println(strings.Contains("hello golang","go"))
//true
```

```
//相等0， 大于1， 小于-1
func Compare(a,b string) int

//会忽略大小写
func EqualFold(s, t string) bool

```

```
func Split(s, sep string) []string
func SplitN(s, sep string, n int) []string

func Join(a []string, sep string) string
```

```
//去除字符串两端指定字符
func Trim(s , cutset string) string

func TrimLeft(s, cutset string) string
func TrimRight(s, cutset string) string
func TrimSpace(s string) string
func TrimPrefix(s, prefix string) string
func TrimSuffix(s, suffix string) string

```


### 数值类型和字符串类型之间的转换
#### 数值类型转换字符串类型`strconv.Fromat***()`
```
strconv.FormatInt(int64(num),n)
```
#### 字符串类型转数值类型`strconv.Parse***()`
`num1, err := strconv.ParseInt(str1,10,8)`
第一个参数：需要转换的数据
第二个参数：转换为多少进制
第三个参数：转换为多少位整型
#### 字符串类型和整型快速转换

整型转换为字符串`strconv.Itoa(int(num1))`

字符串类型转换为整型`strconv.Atoi(str)`

数值类型转换字符串类型
```
//整型
fmt.Sprintf("%d",num1)
//float类型  
fmt.Sprintf("%f",num2)
//bool类型
fmt.Sprintf("%t",num3)

```


### os包和flag包获取命令行参数
```
flag.****(*type, name, value, usage)
第一个参数
```
`flag.StringVar(&name, "name","test","名字")`

`flag.IntVar(&age, "age",11,"年龄")`
`for...range`循环可以快速完成对字符串、数组、slice、map、channel遍历


### GO中引用类型和值类型
引用类型：指针，slice, map, channel
值类型：int系列，float系列， bool, string, 数组，结构体




### 匿名函数以及闭包
#### 匿名函数
```
//直接调用
func(s string) {
    fmt.Println(s)

}("hello")

//保存到变量
f := func(s string) {
    fmt.Println(s)
}
f("hello")

//作为参数

test(func(s string) {
    fmt.Println(s)
})

func test(f func(s string)) {
    f("hello go")

}


//作为返回值

```

#### 闭包


### init函数以及main函数
golang有两个保留函数、

1. init函数(能够用于所有的package)
2. main函数(只能应用于package main)
3. 这两个函数在定义时不能有任何的参数和返回值
4. go程序会自动调用这两个函数，所以不能在任何地方调用这两个函数
5. package main必须包含一个main函数，但是每个package中的init函数都是可选的
6. 一个package可以写任意多个init函数，但是建议一个package中每个文件只写一个init函数
7. 单个保重代码执行顺序
```
main包--->常量--->全局变量--->init函数--->main函数--->Exit
```

8. 多个包之间代码执行顺序
   
![多个包之间代码执行顺序](%E5%A4%9A%E4%B8%AA%E5%8C%85%E4%B9%8B%E9%97%B4%E4%BB%A3%E7%A0%81%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F.png)

9. init函数用于处理当前文件的初始化操作，在使用某个文件时的一些准备工作应该在这里完成



### 类型(model)

1. 两种不同类型的整型数是不能直接比较

2. 小写字母开头的函数只在本包中可见，大写字母开头的函数才能被其他包使用，也适用于类型和变量的可见性

3. 未进行显式初始化的变量都会被初始化为该类型的零值


4. 初始化某类型的对象实例几种方法如下：
```
type Rect struct {
    x, y          float64
    width, height float64
    
    
}

rect1 := new(Rect)
rect2 := &Rect{}
rect3 := &Rect{0,0,100,200}
rect4 := &Rect{width:100, height:200}
```
Go中，没有构造函数，对象的创建通常由一个全局的创建函数来完成，以New****来命名，表示“构造函数”

```
func NewRect(x,y,width,height float64) *Rect {
    return &Rect{x,y,width,height}
    
}
```


### interface类型
interface定义了一组方法，如果某个对象实现了某个接口的所有方法，则此对象就实现了此接口。

接口中只能有方法不能有字段

接口中嵌入接口时不能出现相同的方法名称

空接口类型可以接收任意类型数据
```
var i interface{}
i = 1

i = 1.2

i = map[string]string{"1":"2"}
```

只要是自定义类型就可以实现接口
```
type usber interface {
    start()
    stop()

}

type Computer struct {
    name  string
    model string
}

func (cm Computer) start() {
    fmt.Println("start computer")
}

func (cm Computer) stop() {
    fmt.Println("stop computer")
}

func working(u usber) {
    u.start()
    u.stop()
}

func main() {
    cm := Computer{"lenovo","123"}
    working(cm)

    var i usber
    i = Computer{"mac","1234"}

}

```

接口类型变量可以接收实现了该接口类型的变量，但是只能调用该变量中的方法，不能访问该变量的属性


### 面向对象

#### 继承
golang中所谓的继承其实是利用组合实现的(匿名结构体属性)

1. 子类不仅仅能够继承父类的属性，还能够继承父类的方法
2. 无论是属性继承还是方法继承，都只能子类访问父类，不能父类访问子类

#### 多态
golang的多态是采用接口来实现的
```
package main

import "fmt"
//1.定义接口
type Animal interface { 
	Eat()
}
type Dog struct { 
	name string 
	age int
}
// 2.实现接口方法
func (d Dog) Eat() { 
	fmt.Println(d.name, "正在吃东西")
}

type Cat struct { 
	name string 
	age int
}
// 2.实现接口方法
func (c Cat) Eat() { 
	fmt.Println(c.name, "正在吃东西")
}
// 3.对象特有方法
func (c Cat) Special() { 
	fmt.Println(c.name, "特有方法")
}


func main() {
// 1.利用接口类型保存实现了所有接口方法的对象

    var a Animal
	a = Dog{"旺财", 18}
// 2.利用接口类型调用对象中实现的方法a.Eat()
	a = Cat{"喵喵", 18} 
    a.Eat()
// 3.利用接口类型调用对象特有的方法
    a.Special() 
    // 接口类型只能调用接口中声明的方法, 不能调用对象特有方法
	if cat, ok := a.(Cat); ok{
		cat.Special() // 只有对象本身才能调用对象的特有方法
	}
}


```

多态优点：
1. 简化了编程接口，类与类之间重用

[接口用例](https://zhuanlan.zhihu.com/p/30625125)

```
type Human struct {
    name  string
    age   int
    phone string

}

type Student struct {
    Human    //匿名属性
    school   string
    loan     float32
}

type Employee struct {
    Human
    company  string
    money    float32

}

func (h *Human) SayHi() {

}

func (h *Human) Sing(song string) {


}

//Employee重载SayHi
func (e *Employee) SayHi() {


}

func (s *Student) BorrowMoney(amount float32) {

    s.loan += amount

}

func (e *Employee) SpendSalary(amount float32) {

    e.money -= amount
}

//interface Men被Human,Student和Employee实现
type Men interface {
    SayHi()
    Sing(song string)

}

type YoungChap interface {

    SayHi()
    Sing(song string)
    BorrowMoney(amount float32)
}

type ElderlyGent interface {
    SayHi()
    Sing(song string)
    SpendSalary(amount float32)

}
```
可以看出，interface可以被任意对象实现；一个对象可以实现任意多个interface；任意的类型都实现了空interface（interface{}）

如果定义了一个interface的变量，那么这个变量里面可以存实现这个interface的任意类型的对象

```
func main() {
    mike := Student{Human{"Mike",25,"12xxx"},"MIT",0.00}
    paul := Employee{Human{"Paul",23,"13xxx"},"Golang Inc",1000}

    var m Men
    
    //m 能够存储Student，也能够存储Employee
    m = mike
    m.SayHi()
    m.Sing("never give up")

    //定义slice Men
    x := make([]Men,2)
    x[0],x[1] = mike,paul

    for _, value := range x {
        value.SayHi()

    }

}
```

interface就是一组抽象方法的集合，必须由其它非interface类型实现，而不能自我实现。
空interface可以用在需要存储任意类型的数值，因为它可以存储任意类型的数值；一个函数把interface{}作为参数，那么它可以接受任意类型的值作为参数，如果一个函数返回interface{},那么它也就可以返回认证类型的值。
```
var a interface{}
var i int =5
s := "hello golang"

a = i
a = s
```

### 占位符
```
type tests struct {
    content string
    
}

words := tests{content:"hello golang"}
```
| 占位符 | 说明 | 举例  | 输出|
|----|--- |---|---|
| %v| 默认格式|Printf("%v",words) | {hello golang} |       |
|%+v|打印结构时，添加字段名||{content:"hello golang"}|
|||||
|||||
|||||
|||||

- 布尔占位符


- 整数占位符

| 占位符 | 说明 | 举例  | 输出|
|----|--- |---|---|
|%b|二进制|Printf("%b",5)|101|
|%c|Unicode表示的字符|Printf("%c",0x4E2D)|中|
|%d|十进制|Printf("%d",0x12)|18|
|%q|单引号表示的字符字面值|Printf("%q",0x4E2D)|'中'|
|%x, %X|十六进制表示|Printf("%x %X",10,10)|a A|
|%U|Unicode格式，U+****,等同于U+%04X|Printf("%U",0x4E2D)|U+4E2D|

- 字符串与字节切片

| 占位符 | 说明 | 举例  | 输出|
|----|--- |---|---|
|%s|string或[]byte类型字符串输出|Printf("%s",[]byte("go语言"))|go语言|
|%q|双引号字符串|Printf("%q","go语言")|"go语言"|
|%x,%X|十六进制，每字节两个字符|Printf("%x %X","go","go")|676f 676F|

- 指针

| 占位符 | 说明 | 举例  | 输出|
|----|--- |---|---|
|%p|十六进制表示，前缀0x|Printf("%p",&words)|0x******|


### reflect
要反射一个类型的值（这些值都实现了空interface），reflect三个步骤如下：
1. 将该值转化成reflect对象，获取方式如下
```
t := reflect.TypeOf(i) //得到类型的元数据，通过t获取类型定义里面的所有元素

v := reflect.ValueOf(i) //得到实际的值，还可以去改变值

```

2. 将reflect对象转化成相应的值，获取反射值能返回相应的类型和数值

```
tag := t.Elem().Field(0).Tag  //获取定义在struct里面的标签
name := v.Elem().Field(0).String() //获取存储在第一个字段里面的值

```
3. 反射的字段必须是可修改的

```
var x float64 = 3.4
v := reflect.ValueOf(&x)
v := p.Elem()
v.SetFloat(7.1)

```

### 并发
goroutine是通过Go的runtime管理的一个线程管理器,其特点如下：

- 能够在单一的系统线程中模拟多个任务的并发执行
- 被动的任务调度方式，当一个任务正在执行时，外部没有办法中止它。要进行任务切换，只能通过由该任务自身调用yield()来主动出让CPU使用权
- 每个协程都有自己的堆栈和局部变量

每个协程都包含三种运行状态：挂起、运行和停止

#### channels
goroutine运行在相同的地址空间，因此访问共享内存要做好同步。
默认情况下，channel接收和发送数据都是阻塞的(非缓存类型)，除非另一端已经准备好。
Go也可以指定channel的缓冲大小 `ch := make(chan type, num)`
channel是线程安全的，即自带锁定功能
channel如果没有数据，再取就会报错；数据已满，再写就会报错

1. 单独在主线程中操作管道，写满了会报错，没有数据获取也会报错
2. 只要在协程中操作管道，写满了会阻塞，没有数据获取也会阻塞


```
package main

import "fmt"

func fib(n int, c chan int) {
	x, y := 1, 1

	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y

	}
	close(c)
}

func main() {

	c := make(chan int, 10)
	go fib(cap(c), c)

	for i := range c {
		fmt.Println(i)
	}

}

```

生产者通过关键字close函数关闭channel，关闭channel之后就无法再发送任何数据，消费者可以通过语法`v,ok := <-ch`测试channel是否被关闭，ok返回false说明被关闭。

#### select
针对多个channel的场景，通过select可以监听channel上的数据流动，select默认是阻塞的，只有当监听的channel中有发送或接收时才运行，当多个channel都准备好的时候，select是随机选择一个执行。
有时候出现goroutinue阻塞情况，可以利用select设置超时避免整个程序进行入阻塞情况

```
func main() {
    ca := make(chan int)
    ch := make(chan bool)

    go func() {
        for {
            select {
                case v := <-ca:
                    fmt.Println(v)

                case <-time.After(5*time.Second):
                    fmt.Println("timeout")
                    ch <- true
                    break

            }
        }
    }()
    <- ch
}
```

### 方法
1. golang中方法一般用于将函数和结构体绑定在一起，让结构体除了能够保存数据外还能具备某些行为
2. 方法中的结构体，通函数形参一样使用
```
func (st *structname) funcname(params type) retype {

}
```





### 文件操作
```
func Mkdir(name string, perm FileMode) error 

func MkdirAll(path string,perm FileMode) error //根据path创建多级子目录

func Remove(name string) error //当目录下有文件或者其他目录会报错

func RemoveAll(path string) error//如果path是单个名称，那么该目录不删除

func Create(name string)(file *File, err Error)//默认权限0666

func NewFile(fd uintptr, name string) *File//根据文件描述符创建一个文件对象

func OpenFile(name string, flag int, perm uint32)(file *File, err Error)


//写文件操作
func (file *File) Write(b []byte) (n int, err Error)

func (file *File) WriteAt(b []byte, off int64) (n int, err Error)//在指定位置开始写入

func (file *File) WriteStrintg(s string) (ret int, err Error)


//读文件操作
func (file *File) Read(b []byte) (n int, err Error)

func (file *File) ReadAt(b []byte,off int64) (n int, err Error)
```
