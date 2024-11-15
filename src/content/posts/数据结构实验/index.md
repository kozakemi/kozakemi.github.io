---
title: 数据结构实验
published: 2021-09-24 00:41:06
tags: ['C语言', 数据结构]
category: 编程学习
draft: false
---

# 线性表

## 顺序表：

```
#include<bits/stdc++.h>
using namespace std;
/*
 建立表
 */
struct sqlist{
    int elem[100];
    int length;
};
/*
 初始化
 */
void initSqlist(sqlist & L){
    L.length=0;
}
/*
 输出
 */
void outputSqlist(sqlist L){
    int i;
    for(i=0;i<L.length;i++){
        cout<<L.elem[i]<<' ';
    }
}
/*
 建立有数据的顺序表
 */
void createSqlist(sqlist &L,int len){
    int i;
    L.length=len;
    for (i=0; i<L.length; i++) {
        cin>>L.elem[i];
    }
}
/*
 插入元素
 */
void insertSqlist(sqlist &L,int i,int e){
    int j;
    for (j=L.length; j>=i; j--) {
        L.elem[j]=L.elem[j-1];
    }
    L.elem[i-1]=e;
    L.length+=1;
}
/*
main
 */
int main()
{
    sqlist myl;
    initSqlist(myl);
    createSqlist(myl,5);
    outputSqlist(myl);
    return 0;
}
```

## 链表：

```
#include<bits/stdc++.h>
using namespace std;
/*
 定义链表
 */
struct LNode{
    int data;
    LNode *next;
};
typedef LNode *LinkList;
/*
 建立链表
 */
void crtLinkList(LinkList &L,int len){
    //生成带空节点的空链表
    L=(LNode*)malloc(sizeof(LNode));
    L->data=NULL;
    LinkList ptail;
    ptail=L;
    LNode*p;
     int i;
    for (i=0; i<len; i++) {
        //生成节点，挂接在尾端
        p=(LNode*)malloc(sizeof(LNode));
        p->data=i+100;
//        cin>>p->data
        p->next=NULL;
        ptail->next=p;
        ptail=p;
        }
}
/*
 输出链表
 */
void outputeLinkList(LinkList L){
    LNode* p;
    p=L->next;
    while (p!=NULL) {
        cout<<p->data<<' ';
        p=p->next;
    }
}
int main()
{
    LinkList L;
    crtLinkList(L, 5);
    outputeLinkList(L);
    return 0;
}
```

```
#include<bits/stdc++.h>
using namespace std;
/*
 定义链表
 */
struct LNode{
    int data;
    LNode *next;
};
typedef LNode *LinkList;
/*
 建立链表(逆序输入)
 */

void CreateList_L(LinkList &L, int n){
int i=0;
L=(LinkList)malloc(sizeof(LNode));
L->next=NULL;
LNode* p;
for(i=n;i>0;--i){
p=(LinkList)malloc(sizeof(LNode));
cin>>p->data;
p->next=L->next;L->next=p;
}
}
/*
 输出链表
 */
void outputeLinkList(LinkList L){
    LNode* p;
    p=L->next;
    while (p!=NULL) {
        cout<<p->data<<' ';
        p=p->next;
    }
}
int main()
{
    LinkList L;
    CreateList_L(L, 5);
    outputeLinkList(L);
    return 0;
}
```

# 栈

```
#include<bits/stdc++.h>
using namespace std;
#define Stack_Init_Size 10
#define StackIncrement 10
typedef int ElemType;
typedef int Status;

typedef struct {
    ElemType *base; // 栈底
    ElemType *top; // 栈顶
    int stack_size; // 栈的最大长度
} SqStack;

 /*
  初始化栈
  */
Status InitStack(SqStack *S) {
    // 分配初始空间
    S->base = (ElemType *) malloc(Stack_Init_Size * sizeof(ElemType));
    if (!S->base) {
        exit(0);
    }
    S->top = S->base; //栈顶与栈底相同
    S->stack_size = Stack_Init_Size; // 栈的最大长度等于初始长度
    return 1;
}

/*
 判断栈空
 */
Status EmptyStack(SqStack *S) {
    return S->base == S->top;
}

/*
 栈长
 */
Status LengthStack(SqStack *S) {
    if (S->top == S->base) {
        return 0;
    }
    return (Status) (S->top - S->base);
}

/*
 栈顶
 */
Status GetTopStack(SqStack *S, ElemType *e) {
    if (S->top == S->base) {
        return 0;
    }
    *e = *(S->top - 1);
    return 1;
}

/*
 入栈
 */
Status PushStack(SqStack *S, ElemType e) {
    // 若栈的最大长度不会够用时，重新开辟，增大长度
    if (S->top - S->base >= S->stack_size) {
        S->base = (ElemType *)realloc(S->base, (S->stack_size + StackIncrement) * sizeof(ElemType));
        if (!S->base) {
            return 0;
        }
        // 栈顶指针为栈底指针加上栈之前的最大长度
        S->top = S->base + S->stack_size;
        // 栈当前的最大长度等于栈之前的最大长度与增加的长度之和
        S->stack_size += StackIncrement;
    }
    *S->top++ = e; // 先赋值，后栈顶指针上移
    return 1;
}
/*
 出栈
 */
Status PopStack(SqStack *S, ElemType *e) {
    if (S->base == S->top) {
        return 0;
    }
    *e = *--S->top; // 栈顶指针先下移，后赋值
    return 1;
}

/*
 销毁栈
 */
Status DestroyStack(SqStack *S) {
    free(S->base);
    S->base = S->top = NULL;
    S->stack_size = 0;
    return 1;
}

/*
 遍历
 */
Status StackTraverse(SqStack *S) {
    ElemType *p;

    if (S->top == S->base) {
        printf("Stack is NULL.\n");
        return 0;
    }
    p = S->top;
    // 由栈顶依次向下遍历
    while (p > S->base) {
        p--;
        printf("%d ", *p);
    }
    printf("\n");
    return 1;
}
/*
 main
 */

int main() {
    SqStack q, *S;
    S = &q;

    int i, n, e;
    InitStack(S);//初始化

    cout<<"入栈数目"<<endl;
    cin>>n;
    for (i = 1; i <= n; i++) {
        cin>>e;
        PushStack(S, e);//入栈
    }
    cout<<"栈长"<<LengthStack(S)<<endl;

    StackTraverse(S);//遍历输出

    cout<<"栈顶"<<GetTopStack(S, &e)<<endl;//栈顶元素
    DestroyStack(S);//销毁
    StackTraverse(S);
    return 0;
}
```

