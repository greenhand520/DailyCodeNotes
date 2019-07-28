### malloc函数

全名`void * malloc(size_t size)`，动态分配内存，作用是向系统申请指定size字节大小的内存。malloc的返回类型是void*类型。void*表示为确定类型的指针。C/C++规定void*类型可以强制转换为任何其它类型的指针。

如果分配成功则返回指向被分配内存的指针，既被分配内存空间的首地址，否则返回空指针NULL。当内存不再使用时，用free()函数将内存块释放，否则可能出现内存泄露。并且，在调用malloc函数动态分配内存时，**一定要注意其返回值的判断**。

如果由malloc()函数分配的内存空间原来没有被使用过，则其中的每一位可能都是0;反之,如果这部分内存曾经被分配过,则其中可能遗留有各种各样的数据。也就是说，使用malloc()函数的程序开 始时(内存空间还没有被重新分配)能正常进行,但经过一段时间(内存空间还已经被重新分配)可能会出现问题。

一般这么用

“(分配类型 *)malloc(分配元素个数 *sizeof(分配类型))”

举例：

```c
int* p = (int *) malloc (sizeof(int)); 
```

第一、malloc 函数返回的是 void * 类型，如果你写成：p = malloc (sizeof(int)); 则程序无法通过编译，报错：“不能将 void* 赋值给 int * 类型变量”。所以必须通过 (int *) 来将强制转换。 

第二、函数的实参为 sizeof(int) ，用于指明一个整型数据需要的大小。如果你写成： 

int* p = (int *) malloc (1); 

代码也能通过编译，但事实上只分配了1个字节大小的内存空间，当你往里头存入一个整数，就会有3个字节无家可归，而直接“住进邻居家”！造成的结果是后面的内存中原有数据内容全部被清空。  

```c
//初始化List
Status initList(SqList *list) {

    (*list).elem = (SqListElemType *) malloc(LIST_INIT_SIZE * sizeof(SqListElemType));
    if (!(*list).elem) {   //存储分配失败
        exit(OVERFLOW);
    }
    (*list).length = 0;
    (*list).spaceSize = LIST_INIT_SIZE;
    return OK;
}
```

### realloc函数

全名`void * realloc(void *__ptr, size_t __size)`

基本特性

1、 realloc()函数可以重用或扩展以前用malloc()、calloc()及realloc()函数自身分配的内存。

2、 realloc()函数需两个参数：一个是包含地址的指针（该地址由之前的malloc()、calloc()或realloc()函数返回），另一个是要新分配的内存字节数。

3、 realloc()函数分配第二个参数指定的内存量，并把第一个参数指针指向的之前分配的内容复制到新配的内存中，且复制的内容长度等于新旧内存区域中较小的那一个。即新内存大于原内存，则原内存所有内容复制到新内存，如果新内存小于原内存，只复制长度等于新内存空间的内容。

 4、realloc()函数的第一个参数若为空指针，相当于分配第二个参数指定的新内存空间，此时等价于malloc()、calloc()或realloc()函数。

5、如果是将分配的内存扩大，则有以下3种情况：

1)如果当前内存段后面有需要的内存空间，则直接扩展这段内存空间，realloc()将返回原指针。  
2)如果当前内存段后面的空闲字节不够，那么就使用堆中的第一个能够满足这一要求的内存块，将目前的数据复制到	新的位置，并将原来的数据块释放掉，返回新的内存块位置。
3)如果申请失败，将返回NULL，此时，原来的指针仍然有效。

注意

 1、第一个参数要么是空指针，要么是指向以前分配的内存。如果不指向以前分配的内存或指向已释放的内存，结果就是不确定的。

 2、 如果调用成功，不管当前内存段后面的空闲空间是否满足要求，都会释放掉原来的指针，重新返回一个指针，虽然返回的指针有可能和原来的指针一样，即不能再次释放掉原来的指针。

举例：

```c
//在List的第i位置之前插入元素
Status insertElem(SqList *list, int i, SqListElemType elem) {

    if (i > 0 && i <= (*list).length + 1) {    //i值在合理范围内继续插入，List最后一个元素后面还能继续插入一个
        if ((*list).length >= (*list).spaceSize) {
            //这里使用realloc（）函数，而不是malloc()函数，newLisPointer新分配内存的指针
            SqListElemType *newListPointer = (SqListElemType *) realloc((*list).elem,
                    ((*list).spaceSize + LIST_INCREMENT) *sizeof(SqListElemType));
            if (!newListPointer) {
                exit(OVERFLOW);
            }
            (*list).elem = newListPointer;
            (*list).spaceSize += LIST_INCREMENT;
        }
        //首先将插入位置及插入位置后面的元素后移，才能给插入位置的元素赋值
        //从List的末尾开始移动
        for (int j = (*list).length; j >= i; j--) {      //假如在第3个位置前插入元素，最后一步是将第2个位置元素移动到第个3位置去，所以j要能取到3
            (*list).elem[j] = (*list).elem[j - 1];
        }
        (*list).elem[i - 1] = elem;
        (*list).length++;
        return OK;
    }
    return ERROR;
}
```

