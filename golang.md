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
