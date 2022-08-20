### encoding/base64包



### encoding/hex包
```
//将src编码为16进制字符串
func EncodeToString(src []byte) string

//将字符串s转化为slice
func DecodeString(s string) ([]byte, error)
```