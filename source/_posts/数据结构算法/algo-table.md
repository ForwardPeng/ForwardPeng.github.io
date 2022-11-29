---
title: algo-table
date: 2020-04-22 12:21:14
categories: 
- 计算机基础
- 数据结构
tags:
- 数据结构
- 算法导论
- 线性表
toc: true
---
# 线性表
将具有“一对一”关系的数据“线性”地存储到物理空间中，这种存储结构就称为线性存储结构（简称线性表）。使用线性表存储的数据，如同向数组中存储数据那样，要求数据类型必须一致，也就是说，线性表存储的数据，要么全不都是整形，要么全部都是字符串。一半是整形，另一半是字符串的一组数据无法使用线性表存储。
## 顺序存储结构和链式存储结构
* 将数据依次存储在连续的整块物理空间中，这种存储结构称为顺序存储结构(顺序表)
* 数据分散的存储在物理空间中，通过一根线保存着它们之间的逻辑关系，这种存储结构称为链式存储结构(链表)

### 前驱和后继
数据结构中，一组数据中的每个个体被称为“数据元素”。
* 某一元素的左侧相邻元素称为“直接前驱”，位于此元素左侧的所有元素都统称为“前驱元素”；
* 某一元素的右侧相邻元素称为“直接后继”，位于此元素右侧的所有元素都统称为“后继元素”；
 <div align=center><img src="/image/prev.png" width="400"/></div>
 <center>图1 前驱和后继</center>

 # 顺序表(顺序存储结构)及初始化
 顺序表存储数据时，会提前申请一整块足够大小的物理空间，然后将数据依次存储起来，存储时做到数据元素之间不留一丝缝隙。顺序表存储数据使用的是数组。

## 顺序表的初始化
使用顺序表存储数据之前，除了要申请足够大小的物理空间之外，为了方便后期使用表中的数据，顺序表还需要实时记录以下 2 项数据：
* 顺序表申请的存储容量
* 顺序表的长度，元素的个数

<font color=red>正常状态下，顺序表申请的存储容量要大于顺序表的长度。</font>

自定义顺序表的结构体：C语言实现：
```c
typedef struct Table
{
    int* head;  //动态数组
    int length; //顺序表的长度
    int size; //顺序表分配的存储容量
}table;
```
建立顺序表需要做如下工作：
* 给head动态数据申请足够大小的物理空间；
* 给size和length赋初值；
```c
#include <stdio.h>
#include <stdlib.h>
#define Size 5
typedef struct Table{
    int * head;
    int length;
    int size;
}table;
table initTable(){
    table t;
    t.head=(int*)malloc(Size*sizeof(int));  //构造一个空的顺序表，动态申请存储空间
    if (!t.head) //如果申请失败，作出提示并直接退出程序
    {
        printf("初始化失败");
        exit(0);
    }
    t.length=0;  //长度为0
    t.size=Size;  //存储空间为Size
    return t;
}
//输出顺序表中元素的函数
void displayTable(table t){
    for (int i=0;i<t.length;i++) {
        printf("%d ",t.head[i]);
    }
    printf("\n");
}
int main(){
    table t=initTable();
    //向顺序表中添加元素
    for (int i=1; i<=Size; i++) {
        t.head[i-1]=i;
        t.length++;
    }
    printf("顺序表中存储的元素分别是：\n");
    displayTable(t);
    return 0;
}
```
## 顺序表插入元素
虽然数据元素插入顺序表中的位置有所不同，但是都使用的是同一种方式去解决，即：通过遍历，找到数据元素要插入的位置，然后做如下两步工作：
* 将要插入位置元素以及后续的元素整体向后移动一个位置；
* 将元素放到腾出来的位置上；
```c
//插入函数，其中，elem为插入的元素，add为插入到顺序表的位置
table addTable(table t, int elem, int add)
{
    //判断插入本身是否存在问题（如果插入元素位置比整张表的长度+1还大（如果相等，是尾随的情况），或者插入的位置本身不存在，程序作为提示并自动退出）
    if (add > t.length + 1 || add < 1)
    {
        printf("插入位置有问题\n");
        return t;
    }
    //做插入操作时，首先需要看顺序表是否有多余的存储空间提供给插入的元素，如果没有，需要申请
    if (t.length == t.size)
    {
        t.head = (int *)realloc(t.head, (t.size + 1) * sizeof(int));
        if (!t.head)
        {
            printf("存储分配失败\n");
            return t;
        }
        t.size += 1;
    }
    //插入操作，需要将从插入位置开始的后续元素，逐个后移
    for (int i = t.length - 1; i >= add - 1; i--)
    {
        t.head[i + 1] = t.head[i];
    }
    //后移完成后，直接将所需插入元素，添加到顺序表的相应位置
    t.head[add - 1] = elem;
    //由于添加了元素，所以长度+1
    t.length++;
    return t;
}
```
<font color=red>注意，动态数组额外申请更多物理空间使用的是 realloc 函数。并且，在实现后续元素整体后移的过程，目标位置其实是有数据的，还是 3，只是下一步新插入元素时会把旧元素直接覆盖。</font>

