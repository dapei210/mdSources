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
slice = append(slice,1,2,3)
等同于
slice2 := []int{1,2,3}
slice = append(slice,slice2...)







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
### 字符串操作
函数来自strings包

`func Contains(s, substr string) bool`
如：
```
fmt.Println(strings.Contains("hello golang","go"))
//true
```

`func Join(a []string, sep string) string`

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

[接口用例](https://zhuanlan.zhihu.com/p/30625125)

```
type Human struct {
    name  string
    age   int
    phone string

}

type Student struct {
    Human
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