# 队列

```
#include<bits/stdc++.h>
using namespace std;
/*
节点定义
*/
struct QNode{
    int data;
    QNode *next;
};
typedef QNode *QueuePtr;
/*
 链队列首尾指针
 */
typedef struct {
    QueuePtr frot;
    QueuePtr rear;
}LinkQueue;

/*
带头节点的空队列
*/
void InitQueue(LinkQueue& Q){
    Q.frot=(QNode*)malloc(sizeof(QNode));
    Q.frot->next=NULL;
    Q.rear=Q.frot;
}
/*
输出队列
*/
void outputQueue(LinkQueue Q){
    QueuePtr p;
    p=Q.frot->next;
    while (p)
    {
        cout<<p->data<<' ';
        p=p->next;
    }
    
} 
/*
入队
*/
void EnQuene(LinkQueue& Q,int e){
    //新数据节点
    QueuePtr p;
    p=(QNode*)malloc(sizeof(QNode));
    p->data=e;
    p->next=NULL;
    //挂接在尾端
    Q.rear->next=p;
    Q.rear=p;
}
/*
出队
*/
void DeQueue(LinkQueue &Q,int &e){
    QNode *s;
    s=Q.frot->next;
    e=s->data;
    Q.frot->next=s->next;
    if (Q.rear==s)
    {
        Q.rear=Q.frot;
    }
    free(s);
}
/*
主函数
*/
int main(){
    int e;
    LinkQueue Q;
    InitQueue(Q);
    EnQuene(Q,3);
    EnQuene(Q,13);
    EnQuene(Q,23);
    EnQuene(Q,33);
    EnQuene(Q,43);
    outputQueue(Q);
    DeQueue(Q,e);
    cout<<endl;
    outputQueue(Q);
    return 0;
}
```

# 二叉树

```
#include <bits/stdc++.h>
using namespace std;
/*
*定义二叉树
*/
struct  BiTNode{
    int data;
    BiTNode* Lchild;
    BiTNode* Rchild;
};
typedef BiTNode* BiTree;
/*
*建立二叉树
*/
void crtBiTree(BiTree& t){
    int d;
    cin>>d;
    if(d==0){
        t=NULL;
    }else{
        //建立节点
        t=(BiTNode*)malloc(sizeof(BiTNode));
        t->data=d;
        crtBiTree(t->Lchild);
        crtBiTree(t->Rchild);
    }
}
//遍历
void preOrderTraverse(BiTree t){
    if(!t){
        return;
    }else{
        cout<<t->data<<' ';
        preOrderTraverse(t->Lchild);
        preOrderTraverse(t->Rchild);
    }
}

int main()
{
    BiTree t;
    crtBiTree(t);
    preOrderTraverse(t);
    return 0;
}
```

```
#include<bits/stdc++.h>
using namespace std;
struct BitNode{
    char data;
    BitNode* Lchild;
    BitNode* Rchild;
};
typedef BitNode* BiTree;
void crtTree(BiTree& t){
    int d;
    if(d=='#'){
        t=NULL;
    }else{
        t=(BiTree)malloc(sizeof(BitNode));
        t->data=d;
        crtTree(t->Lchild);
        crtTree(t->Rchild);
    }
}
void preOrderTraverse(BiTree t){
    if (!t)
    {
        return;
    }else{
        cout<<t->data<<' ';
        preOrderTraverse(t->Lchild);
        preOrderTraverse(t->Rchild);
    }
    
}
int main()
{
    BiTree t;
    crtTree(t);
    preOrderTraverse(t);
    return 0;
}
```

输出叶子节点

```
 void  treedown(BiTree t){
 if(t->Lchild==NULL &&t->Rchild==NULL){
 cout<<t->data;
 }
 } 
```