## 顺序表删除元素
从顺序表中删除指定元素，实现起来非常简单，只需找到目标元素，并将其后续所有元素整体前移 1 个位置即可。
```c
table delTable(table t, int add)
{
    printf("被删除元素的位置有误\n");
        return t;
    }
    //删除操作
    for (int i=add; i<t.length; i++) {
        t.head[i-1]=t.head[i];
    }
    t.length--;
    return t;
}
```
## 顺序表查找元素
顺序表中查找目标元素，可以使用多种查找算法实现，比如说二分查找算法、顺序查找等
```c
//顺序查找
int selectTable(table t,int elem){
    for (int i=0; i<t.length; i++) {
        if (t.head[i]==elem) {
            return i+1;
        }
    }
    return -1;//如果查找失败，返回-1
}
```
## 更改元素
顺序表更改元素的实现过程是：
* 找到目标元素；
* 直接修改该元素的值；
```c
table amendTable(table t,int elem,int newElem){
    int add=selectTable(t, elem);
    t.head[add-1]=newElem;//由于返回的是元素在顺序表中的位置，所以-1就是该元素在数组中的下标
    return t;
}
```
# 单链表
与顺序表不同，链表不限制数据的物理存储状态，换句话说，使用链表存储的数据元素，其物理存储位置是随机的。<font color=red>通过指针表示数据之间逻辑关系的存储结构就是链式存储结构。</font>
## 链表的节点
链表中每个数据的存储都由以下两部分组成：
* 数据元素本身，其所在的区域称为数据域；
* 指向直接后继元素的指针，所在的区域称为指针域；
<div align=center><img src="/image/节点结构.png" width="400"/></div>
 <center>图2 节点结构</center>

 链表中每个节点的结构体实现c代码：
 ```c
 typedef struct Link{
    char elem; //代表数据域
    struct Link * next; //代表指针域，指向直接后继元素
}link; //link为节点名，每个节点都是一个 link 结构体
 ```
 ## 头节点、头指针和首元节点
 一个完整的链表需要由以下几部分构成：
 * 头指针：一个普通的指针，它的特点是永远指向链表第一个节点的位置。很明显，头指针用于指明链表的位置，便于后期找到链表并使用表中的数据；
 * 头节点：其实就是一个不存任何数据的空节点，通常作为链表的第一个节点。对于链表来说，头节点不是必须的，它的作用只是为了方便解决某些实际问题；
* 首元节点：由于头节点（也就是空节点）的缘故，链表中称第一个存有数据的节点为首元节点。首元节点只是对链表中第一个存有数据节点的一个称谓，没有实际意义；
* 其他节点：链表中其他的节点；

<font color=red>注意：链表中有头节点时，头指针指向头节点；反之，若链表中没有头节点，则头指针指向首元节点。</font>

