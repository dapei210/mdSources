### du命令
查找根目录下最大目录/文件(包括子文件夹)

`du -Sh / | sort -rh | head -n10`


### linux网络相关命令
#### 


### sed, awk and grep
grep更适合单纯的查找或匹配文件
sed更适合编辑匹配到的文本
awk更适合格式化文本，对文本进行较复杂格式处理

#### sed
sed是一种流编辑器，每次处理一行的内容。处理时，把当前处理的行存储在临时缓冲区中，成为“模式空间”，接着用sed命令处理缓冲区中的内容


### shell
1. 双引号可以有变量
2. 双引号可以出现转义字符

获取字符串长度`${#string}`

#### 数组操作
array_name=(value0 value1 value2)

读取数组元素`valuen=${array_name[n]}`

获取数组中所有元素`${array_name[@]}`

获取数组的长度`length=${#array_name[@]}`

获取单个元素的长度`${#array_name[n]}`




