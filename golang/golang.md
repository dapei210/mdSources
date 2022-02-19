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


### interface类型
interface定义了一组方法，如果某个对象实现了某个接口的所有方法，则此对象就实现了此接口。

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
goroutine是通过Go的runtime管理的一个线程管理器

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