## 链表的创建(初始化)
创建一个链表步骤如下：
* 声明一个头指针（如果有必要，可以声明一个头节点）；
* 创建多个存储数据的节点，在创建的过程中，要随时与其前驱节点建立逻辑关系；
```c
//创建一个存储 {1,2,3,4} 且含头节点的链表
link * initLink(){
    link * p=(link*)malloc(sizeof(link));//创建一个头结点
    link * temp=p;//声明一个指针指向头结点，
    //生成链表
    for (int i=1; i<5; i++) {
        link *a=(link*)malloc(sizeof(link));
        a->elem=i;
        a->next=NULL;
        temp->next=a;
        temp=temp->next;
    }
    return p;
}
```
## 链表插入元素
虽然新元素的插入位置不固定，但是链表插入元素的思想是固定的，只需做以下两步操作，即可将新元素插入到指定的位置：
* 将新结点的 next 指针指向插入位置后的结点；
* 将插入位置前结点的 next 指针指向插入结点；

<font color=red>注意：链表插入元素的操作必须是先步骤 1，再步骤 2；反之，若先执行步骤 2，除非再添加一个指针，作为插入位置后续链表的头指针，否则会导致插入位置后的这部分链表丢失，无法再实现步骤 1。</font>

```c
//p为原链表，elem表示新数据元素，add表示新元素要插入的位置
link * insertElem(link * p, int elem, int add) {
    link * temp = p;//创建临时结点temp
    //首先找到要插入位置的上一个结点
    for (int i = 1; i < add; i++) {
        temp = temp->next;
        if (temp == NULL) {
            printf("插入位置无效\n");
            return p;
        }
    }
    //创建插入结点c
    link * c = (link*)malloc(sizeof(link));
    c->elem = elem;
    //向链表中插入结点
    c->next = temp->next;
    temp->next = c;
    return p;
}
```
## 链表删除元素
从链表中删除数据元素需要进行以下 2步操作：
* 将结点从链表中摘下来;
* 手动释放掉结点，回收被结点占用的存储空间;
>temp->next=temp->next->next;

执行效果如图3所示：
 <div align=center><img src="/image/链表删除元素.png" width="400"/></div>
 <center>图3 链表删除元素</center>
 
 ```c
//p为原链表，add为要删除元素的值
link * delElem(link * p, int add) {
    link * temp = p;
    //遍历到被删除结点的上一个结点
    for (int i = 1; i < add; i++) {
        temp = temp->next;
        if (temp->next == NULL) {
            printf("没有该结点\n");
            return p;
        }
    }
    link * del = temp->next;//单独设置一个指针指向被删除结点，以防丢失
    temp->next = temp->next->next;//删除某个结点的方法就是更改前一个结点的指针域
    free(del);//手动释放该结点，防止内存泄漏
    return p;
}
 ```
 ## 链表查找元素
从表头依次遍历表中节点，用被查找元素与各节点数据域中存储的数据元素进行比对，直至比对成功或遍历至链表最末端的 NULL（比对失败的标志）。
```c
//p为原链表，elem表示被查找元素、
int selectElem(link * p,int elem){
//新建一个指针t，初始化为头指针 p
    link * t=p;
    int i=1;
    //由于头节点的存在，因此while中的判断为t->next
    while (t->next) {
        t=t->next;
        if (t->elem==elem) {
            return i;
        }
        i++;
    }
    //程序执行至此处，表示查找失败
    return -1;
}
```
## 链表更新元素
更新链表中的元素，只需通过遍历找到存储此元素的节点，对节点中的数据域做更改操作即可。
```c
//更新函数，其中，add 表示更改结点在链表中的位置，newElem 为新的数据域的值
link *amendElem(link * p,int add,int newElem){
    link * temp=p;
    temp=temp->next;//在遍历之前，temp指向首元结点
    //遍历到待更新结点
    for (int i=1; i<add; i++) {
        temp=temp->next;
    }
    temp->elem=newElem;
    return p;
}
```
# 顺序表和链表的优缺点(区别、特点)
* 顺序表存储数据，需预先申请一整块足够大的存储空间，然后将数据按照次序逐一存储，数据之间紧密贴合，不留一丝空隙，如图4a)所示；
* 链表的存储方式与顺序表截然相反，什么时候存储数据，什么时候才申请存储空间，数据之间的逻辑关系依靠每个数据元素携带的指针维持，如图4b)所示；

<div align=center><img src="/image/区别.png" width="400"/></div>

