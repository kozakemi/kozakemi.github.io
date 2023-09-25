---
title: Matlab字符串
id: '405'
tags:
  - MATLAB
  - 字符串
categories:
  - 编程学习
abbrlink: f92121b4
date: 2021-09-17 23:47:26
---

# 字符串

## 建立字符串

使用‘ ’创建字符串

```
>>str1='abcd'
>>str2='eigh'
```

如果要括起来的内容中有 ‘ ，则使用连续的 ’‘ 表示单引号

```
>>sentence = 'I''ve got a date!'
```

```
sentence =

    'I've got a date!'
```

## 连接两个字符串

使用\[ \]连接多个字符串

```
>>str1='abcd'
>>str2='eigh'
>>str3=[str1 str2]
>>%或者str3=[str1,str2]
```

```
str3 =
    'abcdeigh'
```

## 获取字符串长度

```
>>str1='abcd';
>>length(str1)
```

```
ans =
     4
```

特殊的

```
>>sentence = 'I''ve got a date!';
>>length(sentence)
```

```
ans =
    16
```

## 字符串转ASCII码

### 1.使用double()

#### 语法

```
Y = double(X) 
```

X - 输入数组标量 向量 矩阵 多维数组

#### 说明

Y = double(X) 将 X 中的值转换为双精度。

#### 示例

```
>>sentence = 'I''ve got a date!'
>>sentenceAscii = double(sentence) 
```

```
sentenceAscii =
    73    39   118   101    32   103   111   116    32    97    32   100    97   116   101    33
```

### 2.使用abs()

#### 语法

```
Y = abs(X) 
```

X - 输入数组

标量 向量 矩阵 多维数组

#### 说明

Y = abs(X) 返回数组 X 中每个元素的绝对值。

如果 X 是复数，则 abs(X) 返回复数的模。

#### 示例

```
>>sentence = 'I''ve got a date!'
>>sentenceAscii = abs(sentence) 
```

```
sentenceAscii =
    73    39   118   101    32   103   111   116    32    97    32   100    97   116   101    33
```

## ASCII码转字符串

### 使用char()

#### 说明

C = char(A) 将数组 A 转换为字符数组。

A - 输入数组  
数值数组 字符数组 字符数组的元胞数组 分类数组 字符串数组

#### 示例

```
>>sentence = 'I''ve got a date!';
>>sentenceAscii =abs(sentence) ;
>>sentence2=char(sentenceAscii)
```

```
sentence2 =
    'I've got a date!'
```

## 判断数据类型

### 1.使用class()

#### 语法

```
className = class(obj)
```

obj - 对象或文字  
变量或表达式

#### 说明

className = class(obj) 返回 obj 的类的名称。

#### 示例

```
>>sentence = 'I''ve got a date!';
>>out1=class(sentence)
```

```
out1 =
    'char'
```

### 2.使用ischar()

#### 语法

```
tf = ischar(A)
```

A - 输入数组  
标量 向量 矩阵 多维数组

#### 说明

如果 A 为字符数组，则 tf = ischar(A) 返回逻辑值 1 (true)；否则返回逻辑值 0 (false)。

#### 示例

```
>>sentence = 'I''ve got a date!';
>>out1=ischar(sentence)
```

```
out1 =
  logical
   1
```

# 一个字符数组变量存储多行字符串

## 1.使用二维字符数组

使用 ; 进行分行，但是建立时需要将短的字符串使用空格补全至相同长度

```
>>departments= ['ee  '; 'cs  '; 'econ']
```

```
departments =
  3×4 char 数组
    'ee  '
    'cs  '
    'econ'
```

## 2.使用char()

优点:省去补空格的步骤

注意行间使用 , 分隔

```
>>departments= char('ee','cs','econ')
```

```
departments =
  3×4 char 数组
    'ee  '
    'cs  '
    'econ'
```

注意：从二维字符数组访问字符串时，切记要使用 deblank 指令来清除字符串尾部的空格字符

### 清除字符串尾部的空格字符

使用deblank()

#### 语法

```
newStr = deblank(str)
```

str - 输入文本  
字符串数组 字符数组 字符数组的元胞数组

#### 说明

删除尾部的空格

#### 示例

```
departments = char('ee', 'cs', 'econ');
dept1 = departments(1,:); dept2 = deblank(dept1);
len1 = length(dept1) len2 = length(dept2)
% (1,:)代表第一行的全部元素 % 使用 deblank 指令清除尾部的空格字符
% 显示变量 dept1 的长度=4 % 显示变量 dept2 的长度=2
```

# 字符串的操作

## 比较字符串的內容的异同

### strcmp()

#### 语法

```
tf = strcmp(s1,s2)
```

#### 说明

tf = strcmp(s1,s2) 比较 s1 和 s2，如果二者相同，则返回 1 (true)，否则返回 0 (false)。如果文本的大小和内容相同，则它们将视为相等。返回结果 tf 的数据类型为 logical。  
s1,s2 - 输入文本  
字符向量 字符数组 字符向量元胞数组 字符串数组

输出参数  
tf - True 或 false 结果  
1 0 逻辑数组

#### 示例

```
>>str1 = 'today';
>>str2 = 'tomorrow';
>>out1 = strcmp(str1, str2)
```

```
out1 =
  logical
   0
```

#### 其他

strncmp:比较两个字符串的前n个字符是否相同;

```
tf = strncmp(s1,s2,n)
```

strcmpi:与strcmp类似，但strcmpi会忽略字母大小 写的区别;

```
tf = strcmpi(s1,s2)
```

strncmpi:与strncmp类似，但strncmpi会忽略字母 大小写的区别;

```
tf = strncmpi(s1,s2,n)
```

也可使用 == 但长度必须相同

```
>>str1 = 'today';
>>str2 = 'today';
>>str1==str2
```

```
ans =
  1×5 logical 数组
   1   1   1   1   1
```

## 判别字符串中字符类别

isletter:判断字符串中的每个字符是否为英文字母;

isspace:判断字符串中的字符是否属于格式字符(空 格、制表符、回车和换行等);

isstrprop:逐字符检测字符串里的字符是否属于指定 范围(字母、字母和数字、大写或小写、十进制数、 十六进制数等)。

strfind(str, pattern):在字符串str中查找pattern子串，返回字串出现位置;

findstr(str1, str2):查找字符串str1和str2，返回较短 字符串在较长字符串中出现的位置;

​ 此语法比上一条语法更加易用

```
>> str1 = 'hi matlab2010!'; >> str2 = 'mat';
>> strfind(str1,str2)
```

```
ans = 
  4
```

## 格式化字符串

sprintf:按照指定格式将数据写入输出字符串;  
fprintf:按照指定格式将数据写入输出文件或命令 窗口;  
warning:在警告信息中显示格式化数据;  
error:在错误信息中显示格式化的数据;  
assert:在条件不满足时，生成错误信息;  
MException:捕获错误信息。

![截屏2021-09-17 下午11.44.18](https://kozakemi.oss-cn-beijing.aliyuncs.com/20210917234421.png)