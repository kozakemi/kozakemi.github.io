---
title: MATLAB作业
id: '388'
tags:
  - MATLAB
  - 作业
categories:
  - 编程学习
abbrlink: 7c1c2be7
date: 2021-09-13 22:49:49
---

# 第一次作业

![img](https://kozakemi.oss-cn-beijing.aliyuncs.com/20210913224411.jpeg)

```
%清理系统
clear
clc
close all
%矩阵
A=[23,10,-0.778,0;41,-45,65,5;32,5,0,32;6,-9.54,54,3.14];
%前三行
B=A(1:3,:);%1:3代表取1至3行，列取全部
%前两列
C=A(:,1:2);
%右下角3*2
D=A(2:4,3:4);
%B*C
E=B*C;
%题目
E<D
E&D
ED
~E~D
find(A>=10&A<25)
```

# 第二次作业

![img](https://kozakemi.oss-cn-beijing.aliyuncs.com/20210913224525.jpeg)

```
%清理
clear
clc
close all
%单位矩阵
I=eye(3);
%已知
A=[-1 5 -4;0 7 8;3 61 7];
B=[8 3 -1;2 5 3;-3 2 0];
%1)
L1=A+6*B
%2)
L2=A*B
L21=A.*B
L23=B*A
%3)
L3=A/B
L31=B\A
%4)
L3=[A,B]
L31=[A([1,3],:);B^2]
```

# 第三次作业

![img](https://kozakemi.oss-cn-beijing.aliyuncs.com/20210913224557.jpeg)

```
%清理
clear
clc
close all
%原始数据
A=[2004 98 94;2005 105 97;2006 121 110];
%平均
p=mean(A,1);
char=sprintf('平均新生数');
disp(char)
P1=p(:,2);
disp(P1)
char=sprintf('平均毕业生数');
disp(char)
P2=p(:,3);
disp(P2)
%加和
s=sum(A(:,2:3));
S1=s(:,1);
char=sprintf('毕业生总数');
disp(char)
S2=s(:,2);
disp(S2)
%比较
m=max(A(:,2:3),[],2);
[i,j]=size(A);
char=sprintf('年份');
disp(char)
for i=1:j
    if m(i)==A(i,2)
       COM= A(i,1);
       disp(COM)
    end
end
%毕/新 -
for i=1:j
avg(i)=A(i,3)/A(i,2);
end
char=sprintf('比例平均值');
disp(char)
AVG= sum(avg,'all')/3;
disp(AVG)
```