## 开辟空间的方式
顺序表存储数据实行的是 "一次开辟，永久使用"，即存储数据之前先开辟好足够的存储空间，空间一旦开辟后期无法改变大小链表则不同，链表存储数据时一次只开辟存储一个节点的物理空间，如果后期需要还可以再申请。

因此，若只从开辟空间方式的角度去考虑，当存储数据的个数无法提前确定，又或是物理空间使用紧张以致无法一次性申请到足够大小的空间时，使用链表更有助于问题的解决。

## 空间利用率
空间利用率的角度上看，顺序表的空间利用率显然要比链表高。链表在存储数据时，每次只申请一个节点的空间，且空间的位置是随机的

这种申请存储空间的方式会产生很多空间碎片，一定程序上造成了空间浪费。不仅如此，由于链表中每个数据元素都必须携带至少一个指针，因此，链表对所申请空间的利用率也没有顺序表高。空间碎片，指的是某些容量很小（1KB 甚至更小）以致无法得到有效利用的物理空间。

## 时间复杂度
根据顺序表和链表在存储结构上的差异，问题类型主要分为以下 2 类：
* 问题中主要涉及访问元素的操作，元素的插入、删除和移动操作极少；
* 问题中主要涉及元素的插入、删除和移动，访问元素的需求很少；

第 1 类问题适合使用顺序表。这是因为，顺序表中存储的元素可以使用数组下标直接访问，无需遍历整个表，因此使用顺序表访问元素的时间复杂度为 O(1)；而在链表中访问数据元素，需要从表头依次遍历，直到找到指定节点，花费的时间复杂度为 O(n);

第 2 类问题则适合使用链表。链表中数据元素之间的逻辑关系靠的是节点之间的指针，当需要在链表中某处插入或删除节点时，只需改变相应节点的指针指向即可，无需大量移动元素，因此链表中插入、删除或移动数据所耗费的时间复杂度为 O(1)；而顺序表中，插入、删除和移动数据可能会牵涉到大量元素的整体移动，因此时间复杂度至少为 O(n);

# 静态链表及其创建
使用静态链表存储数据，数据全部存储在数组中（和顺序表一样），但存储位置是随机的，数据之间"一对一"的逻辑关系通过一个整形变量（称为"游标"，和指针功能类似）维持（和链表类似）。
 <div align=center><img src="/image/静态链表.png" width="400"/></div>
 <center>图4 静态链表存储数据</center>

 静态链表会将第一个数据元素放到数组下标为 1 的位置（a[1]）中。从 a[1] 存储的数据元素 1 开始，通过存储的游标变量 3，就可以在 a[3] 中找到元素 1 的直接后继元素 2；同样，通过元素 a[3] 存储的游标变量 5，可以在 a[5] 中找到元素 2 的直接后继元素 3，这样的循环过程直到某元素的游标变量为 0 截止（因为 a[0] 默认不存储数据元素）。

## 静态链表中的节点
静态链表存储数据元素也需要自定义数据类型，至少需要包含以下 2 部分信息：
* 数据域：用于存储数据元素的值；
* 游标：其实就是数组下标，表示直接后继元素所在数组中的位置

静态链表中节点构成的C语言实现：
```c
typedef struct {
    int data;//数据域
    int cur;//游标
}component;
```
## 备用链表
备用链表的作用是回收数组中未使用或之前使用过（目前未使用）的存储空间，留待后期使用。也就是说，静态链表使用数组申请的物理空间中，存有两个链表，一条连接数据，另一条连接数组中未使用的空间
>通常，备用链表的表头位于数组下标为 0（a[0]） 的位置，而数据链表的表头位于数组下标为 1（a[1]）的位置。

## 静态链表的创建
* 在数据链表未初始化之前，数组中所有位置都处于空闲状态，都被链接到备用链表上。
* 向静态链表添加数据时，需提前从备用链表中摘除节点，供新数据使用。

<font color=red>备用链表摘除节点最简单的方法是摘除 a[0] 的直接后继节点；同样，向备用链表中添加空闲节点也是添加作为 a[0] 新的直接后继节点。因为 a[0] 是备用链表的第一个节点，我们知道它的位置，操作它的直接后继节点相对容易，无需遍历备用链表，耗费的时间复杂度为 O(1)。</font>

