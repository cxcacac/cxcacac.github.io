---
layout: post
title:  "Python small tips memo"
date:   2020-06-03
categories: recorder
tags: Python
subtitle: python memo
---

# python tips

### func: enumerate

描述：enumerate() 函数用于将一个可遍历的数据对象(如列表、元组或字符串)组合为一个索引序列，同时列出数据和数据下标。

函数结构：`enumerate(sequence, [start=0])`，其中start是下标其实位置，sequence表示可迭代对象。

### dict.get()

描述：不同于对字典的直接访问，`dict.get()`函数返回指定键的值，不在字典中则会返回默认值，和`collections.defaultdict()`的效果一样。

函数结构: `dict.get(key, default=None)`，其中default代表找不到键时的默认返回对象。

### dict.setdefault()

描述：和`get()`返回默认值不同，如果键不存在于字典中，`setdefault()`方法会添加键并将其设置为默认值。

### list

（1）`[]==None `的结果是False，所以可以用`len(arr)`判定。

（2）可以对数组直接进行`+`运算来扩充数组，即`[1,2]+[3,4]=[1,2,3,4]`

（3）列表可以直接判定相等，即`[1,2,3]==[1,2,3]`的结果为`True`

### func: all()

描述：`all()`函数用于判定给定可迭代参数iterable中的所有元素是否都为True。

### 关于in的时间复杂度

所以能用hash表来判断的话最好，哈希表的时间复杂度还是比较低的。

python 字典底层是hash的，用in是$O(1)$

而普通的in是$O(n)$

对字典来说，`x in dict`, 其实类同于`x in dict.keys()`，就是对键进行查询。

### **isdigit函数判定**

有`isdigit()`函数和`isalpha()`函数，以及`isalnum()`函数。

之后还有`islower()`、`isnumeric()`、`isupper()`、`isTitle()`，都是用来判定字符串的组成。

### join()函数

`str.join(sequence)`就是将序列添加到字符串中。

### OS模块

具体的os模块的解释，可以看[链接](https://www.runoob.com/python/os-file-methods.html)

常用的： 

`os.getcwd()`：放回当前工作目录

`os.listdir()`：返回path指定的文件夹包含的文件或者文件夹的名字的列表。

`os.makedir(path)`：以创建一个名为path的文件夹。

`os.open()`：打开一个文件，并且设置需要的打开选项。

`os.path()`：获取文件的属性信息。

### **递归的时间复杂度**

若算法执行所需要的辅助空间相对于输入数据n而言是一个常数，则称这个算法空间复杂度辅助空间为$O(1)$；

递归算法空间复杂度：递归深度$n$乘以每次递归所要的辅助空间，如果每次递归所需要的辅助空间为常数，则递归空间复杂度$O(n)$。

递归用的栈的思想。

### ord()/chr()

```python
>>>print chr(0x30), chr(0x31), chr(0x61)   # 十六进制
0 1 a
>>> print chr(48), chr(49), chr(97)         # 十进制
0 1 a
```

