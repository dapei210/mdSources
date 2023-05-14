#### python中的r'',b'',u'',f''含义
1. r''作用是去除转义字符
2. b''表示bytes对象
3. u''表示unicode格式编码
   
###
str()
int()

None, '', 0,[],{},(),False都被判断为空值


### 列表
1. 列表追加元素data.append(***)

2. 列表插入元素data.insert(num,***)

3. 列表删除元素del data[num]

4. 使用pop()删除元素，并能获取到删除的值：deldata = data.pop(),弹出栈顶元素(即删除末尾元素)
pop(num)删除任意位置元素

5. 根据值删除元素data.remove(***): remove只删除第一个指定的值，如果出现多次，循环删除

6. 列表永久排序data.sort(), data.sort(reverse=true)  ; 临时排序tmpdata = sorted(data)

7. 从后往前打印列表data.reverse()， 永久修改顺序

8. list创建数字列表list(range(num1, num2(,step)))

9. squares = [value**2 for value in range(1,11)]

10. 复制列表，使用cpdata = data[:]

11. 检查特定值是否包含在列表中 in, not in

12. 列表是否为空 if data:


### 字典
1. 删除键值对 del datamap['key']
2. 遍历字典 for key, value in datamap.items():   遍历字典所有键： for key in datamap.keys():  按顺序遍历字典中的所有键： for key in sorted(datamap.keys()):   遍历字典中的所有值：for value in datamap.values():