```c
#include <stdio.h>
#define maxSize 6
typedef struct
{
    /* data */
    int data;
    int cur;
} component;
//将结构体数组中所有分量链接到备用链表
void reserveArr(component *array);
//初始化静态链表
int initArr(component *array);
//输出函数
void displayArr(component *array, int body);

//从备用链表上摘下空闲节点
int mallocArr(component *array);
int main()
{
    component array[maxSize];
    int body = initArr(array);
    printf("静态链表：\n");
    displayArr(array, body);
    return 0;
}
//创建备用链表
void reserveArr(component *array)
{
    for (int i = 0; i < maxSize; i++)
    {
        array[i].cur = i + 1; //将每个数组分量链接到一起
        array[i].data = -1;
    }
    array[maxSize - 1].cur = 0; //链表最后一个节点的游标为0
}
//提前分配空间
int mallocArr(component *array)
{
    int i = array[0].cur;
    if (array[0].cur)
    {
        array[0].cur = array[i].cur;
    }
    return i;
}
//初始化静态链表
int initArr(component *array)
{
    reserveArr(array);
    int body = mallocArr(array); //声明一个变量，把它当指针使，指向链表的最后的一个结点，因为链表为空，所以和头结点重合
    int tempBody = body;
    for (int i = 1; i < 4; i++)
    {
        int j = mallocArr(array); //从备用链表中拿出空闲的分量
        array[tempBody].cur = j;  //将申请的空闲分量链接在链表的最后一个结点后面
        array[j].data = i;        //给新申请的分量的数据域初始化
        tempBody = j;             //将指向链表最后一个结点的指针后移
    }
    array[tempBody].cur = 0; //新的链表最后一个结点的指针设置为0
    return body;
}
void displayArr(component *array, int body)
{
    int tempBody = body; //tempBody准备做遍历使用
    while (array[tempBody].cur)
    {
        printf("%d,%d ", array[tempBody].data, array[tempBody].cur);
        tempBody = array[tempBody].cur;
    }
    printf("%d,%d\n", array[tempBody].data, array[tempBody].cur);
}
```
## 静态链表的基本操作

### 添加元素
将元素4添加到静态链表中的第3个位置上，实现过程如下：
* 从备用链表中摘除一个节点，用于存储元素 4；
* 找到表中第 2 个节点（添加位置的前一个节点，这里是数据元素 2），将元素 2 的游标赋值给新元素 4；
* 将元素 4 所在数组中的下标赋值给元素 2 的游标；
```c
void insertArr(component * array,int body,int add,char a){
    int tempBody=body;//tempBody做遍历结构体数组使用
    //找到要插入位置的上一个结点在数组中的位置
    for (int i=1; i<add; i++) {
        tempBody=array[tempBody].cur;
    }
    int insert=mallocArr(array);//申请空间，准备插入
    array[insert].data=a;
    array[insert].cur=array[tempBody].cur;//新插入结点的游标等于其直接前驱结点的游标
    array[tempBody].cur=insert;//直接前驱结点的游标等于新插入结点所在数组中的下标
}
```
### 删除元素
静态链表中删除指定元素，只需实现以下 2 步操作：
* 将存有目标元素的节点从数据链表中摘除；
* 将摘除节点添加到备用链表，以便下次再用；
```c
//备用链表回收空间的函数，其中array为存储数据的数组，k表示未使用节点所在数组的下标
void freeArr(component * array,int k){
    array[k].cur=array[0].cur;
    array[0].cur=k;
}
//删除结点函数，a 表示被删除结点中数据域存放的数据
void deletArr(component * array,int body,char a){
    int tempBody=body;
    //找到被删除结点的位置
    while (array[tempBody].data!=a) {
        tempBody=array[tempBody].cur;
        //当tempBody为0时，表示链表遍历结束，说明链表中没有存储该数据的结点
        if (tempBody==0) {
            printf("链表中没有此数据");
            return;
        }
    }
    //运行到此，证明有该结点
    int del=tempBody;
    tempBody=body;
    //找到该结点的上一个结点，做删除操作
    while (array[tempBody].cur!=del) {
        tempBody=array[tempBody].cur;
    }
    //将被删除结点的游标直接给被删除结点的上一个结点
    array[tempBody].cur=array[del].cur;
    //回收被摘除节点的空间
    freeArr(array, del);
}
```
### 查找元素
静态链表查找指定元素，由于我们只知道静态链表第一个元素所在数组中的位置，因此只能通过逐个遍历静态链表的方式，查找存有指定数据元素的节点。
```c
int selectElem(component * array,int body,char elem){
    int tempBody=body;
    //当游标值为0时，表示链表结束
    while (array[tempBody].cur!=0) {
        if (array[tempBody].data==elem) {
            return tempBody;
        }
        tempBody=array[tempBody].cur;
    }
    return -1;//返回-1，表示在链表中没有找到该元素
}
```
### 更改数据
找到目标元素所在的节点，直接更改节点中的数据域即可。
```c
void amendElem(component * array,int body,char oldElem,char newElem){
    int add=selectElem(array, body, oldElem);
    if (add==-1) {
        printf("无更改元素");
        return;
    }
    array[add].data=newElem;
}
```
# 静态链表和动态链表区别
## 静态链表
使用静态链表存储数据，需要预先申请足够大的一整块内存空间，也就是说，静态链表存储数据元素的个数从其创建的那一刻就已经确定，后期无法更改。

