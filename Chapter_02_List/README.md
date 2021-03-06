## 第二章目录
>* <font size = 8 color = blue>[线性表](#线性表)</font>
>>* <font size = 8 color = blue>[顺序表](#顺序表)</font>
>>* <font size = 8 color = blue>[链表](#链表)</font>
>>>* <font size = 8 color = blue>[单链表](#单链表)</font>
>>>* <font size = 8 color = blue>[双(向)链表](#双向链表)</font>
>>>* <font size = 8 color = blue>[循环链表](#顺序表)</font>
>>>>* <font size = 8 color = blue>[单(向)循环链表](#循环链表)</font>
>>>>* <font size = 8 color = blue>[双(向)循环链表](#循环链表)</font>
-----------------------------

## 线性表(linear list，简称list)

### 定义
- 是**n(n≥0)**个**相同类型**的**数据元素**构成的**有限**序列。

#### 数学表示
- (a1，a2，…，an)
- 其中，n为线性表的长度，当n=0时，表示线性表是一个空表，即表中不包含任何元素。
- 对任意一对相邻元素<ai,ai+1>（1≤i<n），ai称为ai+1的**前驱**，ai+1称为ai的**后继**
-------------
#### 图示
<img width="400"  src="/Chapter_02_List/img/0.jpg"/>

#### 注意事项
- 线性表除第一个和最后一个元素之外，每一格数据元素只有一个前驱和一个后继
- 分有序线性表（顺序表）和无序线性表（链表），有序线性表的元素按照值的递增顺序排列；无序线性表在元素的值与位置之间没有特殊的联系
- 元素含义无所谓，可以指任何东西，但元素类型必须相同

------------------
#### 抽象数据类型ADT
```C
ADT List
{
    Data:
    Operation:
        InitList(&L)
        CreateList(&L)
        ListEmpty(L)
        ListLength(L)
        LocateElem(L,e)
        PriorElem(L,cur_e,&pre_e)
        NextElem(L,cur_e,&pre_e) 
        ListInsert(&L,i,e)
        ListDelete(&L,i,&e)
        GetElem(L,i,&e)
        ListTraverse(L)
        DestroyList(&L)
    }//ADT List
```
------------------
## 顺序表

#### 定义
- 顺序存储结构：用一组地址连续的存储单元依次存储**数据结构**(在这里是线性表)里各个元素，这种存储结构称为线性表的顺序存储结构。
- 顺序表：按照顺序存储结构存储的线性表。

#### 逻辑图示
<img width="600"  src="/Chapter_02_List/img/1.jpg"/>

-----------------------------
#### 数学表示
- 同线性表
- 求址
    - 每个元素占用k个存储单元，则<br>
    LOC(ai)= LOC(a1)+(i-1)*k   (1≤i ≤n)<br>
    LOC(ai+1)= LOC(ai)+k       (1≤i ≤n)<br>

#### 注意事项
- 访问顺序表中任意元素的时间都相等，具有这一特点的存储结构称为**随机存取结构**。

------------------
#### 顺序表静态存储数据结构
```C
[in SqList.h]
# define LIST_INIT_SIZE 100    //(默认)最大存储容量
typedef struct {
    ElemType  elem [LIST_INIT_SIZE];   //存储数据元素的一维数组 
    int length;                        //线性表的当前长度
} SqList;
```

#### 顺序表动态存储数据结构
```C
[in SqList.h]
# define LISTINCREMENT  10          //(默认)增补空间量
typedef struct {
    ElemType  *elem;           // 存储数据元素的一维数组
    int length;                // 线性表的当前长度
    int listsize;              // 当前分配的数组容量（以ElemType为单位）
    int incrementsize;        // 增补空间量（以ElemType为单位）
} SqList;
```
-----------------------------

#### 顺序表初始化操作
```C
[in SqList.h]
void InitList_Sq( SqList &L, int maxsize=LIST_INIT_SIZE, int incresize=LISTINCREMENT ) // 构造一个最大容量为maxsize的顺序表 L
{      
     L.elem=(ElemType *)malloc(maxsize*sizeof(ElemType)); // 分配一个最大容量为maxsize的数组空间
     if(!L.elem)  exit(1);           // 存储分配失败
      L.length=0;                    // 顺序表中当前所含元素个数为 0
      L.listsize=maxsize;            // 该顺序表可以容纳maxsize个数据元素
      L.incrementsize=incresize;     // 需要时可扩容incresize个元素空间
}// InitList_Sq
```
-----------------------------
#### 顺序表求表长操作
```C
[in SqList.h]
int ListLength_Sq(SqList L)
{ 
    return L.length;
}// ListLength_Sq
```
-----------------------------
#### 判断顺序表是否为空
```C
[in SqList.h]
bool ListEmpty_Sq(SqList L)
{
    if(L.length==0)
    return true;
    else return false;
}
```
-----------------------------
#### 顺序表定位元素(返回与e值相等的元素下标)
```C
[in SqList.h]
int LocateElem_Sq( SqList L, ElemType e) 
{  
    for(int i=0;i<L. length;i++)
    if(L.elem[i]==e)  return i;   // 找到满足判定的数据元素为第 i 个元素
    return -1;                     // 该线性表中不存在满足判定的数据元素
 }//LocateElem_Sq
```
-----------------------------
#### 顺序表前插
在顺序表L的第i个元素之**前**插入新的元素e，若表中当前容量不足，则按预定义的增量扩容
```C
[in SqList.h]
bool ListInsert_Sq(SqList &L, int i, ElemType e)
{   
    int j;
        if(i<0||i>L.length)  return false;  // i值不合法
        if(L.length>=L.listsize)           // 当前存储空间已满，增补空间
        {                   
            L.elem=(ElemType *)realloc(L.elem,(L.listsize+L.incrementsize)*sizeof(ElemType));
            if(!L.elem) exit(1);                   // 存储分配失败
            L.listsize+=L.incrementsize;           // 当前存储容量增加
        }
    for(j=L.length;j>i;j--)                // 被插入元素之后的元素左移
        L.elem[j]=L.elem[j-1];             
        
    L.elem[i]=e;                           // 插入元素e
    L.length++;                            // 表长增1
    return true;   
}// ListInsert_Sq
```
-----------------------------
#### 顺序表后插
在顺序表L的第i个元素之**后**插入新的元素e，若表中当前容量不足，则按预定义的增量扩容
```C
[in SqList.h]
bool ListInsert_Sq(SqList &L, int i, ElemType e)
{   
    int j;
        if(i<0||i>L.length)  return false;  // i值不合法
        if(L.length>=L.listsize)           // 当前存储空间已满，增补空间
        {                   
            L.elem=(ElemType *)realloc(L.elem,(L.listsize+L.incrementsize)*sizeof(ElemType));
            if(!L.elem) exit(1);                   // 存储分配失败
            L.listsize+=L.incrementsize;           // 当前存储容量增加
        }
    int target=i+1;
    for(j=L.length;j>target;j--)                // 被插入元素之后的元素左移
        L.elem[j]=L.elem[j-1];             
        
    L.elem[target]=e;                           // 插入元素e
    L.length++;                            // 表长增1
    return true;   
}// ListInsert_Sq
```
-----------------------------

#### 顺序表尾插
在顺序表L的最后一个元素之**后**插入新的元素e，若表中当前容量不足，则按预定义的增量扩容
```C
[in SqList.h]
bool ListInsert_Sq(SqList &L, int i, ElemType e)
{   
    int j;
        if(i<0||i>L.length)  return false;  // i值不合法
        if(L.length>=L.listsize)           // 当前存储空间已满，增补空间
        {                   
            L.elem=(ElemType *)realloc(L.elem,(L.listsize+L.incrementsize)*sizeof(ElemType));
            if(!L.elem) exit(1);                   // 存储分配失败
            L.listsize+=L.incrementsize;           // 当前存储容量增加
        }           
    L.elem[L.length]=e;                           // 插入元素e
    L.length++;                            // 表长增1
    return true;   
}// ListInsert_Sq
```

-----------------------------

#### 顺序表查插
在递增有序顺序表L中查找插入新的元素e，首先需要找到在哪插
```C
[in SqList.h]
bool ListInsert_Sq_1(SqList &L, ElemType e)
{   
// 若表中当前容量不足，则按预定义的增量扩容
      int i,j;
      if(L.length>=L.listsize)  {          // 当前存储空间已满，增补空间
          L.elem=(ElemType *)realloc(L.elem,(L.listsize+L.incrementsize)*sizeof(ElemType));
          if(!L.elem) exit(1);                   // 存储分配失败
          L.listsize+=L.incrementsize;           // 当前存储容量增加
        }
      for(i=0;i<L.length&&e>L.elem[i];i++); //让下标移动到应该插入的位置之前
	  for(j=L.length;j>i;j--)                // 被插入元素之后的元素左移
        L.elem[j]=L.elem[j-1];
      L.elem[i]=e;                           // 插入元素e
      L.length++;                            // 表长增1
      return true;   
}// ListInsert_Sq
```
-----------------------------

#### 顺序表删除元素操作
在顺序表L中删除第i个元素，并用e返回其值
```C++
[in SqList.h]
bool ListDelete_Sq(SqList &L,int i, ElemType &e)
{      
      int  j;
if(i<0||i>L.length) return false;            // i值不合法
if(L.length<=0)  return false;                // 表空无数据元素可删
     e=L.elem[i];                                  // 被删除元素的值赋给e
      for(j=i+1;j<=L.length-1;j++)                 // 被删除元素之后的元素前移
        L.elem [j-1]=L.elem [j];
      L.length--;                                  // 表长减1
      return true;    
}// ListDelete_Sq
```
-----------------------------

#### 顺序表取元素操作
取出顺序表L中第i个元素，并用e返回其值。
```C++
[in SqList.h]
bool GetElem_Sq(SqList L,int i, ElemType &e)
{     // 取出顺序表L中第i个元素，并用e返回其值。
     if(i<0||i>L.length) return false;            // i值不合法
if(L.length<=0)  return false;               // 表空无数据元素可取
e=L.elem[i];                                 // 被取元素的值赋给e
      return true;    
}// GetElem_Sq  
```
-----------------------------

#### 顺序表遍历输出各元素
```C++
[in SqList.h]
void ListTraverse_Sq(SqList L)
{
int i;
for(i=0;i<L.length;i++)
      cout<<setw(6)<<L.elem[i];
cout<<endl;
}// ListTraverse_Sq
```

-----------------------------
#### 删除顺序表
```C++
[in SqList.h]
void DestroyList_Sq(SqList &L)
{       // 释放顺序表L所占存储空间
      free(L.elem);
	  L.elem=NULL;
      L.listsize=0;
      L.length=0;
}// DestroyList_Sq
```
-----------------------------
#### 顺序表的可视化操作
将这个顺序表(用一个png文件)可视化展示出来,前提是需要安装graphviz并配置系统环境变量
```C++
void visualization(SqList L, char* filename)
{   int temp;
	FILE *stream;  
    if( NULL == (stream = fopen(filename, "w")) )  
    {  
	   printf("open file error");  exit(0);  
    }  
    fprintf(stream, "digraph\n{\nnode [shape = box];\n");  
	for(int i=0;i<L.length;i++)
	if(GetElem_Sq(L,i,temp))
    {
	    fprintf(stream, "box%d [label = \"%d\"];\n",i,temp);
	}
	fprintf(stream, "}"); 
	fclose(stream);  
	system("dot -Tpng showsqlist.dot -o showsqlist.png");
	system("showsqlist.png");
}
```
-----------------------------
#### 顺序表测试
```C++
[in SqListTest.cpp]
typedef int ElemType;    // 顺序表中元素类型为int
# include "stdio.h"      // 其实没有用
# include "stdlib.h"     // 该文件包含malloc()、realloc()和free()等函数
# include "iomanip.h"    // 该文件包含标准输入输出流cout和cin及控制符setw()等函数 
# include "SqList.h"     // 该文件中包含链表数据对象的描述及相关操作


int main()
{
  SqList mylist;
  int i,j,temp,forLocate,forGet,forInsert,forDelete;
  
  InitList_Sq(mylist, 50, 10);  
  for(i=0,j=1;i<10;i++,j++)
  if(!ListInsert_Sq(mylist,i, 2*j))
  {cout<<"错误!"<<endl; return 0;}
  cout<<"我创建了一个顺序表，表长为："<<ListLength_Sq(mylist)<<endl;
  cout<<endl<<"表中的元素依次为："<<endl;
  ListTraverse_Sq(mylist);
  
  
  cout<<endl<<"***现在执行元素定位操作,请输入需要查找的元素：***"<<endl;
  cin>>forLocate;
  j=LocateElem_Sq(mylist, forLocate);
  if(j!=-1) cout<<"找到了，它是第"<<j+1<<"个元素。"<<endl;
  else cout<<"定位失败！"<<endl;
  
  
  cout<<endl<<"***现在执行取元素操作,您要找第几个元素？请输入：***"<<endl;
  cin>>forGet;
  if(GetElem_Sq(mylist,forGet-1,temp))  
  cout<<"第"<<forGet<<"个元素的值是:"<<temp<<endl;
  else cout<<"取元素失败！"<<endl;
 
 
  cout<<endl<<"***现在执行插入操作,您要将什么元素插入顺序表？请输入：***"<<endl;
  cin>>forInsert;
  cout<<"***插入到第几个位置？***"<<endl;
  cin>>temp;
  if(!ListInsert_Sq(mylist, temp-1, forInsert))
  {cout<<"插入失败!"<<endl; return 0;} 
  else cout<<"插入成功，插入后的顺序表为："<<endl;
  ListTraverse_Sq(mylist);
  
 
  cout<<endl<<"***现在执行删除操作,您要删除第几个元素？请输入：***"<<endl;
  cin>>forDelete;
  if(!ListDelete_Sq(mylist,forDelete-1,temp))
  {cout<<"删除失败!"<<endl; return 0;} 
  else cout<<"删除成功，删除后的顺序表为："<<endl;
  ListTraverse_Sq(mylist);
 
  //  visualization(mylist,"showsqlist.dot");
 
  cout<<endl<<"***现在执行撤销操作***"<<endl;
  DestroyList_Sq(mylist);
  
 
  cout<<"***顺序表已经销毁！***"<<endl;
  return 0; 
 }
```
-----------------------------

## 链表

### 单链表

#### 定义
- 链式存储结构：用一组任意的存储单元存储**数据结构**（这里是线性表)里的各元素(这组存储单元可以是地址连续的，也可以是不连续的)，并且每个存储元素有一个数据域，一个（或多个）指针域，数据域用来存储元素内容，指针域用来反映元素的逻辑关系(前后位置关系)，这种存储方式成为链式存储（也叫非随机存取的存储结构）。
- 链表：按照链式存储结构存储的线性表。
- 单链表：各元素只有一个指针域的链表（只含有一个指针域）

#### 逻辑图示
<img width="600"  src="/Chapter_02_List/img/2.jpg"/>

- **头结点**：单链表中第一个结点(上图中的a1之前的结点a0)
- **表头指针**：存放单链表中第一个结点的地址的指针。(指向a0的指针)
- **开始结点**：存放单链表的第一个元素的结点。(a1)
- **表尾结点**：单链表中最后一个结点，表尾结点的指针域指针为空。(an)
-----------------------------

#### 数学表示
- 同线性表
- 寻址
  一般用p,q等字母表示操作性指针，例如<br> 
  如果 p = &a[i]，即 a[i] = p->data（或(*p).data）<br>
  那么<br>
   a[i]的下一个元素的地址是： p -> next （或 (*p).next）<br>
   a[i]的下一个元素的值是： p->next->data 

-----------------------------

#### 注意事项
- 链表的元素一般称为“结点”。
- 头结点在链表中并不是必须的，仅仅是为了操作上的方便。 
- 结点ai指其数据域为ai的结点，而p结点则指指针p所指向的结点(即其存储位置存在放在p中的结点)
- 单链表的操作

<img width="800"  src="/Chapter_02_List/img/3.jpg"/>

------------------
#### 单链表的数据结构
```C++
[in LinkList.h]
typedef struct Node {
    ElemType data;    //数据域
    struct  Node *next;   //指针域
}LNode,*LinkList;                 // LinkList为结构体指针类型
```
-----------------------------
#### 单链表的初始化
```C++
[in LinkList.h]
void InitList_L(LinkList &L)
    {  
		L=(LNode *)malloc(sizeof(LNode));  // 申请存放一个结点数据所需要的内在空间
		if(!L)   exit(1);             // 存储分配失败
        L->next=NULL;                // 表头结点的指针域置空
	}// InitList_L
```
-----------------------------
#### 求单链表的长度
// L为带头结点的链表的头指针，函数返回L所指链表的长度
```C++
[in LinkList.h]
int ListLength_L( LinkList L ) 
	{     
  		LinkList  p;
		int k=0;
		p=L->next;                             // p指向链表中的第一个结点
 		 while(p) 
  		 { k++;  p=p->next; }              // k计非空结点数
         return k;
	}// ListLength_L
```
-----------------------------
#### 单链表的定位操作
在L所指的单链表中查找第一个值和e 相等的结点，若存在，则返回其指针；
```C++
[in LinkList.h]
LNode *LocateElem_L( LinkList L,ElemType e) 
	{  
 		 LinkList  p;                           
 		 p=L->next;                               // p指向链表中的第一个结点
  		 while (p&&p->data!=e ) p=p->next;
  		 return p;
	}// LocateElem_L
```
-----------------------------
#### 单链表的定位操作
// 在L所指的单链表中查找第一个值和e相等的结点，若存在，则返回元素顺序(返回值+2为真实顺序),否则返回-1
```C++
[in LinkList.h]
int LocateElem_L_2( LinkList L,ElemType e) 
	{  
 		 int i=-1;
         LinkList  p;                           
 		 p=L->next;                             // p指向链表中的第一个结点
  		 while (p&&p->data!=e) {p=p->next;i++;} 
  		 return i;
	}// LocateElem_L_2
```
-----------------------------
#### 单链表插入元素操作
 在带有头结点的单链表L中的第i个结点之前插入元素e
 ```C++
[in LinkList.h]
bool ListInsert_L( LinkList &L, int i, ElemType e) 
	{   
		LinkList p,s;
  		int j;
  		p=L;  j=0;
  		while(p->next&&j<i-1)  { p=p->next; j++; }   // 寻找第i-1个结点,并让p指向此结点
  		if(j!=i-1)   return false;                   // i的位置不合理
  		if((s=(LNode *)malloc(sizeof(LNode)))==NULL) exit(1);  // 存储分配失败
  		s->data=e;  
  		s->next=p->next;  p->next=s;                  // 插入新结点
  		return true;
 	}// ListInsert_L
```
<img width="600"  src="/Chapter_02_List/img/insert.png"/>
-----------------------------

#### 单链表删除元素操作
 ```C++
[in LinkList.h]
bool ListDelete_L( LinkList &L, int i, ElemType &e) 
	{    // 删除带有头结点的单链表L中的第i个结点，并让e返回其值
		LinkList p,q;
	    int j;
  		p=L;  j=0;
 		while(p->next->next&&j<i-1){ p=p->next; j++; } //寻找第i-1个结点,并让p指向此结点
  		if(j!=i-1)   return false;                     // i的位置不合理
  		q=p->next;                                     // q指向其后继
		p->next=q->next;                               // 删除q所指结点
		e=q->data;    free(q);
  		return true;
 	}// ListDelete_L
```
<img width="600"  src="/Chapter_02_List/img/delete.png"/>

-----------------------------
#### 单链表取元素操作
取出单链表L中第i个元素，并用e返回其值
 ```C++
[in LinkList.h]
bool GetElem_L(LinkList L,int i, ElemType &e)
	{    // 取出单链表L中第i个元素，并用e返回其值
		LinkList p;
  		int j;
  		p=L;  j=0;
		while(p->next&&j<i){ p=p->next; j++; }  // 寻找第i个结点,并让p指向此结点
 		 if(j!=i)   return false;                      // i的位置不合理
  		 e=p->data;                                     // 被取元素的值赋给e
 		 return true;
    }// GetElem_L
```
-----------------------------
#### 创建单链表（尾插）
已知一维数组A[n]中存有线性表的数据元素，利用尾插法创建单链表L
 ```C++
[in LinkList.h]
void CreateList_L_Rear(LinkList &L,ElemType a[],int n ) 
	{   // 已知一维数组A[n]中存有线性表的数据元素，利用尾插法创建单链表L
   		LinkList p,q;   int i;
	  	L=(LinkList)malloc(sizeof(LNode));    // 创建立头结点
		q=L;                                // q始终指向尾结点，开始时尾结点也是头结点
		for(i=0;i<n;i++) 
    	{   p=(LinkList)malloc(sizeof(LNode));          // 创建新结点
            p->data=a[i];                               // 赋元素值
            q->next=p;                                  // 插入在尾结点之后
            q=p;                                         // q指向新的表尾
 	    }
   		q->next=NULL;                                    // 表尾结点next域置空
     }// CreateList_L_Rear
```
-----------------------------
#### 创建单链表（头插）
已知一维数组A[n]中存有线性表的数据元素，利用头插法创建单链表L
 ```C++
[in LinkList.h]
void CreateList_L_Front(LinkList &L,ElemType a[],int n ) 
	{    
   		LinkList p;   int i;
		L=(LinkList)malloc(sizeof(LNode));              //创建立头结点
		L->next=NULL;                               
		for(i=n-1;i>=0;i--)   
		{    p=(LinkList)malloc(sizeof(LNode));           //创建新结点
		     p->data=a[i];                                // 赋元素值
        	 p->next=L->next;                          // 插入在头结点和第一个结点之间
       	     L->next=p;                                         
        }
	}// CreateList_L_Front
 ```
-----------------------------
#### 遍历输出单链表各元素数据
 ```C++
[in LinkList.h]
 void ListTraverse_L(LinkList L)
	{
		 LinkList p=L->next;
 		 while(p)
  	  {  cout<< setw(6)<<p->data;
         p=p->next;
      }
		cout<<endl;
	}// ListTraverse_L
```
-----------------------------
#### 销毁单链表
 ```C++
[in LinkList.h]
void DestroyList_L(LinkList &L )
	{
  		LinkList p,p1;
 		 p=L;
  		while(p) 
  		{   p1=p;
    		p=p->next;
    		free(p1);
        }
  			 L=NULL;
    }// DestroyList_L
```
-----------------------------
#### 单链表的可视化
将这个单链表(用一个png文件)可视化展示出来,前提是需要安装graphviz并配置系统环境变
 ```C++
[in LinkList.h]
void ListVisualization_L(LinkList L, char* filename)
{   int temp=0;
    LinkList p=L;
	FILE *stream;  
    if( NULL == (stream = fopen(filename, "w")) )  
    {  printf("open file error");  cout<<"error!";  }  
    else if(p&&!(p->next))
    {
		fprintf(stream, " digraph g {\n rankdir=LR;\n node [shape = \"record\" width = 0.5];\nnode0  [label = \"<data> //// | <next>  ^\"];\n\n");
	}
	else if(p&&(p->next))
    {   fprintf(stream, "digraph g {\n rankdir=LR;\n node [shape = \"record\" width = 0.5];\nnode0  [label = \"<data> //// | <next>  \"];\n\n");
       
	   while(p->next)
	   {
	     fprintf(stream, 
		 "node%d [label =\"<data> %d| <next>\"]\n  node%d:next -> node%d:data;\n\n",temp+1, p->next->data,temp,temp+1);
		 p=p->next;
		 temp++;
	   }
    }
	fprintf(stream, "\n}"); 
	fclose(stream);  
	system("dot -Tpng showlinklist.dot -o showlinklist.png");
	system("showlinklist.png");
}
```
-----------------------------
#### 单链表测试
 ```C++
[in LinkList.h]
typedef  int ElemType;
# include "stdlib.h"     
# include "iostream.h"   
# include "iomanip.h"     
# include "LinkList.h"

int  main()
{
      LinkList mylist;
      int i,j,temp,forLocate,forGet,forInsert,forDelete,a[]={6,8,16,2,34,56,7,10,22,45};
      InitList_L(mylist);   
      cout<<"我初始化了一个头结点。"<<endl;
      CreateList_L_Front(mylist,a,10);          
      cout<<"我创建了一个带头结点的单链表，表长为："<<ListLength_L(mylist)<<endl;
      cout<<endl<<"表中的元素依次为："<<endl;
      ListTraverse_L(mylist);
      
      cout<<endl<<"<====现在执行定位元素操作,您要找的元素值？"<<endl;
      cout<<"请输入：";
      cin>>forLocate;
      j = LocateElem_L_2(mylist,forLocate);
      if(j!=-1&&GetElem_L(mylist,1,temp)) 
	  cout<<"找到了，它是第"<<j+2<<"个元素（其中，"<<temp<<"是第一个元素）====>"<<endl;
      else cout<<"没找到这个元素！"<<endl;
      
      
      cout<<endl<<"<====现在执行取元素操作,您要找第几个元素？"<<endl;
      cout<<"请输入：";
      cin>>forGet;
      if(GetElem_L(mylist,forGet,temp)) 
      cout<<"第"<<forGet<<"个元素的值是:"<<temp<<"====>"<<endl;
      else cout<<"元素不存在！"<<endl;
      
      
	  cout<<endl<<"<====现在执行插入操作,您要将什么元素插入顺序表？"<<endl;
	  cout<<"请输入：";
	  cin>>forInsert;
	  cout<<"插入到第几个位置？"<<endl;
	  cout<<"请输入：";
	  cin>>temp;
	  if(!ListInsert_L(mylist, temp, forInsert))
	  {cout<<"插入失败!"<<endl; return 0;} 
	  else cout<<"==>插入成功，插入后的链表为：====>"<<endl;
	  ListTraverse_L(mylist);
	  
	  
	  cout<<endl<<"<====现在执行删除操作,您要删除第几个元素？"<<endl;
	  cout<<"请输入：";
	  cin>>forDelete;
	  if(!ListDelete_L(mylist,forDelete,temp)) 
      {cout<<"删除失败!"<<endl; return 0;} 
	  else cout<<"删除成功，删除后的链表为：====>"<<endl;
	  ListTraverse_L(mylist);
	  
      //ListVisualization_L(mylist,"showlinklist.dot");
      
      cout<<endl<<"<====现在执行销毁操作***"<<endl;
      DestroyList_L(mylist);
      cout<<"销毁成功====>"<<endl;
  }
```
-----------------------------
### 双向链表
#### 定义
- 双（向）链表就是每个结点中含有两个指针域的链表，其中一个指针域存放其前趋结点的地址，另一个指针域存放其后继结点的地址。

#### 逻辑图示
<img width="600" src="/Chapter_02_List/img/4.jpg"/>

- **头结点**：双链表中第一个结点(上图中的a1之前的结点a0)
- **表头指针**：存放双链表中第一个结点的地址的指针。(指向a0的指针)
- **开始结点**：存放双链表的第一个元素的结点。(a1)
- **表尾结点**：双链表中最后一个结点，表尾结点的指针域指针为空。(an)

-----------------------------
#### 数学表示
- 同线性表
- 寻址
  一般用p,q等字母表示操作性指针，可用前驱指针域prior和后继指针域next访问表中任意节点，例如： <br>
  p = &a[i]（即a[i] = p->data）<br>
  那么<br>
  a[i]的上一个元素地址是 p -> prior , 上一个元素值是 p->prior->data<br>
  a[i]的下一个元素地址是 p -> next , 下一个元素值是 p->next->data<br>
  一般的，有<br>
  p->next-prior = p = p->prior->next

-----------------------------

#### 注意事项
- 节点p的存储地址既存放在其前驱结点的后继指针域中，也存在其后继节点的前驱指针域中。因此可随意在其上向前或向后移动，使得操作更加容易。
- 操作上比单链表更加便利，但存储开销增大。 
- 双(向)链表是非循环的，有首尾节点，注意与双向循环链表区别开来
-----------------------------
#### 双链表的数据结构
```C++
[in DuLinkList.h]
typedef struct DuNode {
    ElemType data;
    struct  DuNode *prior; //前驱指针域
    struct  DuNode *next;  //后继指针域
}DuLNode,*DuLinkList; 
```
-----------------------------
#### 双链表的初始化
```C++
[in DuLinkList.h]
void InitList_Dul(DuLinkList &L)
{  
    L=(DuLNode *)malloc(sizeof(DuLNode));
    if(!L)   exit(1);  
    L->next=NULL;     //后继指针置空 
    L->prior=NULL;   //前驱指针置空
}// InitList_Dul
```
-----------------------------
#### 求双链表的长度
// DL为带头结点的链表的头指针，函数返回L所指链表的长度
```C++
[in DuLinkList.h]
int ListLength_Dul(DuLinkList L ) 
	{     
  		DuLinkList  p;
		int k=0;
		p=L->next;                           
 		 while(p)             //当p所指向的结点还有值
  		 { k++;  p=p->next; }            
         return k;
	}// ListLength_Dul
```
-----------------------------
#### 双链表的定位操作
在dL所指的双链表中查找第一个值和e相等的结点，若存在，则返回其指针；
```C++
[in DuLinkList.h]
int LocateElem_DL(DuLinkList L,ElemType e) 
{
    DuLinkList  p;
    int k=0;
    p=L->next;
    while(p&&e!=p->data)
    {k++;p=p->next;}
    return p;
}
```
-----------------------------
#### 双链表插入元素操作
在带头结点的双向链表DL中第i个结点之前插入元素e
 ```C++
[in DuLinkList.h]
bool ListInsert_DuL(DuLinkList &L,int i,ElemType e) 
{
   DuLinkList p,s,q;
   int j;
   q=L;  j=0;
   while(q->next&&j<i-1)  { q=q->next; j++; }   // 寻找第i-1个结点,并让q指向此结点
   if(j!=i-1)   return false;                   // i的位置不合理
   s=(DuLNode *)malloc(sizeof(DuLNode));
   if(!s)   exit(1);                            // 存储分配失败
   s->data=e;  
   if(q->next)                                   // 插入的位置不是表尾
   { 
	    p=q->next;                          // p指向待插入的位置
	    s->prior=p->prior;                  // 修改指针
        p->prior->next=s;      
        s->next=p;
        p->prior=s; 
   }
   else                                     // 插入的位置是表尾
   { 
        q->next=s;
	    s->prior=q;
	    s->next=NULL;
   }
   return true;
}
```
-----------------------------
#### 双链表删除元素操作
删除带有头结点的双向链表DL中的第i个结点，并让e返回其值
 ```C++
[in DuLinkList.h]
bool ListDelete_Du(DuLinkList &L, int i, ElemType &e)
{  int j = 0;
   DuLinkList p=L;
   while(p->next&&j<i){ p=p->next; j++; }   // 寻找第i个结点,并让p指向此结点
   if(j!=i)   return false;                 // i的位置不合理
   if(p->next)                              // 待删除的不是表尾结点
     p->next->prior=p->prior;               // 结点p的前驱作为结点p的后继的前驱
     p->prior->next=p->next;                // 结点p的后继作为结点p的前驱的后继                              
     e=p->data;                     
     free(p);                                    
     return true;
 }
```
-----------------------------
#### 双链表取元素操作
取出双链表DL中第i个元素，并用e返回其值
 ```C++
[in DuLinkList.h]
bool GetElem_DL(DuLinkList L,int i, ElemType &e)
{
        DuLinkList p;
  		int j;
  		p=L;  j=0;
		while(p->next&&j<i){ p=p->next; j++; }  
// 寻找第i个结点,并让p指向此结点
 		 if(j!=i)   return false;                      // i的位置不合理
  		 e=p->data;                                     // 被取元素的值赋给e
 		 return true;
}
```
-----------------------------
#### 创建双链表（尾插）
已知一维数组A[n]中存有线性表的数据元素，利用尾插法创建双链表DL
 ```C++
[in DuLinkList.h]
void CreateList_Du_Rear(LinkList &L,ElemType a[],int n ) 
{

    
}
```
-----------------------------
#### 创建双链表（尾插）
已知一维数组A[n]中存有线性表的数据元素，利用尾插法创建双链表DL
 ```C++
[in DuLinkList.h]
void CreateList_Du_Front(LinkList &L,ElemType a[],int n ) 
{

    
}
```
-----------------------------
#### 遍历输出双向链表各元素数据
 ```C++
[in DuLinkList.h]
void ListTraverse_Du(LinkList DL)
{

    
}
```
-----------------------------
#### 销毁双向链表
 ```C++
[in DuLinkList.h]
void DestroyList_Du(LinkList &DL )
{


}
```
-----------------------------
#### 双向链表的可视化
将这个单链表(用一个png文件)可视化展示出来,前提是需要安装graphviz并配置系统环境变
 ```C++
[in DuLinkList.h]
void ListVisualization_Du(LinkList L, char* filename)
{   

}
```
-----------------------------
#### 双向链表测试
[in DuLinkListTest.cpp]
```C++   
int main()
{


} 
```
-----------------------------
### 循环链表
#### 定义
- 即约瑟夫环，是另一种形式的链式存储结构，它的基本思想是利用结点的空指针域，在链尾和链头之间增加链接，形成环状数据结构。
- 一般有两种形式的循环链表，即单向循环链表和双向循环链表。
- 单向循环链表中，表尾结点的指针域不为空，回指第一个结点，整个链表形成一个环。
- 在双向循环链表中，除了表尾结点的后继指针域回指第一个结点外，同时表头结点的前驱指针域回指表尾结点，这样在链表中构成了两个环。

#### 逻辑图示
<img width="600" src="/Chapter_02_List/img/5.jpg"/>
<img width="600" src="/Chapter_02_List/img/6.jpg"/>

-----------------------------
#### 注意事项
- 在初始化操作中，分别将语句L->next = NULL;L- prior = NULL 改成: L->next = L; L->prior = L;
- 在其他操作中，循环控制条件不是判断p,p->next或p->next->next是否为空，二十判断他们是否等于头指针
- 工作指针p的初值应该与循环控制条件相对应，也就是说，若赋值语句为"p=L"，则循环控制表达式为"p->next!=L"，若赋值语句为"p=L->next;";则循环控制表达式为"p!=L"。

#### 双向循环链表的数据结构
同双向链表
-----------------------------
#### 双向循环链表初始化
初始化双向循环链表DL
[in DuLinkList_C.h]
void InitList_DuL_C(DuLinkList &L)
{  
  L=(DuLNode *)malloc(sizeof(DuLNode));    // 申请存放一个结点数据所需要的内在空间
  if(!L)   exit(1);                        // 存储分配失败
  L->next=L;                               // 表头结点作为表头结点的后继
  L->prior=L;                              // 表头结点作为表头结点的前驱
}
-----------------------------
#### 双向循环链表的长度
// DL为带头结点的链表的头指针，函数返回L所指链表的长度
```C++
[in DuLinkList_C.h]
int ListLength_DuL_C(DuLinkList L ) 
{     

}
```
-----------------------------
#### 双向循环链表的定位操作
在dL所指的双链表中查找第一个值和e相等的结点，若存在，则返回其指针；
```C++
[in DuLinkList_C.h]
int LocateElem_DuL_C( LinkList DL,ElemType e) 
{

    
}
```
-----------------------------
#### 双向循环链表的插入元素操作
在带头结点的双向链表DL中第i个结点之前插入元素e
 ```C++
[in DuLinkList_C.h]
bool ListInsert_DuL_C(DuLinkList &L,int i,ElemType e) 
{
   DuLinkList p,s;
   int j;
   p = L->next;j=1
   while(p!= L&&j<1)  { p = p->next; j++; }   // 寻找第i-1个结点,并让p指向此结点
   if(j!=i)   return false;                   // i的位置不合理
   s=(DuLNode *)malloc(sizeof(DuLNode));
   if(!s)   exit(1);                            // 存储分配失败
   s->data=e;  
   s->prior=p->prior;                  // 修改指针
   p->prior->next=s;      
   s->next=p; 
   p->prior=s; 
   return true;
}
```
-----------------------------
#### 双向循环链表的删除元素操作
删除带有头结点的双向链表DL中的第i个结点，并让e返回其值
 ```C++
[in DuLinkList_C.h]
bool ListDelete_DuL_C(DuLinkList &L, int i, ElemType &e)
{   DuLinkList p;
    int j;
    p->L->next;j=1
    while(p->next!=L&&j<i){ p=p->next; j++; }   // 寻找第i个结点,并让p指向此结点
    if(j!=i)   return false;                 // i的位置不合理
    e = p->data;
    p->prior->next = p->next;
    p->next->prior = p->prior;
    free(p);                     
    return true;
 }
```
-----------------------------
#### 双链表取元素操作
取出双链表DL中第i个元素，并用e返回其值
 ```C++
[in DuLinkList_C.h]
bool GetElem_DL_C(LinkList L,int i, ElemType &e)
{

    
}
```
-----------------------------
#### 创建双链表（尾插）
已知一维数组A[n]中存有线性表的数据元素，利用尾插法创建双链表DL
 ```C++
[in DuLinkList_C.h]
void CreateList_Du_C_Rear(LinkList &L,ElemType a[],int n ) 
{

    
}
```
-----------------------------
#### 创建双链表（尾插）
已知一维数组A[n]中存有线性表的数据元素，利用尾插法创建双链表DL
 ```C++
[in DuLinkList_C.h]
void CreateList_Du_C_Front(LinkList &L,ElemType a[],int n ) 
{

    
}
```
-----------------------------
#### 遍历输出双向循环链表各元素数据
 ```C++
[in DuLinkList_C.h]
void ListTraverse_Du_C(LinkList DL)
{

    
}
```
-----------------------------
#### 销毁双向循环链表
 ```C++
[in DuLinkList_C.h]
void DestroyList_Du_C(LinkList &DL )
{


}
```
-----------------------------
#### 双向循环链表的可视化
将这个双向循环链表(用一个png文件)可视化展示出来,前提是需要安装graphviz并配置系统环境变
 ```C++
[in DuLinkList_C.h]
void ListVisualization_Du(LinkList L, char* filename)
{   

}
```
-----------------------------
#### 双向循环链表测试
[in DuLinkList_CTest.cpp]
```C++   
int main()
{


} 
```
-----------------------------


## 书中例子
### 【例2.1】 假设利用两个线性表La和Lb分别表示两个集合A和B（线性表中的数据元素即为集合中的成员），求一个新的集合A=A∪B。<br>
**分析**：这个问题相当于对线性表La做如下操作：把线性表Lb的元素依次插入到La表中。并且需要有一个判断La中是否已经存在Lb中某数据的操作<br>
**算法思想**：
1. 从线性表Lb中取得一个数据元素；
2. 依该数据元素的值在线性表La中进行查查访；
3. 若线性表La中不存在和其值相同的元素，则将从Lb中删除的数据元素插入La中；重复以上操作直到遍历完Lb表为止。
```C
void union(List &La,List Lb)
{
    La_len = ListLength(La)   //求线性表La的长度
    while(!ListEmpty(Lb))     //Lb表的元素尚未处未处理完
    {
        ListDelete(Lb,1,e);    //从Lb中删除第一个数据元素赋值给e
        if(!LocateElem(La,e))     //若La中不存在值和e相等的数据元素
           ListInsert(La,++La_len,e);   //则将它插入在La中最后一个数据元素之后
    }
    DestroyList(Lb); //撤销线性表
}
```

### 【例2.2】已知一个非纯集合B（即集合B中可能有相同元素），试构造一个纯集合A，使A中只包含B中所有值各不相同的成员。<br>
**分析**：与上例不同的是，此例中A先不存在，则第一步需要构造一个集合A<br>
**算法思想**:
1. 构造一个空的线性表La，代表集合A;
2. 从线性表Lb中取得一个数据元素;
3. 依该数据元素的值在La中进行遍历；
4. 如果La中不存在和其值相同的元素，则将从Lb中删除该元素并插入到线性表La中。
5. 重复2至4步直至Lb空为止。
```C
void purge(List &La,List Lb)
{
    InitList(La);          //初始化La,即创建一个新的，空的线性表La
    while(!ListEmpty(Lb))     //Lb表的元素尚未处未处理完
    {
        ListDelete(Lb,1,e);    //从Lb中删除第一个数据元素赋值给e
        if(!LocateElem(La,e))     //若La中不存在值和e相等的数据元素
           ListInsert(La,++La_len,e);   //则将它插入在La中最后一个数据元素之后
    }
    DestroyList(Lb); //撤销线性表
}
```
**仔细比对例2.1和例2.2看区别语句在哪**

### 【例2.3】判别两个**纯**集合A和B是否相等。
**分析**两个集合相等，指的是这两个集合的元素在**值**上具有**仅仅包含**的关系。顺序上不一定具有一一对应的关系。因此，一旦找到一个元素，它存在于A,但不存在于B，或者相反，那么则可以立即判定AB不相等<br>
**算法思想**
1. 构造一个和La相同的线性表Lc;
2. 对Lb中的每一个数据元素，在Lc中进行查询；
3. 如果找到了该元素，则从Lc中删除该元素;
4. 遍历完Lb时检查Lc,若Lc为空，则两个集合相等，否则不等。
```C
bool isequal(List La, List Lb)
{
   La_len = ListLength(La);    //求表长 
   Lb_len - ListLength(Lb);    //求表长
   if(La_len! = Lb_len) return false;
   else
   {
        InitList(Lc);           //初始化Lc
        for(k=1;k<=La_len;k++)  //生成La的复制品Lc
        {
            GetElem(Lb,k,e);
            ListInsert(Lc,k,e);
        }
    found = true;
    for(k=1;k<=Lb_len,found;k++)
    {
        GetElem(Lb,k,e);        //取Lb中第k个数据元素
        i = LocateElem(Lc,e);   //在Lc中进行查询
        if(i==0) found = false; //La中不存在和该数据元素相同的元素
        else ListDelete(Lc,i,e); //从Lc中删除该数据元素
    }
    if(found&&ListEmpty(Lc))   return true;
    else return false;
    DestroyList(Lc);
   }
}//isequal
```

### 【例2.4】顺序表的归并。已知顺序表La和Lb中的数据元素按值非递减有序排列，现要求将La和Lb归并为一个新的顺序表Lc，且Lc中的数据元素仍按值非递减有序排列。
**分析** 将La和Lb中的元素进行比较后插入Lc
**算法思想** 
1. 设三个工作指针i,j,k分别指向La,Lb和Lc中的表头位置。
2. 比较i,j当前所指元素，若i当前所指的元素小于等于j当前所指的元素，则将i当前所指的元素插入到Lc中区，i和k后移一个元素的位置；否则，将j当前所指元素插入到Lc中，j和k后移一个元素的位置。若i和j都未到达各自的表尾，重复此操作。
3. 若i未到达La的表尾，则将La中剩余的元素依次插入到Lc中。
4. 若j未到达Lb的表尾，则将Lb中剩余的元素依次插入到Lc中。
```C
typedef int ElemType;      // 顺序表中元素类型为int
# include "stdlib.h"       // 该文件包含malloc()、realloc()和free()等函数
# include "iomanip.h"      // 该文件包含标准输入输出流cout和cin及控制符setw()
# include "SqList.h"       // 该文件中包含顺序表数据对象的描述及相关操作

void MergeList_Sq(SqList La, SqList Lb,SqList &Lc) 
{    
    int i=0,j=0,k=0;                               // i,j,k分别指向各自的表头
    InitList_Sq(Lc, La.length+Lb.length,10);       // 创建一个空的顺序表Lc
    while(i<La.length&&j<Lb.length)                // 归并
	    if(La.elem[i]<=Lb.elem[j]) 
    Lc.elem[k++]=La.elem[i++]; 
    else Lc.elem[k++]=Lb.elem[j++]; 
    while(i<La.length)  Lc.elem[k++]=La.elem[i++];
    while(j<Lb.length)  Lc.elem[k++]=Lb.elem[j++];
    Lc.length=La.length+Lb.length;
}// MergeList_Sq

void main()
{ 
    SqList La,Lb,Lc;
    ElemType a[]={2,6,9,13,45};
    ElemType b[]={1,6,19,25,45,60};
    InitList_Sq(La, 50,10);                  // 初始化顺序表La
    for(int i=0;i<5;i++)                     // 创建顺序表La
    if(!ListInsert_Sq(La,i,a[i]))           
    { 
        cout<<"插入失败！"<<endl;
        return;
    }
    cout<<"顺序表La：";
    ListTraverse_Sq(La);                  // 显示顺序表La
    InitList_Sq(Lb, 50,10);                // 初始化顺序表Lb
    for(i=0;i<6;i++)                       // 创建顺序表Lb
    if(!ListInsert_Sq(Lb,i,b[i]))
    {
        cout<<"插入失败！"<<endl;
        return;
    }
   cout<<"顺序表Lb：";
   ListTraverse_Sq(Lb);                   // 显示顺序表Lb
   MergeList_Sq(La,Lb,Lc) ;               // 归并La和Lb为Lc
   cout<<"归并后的顺序表Lc：";
   ListTraverse_Sq(Lc);                   // 显示顺序表Lc
 }	
```
### 【例2.5】用顺序表完成例2.2的操作
```
void purge_Sq(SqList &A, SqList &B)
{
   ElemType e;
   A.elem[0] = B.elem[0];
   A.length = 1;
   for(i=1;i<B.length;i++)
   {
       e=B.elem[i];
       j=0;
       while(j<A.length&&A.elem[j]!=e) ++j;
       if(j==A.length)
       {
           A.elem[A.length]=e;
           A.length++;
       }
   }
   delete[B.elem]; B.listsize = 0;
}//purge_Sq
```




## [源代码下载](https://github.com/xiufengcheng/DATASTRUCTURE/tree/master/Chapter_02_List/sourcecode)