比如，如果创建静态链表时只申请存储10个数据元素的空间，那么在使用静态链表时，数据的存储个数就不能超过10个，否则程序就会发生错误。

不仅如此，静态链表是在固定大小的存储空间内随机存储各个数据元素，这就造成了静态链表中需要使用另一条链表（通常称为"备用链表"）来记录空间存储空间的位置，以便后期分配给新添加元素使用，如图 2 所示。

这意味着，如果你选择使用静态链表存储数据，你需要通过操控两条链表，一条是存储数据，另一条是记录空闲空间的位置。
## 动态链表
使用动态链表存储数据，不需要预先申请内存空间，而是在需要的时候才向内存申请。也就是说，动态链表存储数据元素的个数是不限的，想存多少就存多少。

同时，使用动态链表的整个过程，你也只需操控一条存储数据的链表。当表中添加或删除数据元素时，你只需要通过 malloc 或 free 函数来申请或释放空间即可，实现起来比较简单。
# 双向链表
<font color=red>双向，指的是各节点之间的逻辑关系是双向的，但通常头指针只设置一个，除非实际情况需要</font>

双向链表中各节点包含以下3部分信息：
* 指针域：用于指向当前节点的直接前驱节点；
* 数据域：用于存储数据元素。
* 指针域：用于指向当前节点的直接后继节点；

双链表的节点结构的Ｃ语言实现：
```c
typedef struct line{
    struct line * prior; //指向直接前趋
    int data;
    struct line * next; //指向直接后继
}line;
```
## 双向链表的创建
与单链表不同，双链表创建过程中，每创建一个新节点，都要与其前驱节点建立两次联系，分别是：
* 将新节点的 prior 指针指向直接前驱节点；
* 将直接前驱节点的 next 指针指向新节点；
```c
line* initLine(line * head){
    head=(line*)malloc(sizeof(line));//创建链表第一个结点（首元结点）
    head->prior=NULL;
    head->next=NULL;
    head->data=1;
    line * list=head;
    for (int i=2; i<=3; i++) {
        //创建并初始化一个新结点
        line * body=(line*)malloc(sizeof(line));
        body->prior=NULL;
        body->next=NULL;
        body->data=i;
      
        list->next=body;//直接前趋结点的next指针指向新结点
        body->prior=list;//新结点指向直接前趋结点
        list=list->next;
    }
    return head;
}
```
## 添加节点
```c
line * insertLine(line * head,int data,int add){
    //新建数据域为data的结点
    line * temp=(line*)malloc(sizeof(line));
    temp->data=data;
    temp->prior=NULL;
    temp->next=NULL;
    //插入到链表头，要特殊考虑
    if (add==1) {
        temp->next=head;
        head->prior=temp;
        head=temp;
    }else{
        line * body=head;
        //找到要插入位置的前一个结点
        for (int i=1; i<add-1; i++) {
            body=body->next;
        }
        //判断条件为真，说明插入位置为链表尾
        if (body->next==NULL) {
            body->next=temp;
            temp->prior=body;
        }else{
            body->next->prior=temp;
            temp->next=body->next;
            body->next=temp;
            temp->prior=body;
        }
    }
    return head;
}
```
## 删除节点
```c
//删除结点的函数，data为要删除结点的数据域的值
line * delLine(line * head,int data){
    line * temp=head;
    //遍历链表
    while (temp) {
        //判断当前结点中数据域和data是否相等，若相等，摘除该结点
        if (temp->data==data) {
            temp->prior->next=temp->next;
            temp->next->prior=temp->prior;
            free(temp);
            return head;
        }
        temp=temp->next;
    }
    printf("链表中无该数据元素");
    return head;
}
```
## 查找节点
```c
//双链表查找指定元素的实现同单链表类似，都是从表头依次遍历表中元素。
//head为原双链表，elem表示被查找元素
int selectElem(line * head,int elem){
//新建一个指针t，初始化为头指针 head
    line * t=head;
    int i=1;
    while (t) {
        if (t->data==elem) {
            return i;
        }
        i++;
        t=t->next;
    }
    //程序执行至此处，表示查找失败
    return -1;
}
```
## 更改节点
```c
//更新函数，其中，add 表示更改结点在双链表中的位置，newElem 为新数据的值
line *amendElem(line * p,int add,int newElem){
    line * temp=p;
    //遍历到被删除结点
    for (int i=1; i<add; i++) {
        temp=temp->next;
    }
    temp->data=newElem;
    return p;
}
```
# 循环链表
将表中最后一个结点的指针指向头结点，虽然循环链表成环状，但本质上还是链表，因此在循环链表中，依然能够找到头指针和首元节点等。循环链表和普通链表相比，唯一的不同就是循环链表首尾相连，其他都完全一样。
## 约瑟夫环
已知 n 个人（分别用编号 1，2，3，…，n 表示）围坐在一张圆桌周围，从编号为 k 的人开始顺时针报数，数到 m 的那个人出列；他的下一个人又从 1 开始，还是顺时针开始报数，数到 m 的那个人又出列；依次重复下去，直到圆桌上剩余一个人。
```c
#include <stdio.h>
#include <stdlib.h>
typedef struct node
{
    int number;
    struct node *next;
} person;
person *initLink(int n)
{
    person *head = (person *)malloc(sizeof(person));
    head->number = 1;
    head->next = NULL;
    person *cyclic = head;
    for (int i = 2; i <= n; i++)
    {
        person *body = (person *)malloc(sizeof(person));
        body->number = i;
        body->next = NULL;
        cyclic->next = body;
        cyclic = cyclic->next;
    }
    cyclic->next = head; //首尾相连
    return head;
}

void findAndKillK(person *head, int k, int m)
{

    person *tail = head;
    //找到链表第一个结点的上一个结点，为删除操作做准备
    while (tail->next != head)
    {
        tail = tail->next;
    }
    person *p = head;
    //找到编号为k的人
    while (p->number != k)
    {
        tail = p;
        p = p->next;
    }
    //从编号为k的人开始，只有符合p->next==p时，说明链表中除了p结点，所有编号都出列了，
    while (p->next != p)
    {
        //找到从p报数1开始，报m的人，并且还要知道数m-1de人的位置tail，方便做删除操作。
        for (int i = 1; i < m; i++)
        {
            tail = p;
            p = p->next;
        }
        tail->next = p->next; //从链表上将p结点摘下来
        printf("出列人的编号为:%d\n", p->number);
        free(p);
        p = tail->next; //继续使用p指针指向出列编号的下一个编号，游戏继续
    }
    printf("出列人的编号为:%d\n", p->number);
    free(p);
}

int main()
{
    printf("输入圆桌上的人数n:");
    int n;
    scanf("%d", &n);
    person *head = initLink(n);
    printf("从第k人开始报数(k>1且k<%d)：", n);
    int k;
    scanf("%d", &k);
    printf("数到m的人出列：");
    int m;
    scanf("%d", &m);
    findAndKillK(head, k, m);
    return 0;
}
```