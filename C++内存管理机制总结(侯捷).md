# C++内存管理机制

@[toc]
## primitives

### 内存分配的每一个层次

malloc() 	new ::operator	 new() 	allocator<T>::allocate()

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-6LAAlUVI-1651486346481)(C:\Users\whmhurry\AppData\Roaming\Typora\typora-user-images\1651323700502.png)]

```c++
A* buf = new A(3);//只能调用A的默认构造函数
A* tmp = buf;

for(int i = 0; i < size; ++i){
    new(tmp++)A(1);// 这里在tmp++的地址上创建对象
}
delete[] buf;
```



### placement new

```c++
new(tmp)A(1);
```



### 重载

```c++
//应用程序 不可改变，不可重载
Foo* p = new Foo(x);
delete p;

//可以重载
Foo* p = (Foo*)operator new(sizeof(Foo));->::operator new(size_t);->malloc(size_t);
new(p)Foo(x);
...
p->~Foo();
operator delete(p);->operator delete(void*);->free(void*)
```



![在这里插入图片描述](https://img-blog.csdnimg.cn/791e52fa586247088dee6e2c56cb86d5.png#pic_center)






### 编译器行为

![在这里插入图片描述](https://img-blog.csdnimg.cn/4a6251469ec74c53afced2b417c91483.png#pic_center)




### alloctor

![在这里插入图片描述](https://img-blog.csdnimg.cn/877f9a155f7b4ce59d7f844b11fffabc.png#pic_center)




### per-class allocator

```c++
class Airplane{
private:
    union{
        AirplaneRep rep;
        Airplane* next;
    }
};
Airplane* newBlock = static_cast<Airplane*>(::operator new(BLOCK_SIZE*sizeof(Airplane)));
for(int i = 1; i < BLOCK_SIZE - 1; ++i){
    newBlock[i].next = &newBlock[i+1];
}
```



### static allocator

```c++
class allocator{
public:
    void* allocate(size_t);
    void deallocate(void*, size_t);
};

class Foo{
private:
    static allocator myAlloc;
public:
    static void* operator new(size_t size){
        return myAlloc.allocate(size);
    }
    static void operator delete(void* pdead, size_t size){
        return myAlloc.allocte(pdead, size);
    }
};
allocator Foo::myAlloc;
```



### Macro for static allocator

```c++
#define DECLARE_POOL_ALLOC()\
public:\
	static void* operator new(size_t size){
        return myAlloc.allocate(size);
    }
    static void operator delete(void* pdead, size_t size){
        return myAlloc.allocte(pdead, size);
    }
private:\
	static allocator myAlloc;

#define IMPEMENT_POOL_ALLOC(class_name)\
allocator class_name::myAlloc;

//调用
class Foo{
    DECLARE_POOL_ALLOC()
};
IMPEMENT_POOL_ALLOC(Foo)
```



### new handle

```c++
//内存不足抛出异常之前会调用
typedef void(*new_handler){cout,,, abort();};
new_hander set_new_handler(new_handler p) throw();
```



## std::allocator

G2.9

```c++
template<class T, class Alloc = alloc>
class vector{};
void* p = alloc::allocate(512);//512bytes
vector<string, alloc<string>>
```



G4.9

```c++
vector<string, _gnu_cxx_pool_alloc<string>>

```



### embedded pointers



### std::alloc

```
__default_alloc_template<threads, inst>::free_list[__NFREELISTS]={0,...0};
typedef __default_alloc_template<false, 0> alloc;

```



### std::alloc源码

```c++
//第二级分配器
enum{__ALIGN = 8};//上调边界
template<bool threads, int inst>//两个参数都没有用
class __default_alloc_template{
    
};
//在chunk内建立free-list
result = (obj*)chunk;
*my_free_list = next_obj = (obj*)(chunk + n);
for(i = 1; ; ++i){
    current_obj = next_obj;
    next_obj = (obj*)((char*)next_obj + n);
    if(nobjs - 1 == i){//最后一个
        current_obj->free_list_link = 0;
        break;
    }else{
        current_obj->free_list_link = next_obj;
    }
}

//chunk_alloc函数
//内存不足,往右边找
for(i = size; i <= __MAX_BYTES； i+=__ALIGN){
    my_free_list = free_list + FREELIS_INDEX(i);
    p = *my_free_list;
    if(NULL != p){
        *my_free_list = p->free_list_link;
        start_free = (char*)p;
        end_free = start_free + i;
        return chunk_alloc(size, nobjs);
    }
}
end_free = 0;// memory已经山穷水尽
start_free = (char*)malloc_alloc::allocate(bytes_to_get);//这里会爆出异常

```



## malloc/free

![在这里插入图片描述](https://img-blog.csdnimg.cn/c68e3b8cbbfa4532aefdbf25f28944a0.png#pic_center)


```c++
mainCRTStartup()
_heap_init()
_ioinit()
GetCommandLineA()
    _heap_alloc_base()

```



 _heap_alloc_base()

```c++
if(size<=_sbh_threshold){
	pvReturn = _sbh_alloc_block(size);//小块内存
	if(pvReturn) return pvReturn;
}
if(size == 0) size = 1;
return HeapAlloc(_crtheap, 0, size);// 操作系统给的

```



_heap_init()

```c++
{
	if(_crtheap = HeapCreate(mtflag...)) == NULL)
}

```



_ioinit()

```c++
void _cdecl_ioinit(void){
	...
	if((pio = malloc_crt(IOINFO_ARRAY_ELTS * sizeof(ioinfo)))==NULL)
}

#ifdef _DEBUG
#define _malloc_crt malloc
...
#else
#define _THISFILE _FILE_
#define _malloc_crt(s) _malloc_dbg(s, _CRT_BLOCK, _THISFILE, _LINE_)
...
#endif

```



__sbh_alloc_new_region()用于管理1MB内存 16K

__sbh_alloc_new_group()

1024K / 32 = 32K 由32个group管理 每个group中有64个指针 

32K拆分为8个page管理 4k为1page 16为一个paragraph



```
typedef struct tagGroup{
	int cntEntries;
	struct tagListHead listHead[64];
}GROUP, *PGROUP;

```



```
typedef struct tagListHead{
	struct tagEntry * pEntryNext;
	struct tagEntry * pEntryPrev;
}LISTHEAD,*PLISTHEAD;

```



```
typedef struct tagEntry{
	int sizeFront;
	struct tagEntry* pEntryNext;
	struct tagEntry* pEntryPrev;
};

```



ioinit（100H）+debug（24H）+cookie（8H）->调到16的边界 = 130H 

给出去变为131H借由最后一位进行标记

debug（24H）= 两个指针指向前后+ 一根指针指向（“ioinit.c"）+一根指针指向文件第？行发出的需求+100h内存+_CRT_BLOCK(表示给CRT使用)

ff0 - 130 = ec0

130h / 16 - 1 = 18 由第18list提供



### 总体具体步骤

1024K / 32 = 32K 由32个group管理 每个group中有64个指针 

32K拆分为8个page管理 4k为1page (16为一个paragraph)

1page中

第一次分配

ioinit（100H）+debug（24H）+cookie（8H）->调到16的边界 = 130H 

给出去变为131H借由最后一位进行标记

debug（24H）= 两个指针指向前后+ 一根指针指向（“ioinit.c"）+一根指针指向文件第？行发出的需求+100h内存+_CRT_BLOCK(表示给CRT使用)

剩余：ff0H - 130H = ec0H

第二次分配

getenvironmentstring环境变量 占240H 剩C80H 仍挂最后一个链表

第三次分配

占70H 剩C10H 仍挂最后一个链表

...

第15次释放

释放240h内存（第二次分配得到）240h/10h = 36  利用嵌入式指针 挂到35号链表中

注意：

每次分配指针指向16bit 64*16 = 1024bit = 1k最后一个指针指向大于1k的内存（剩余内存）count++，每次释放



剩余：ec0

1.HeadAlloc(,,16*sizeof(HEADER))（共16个header）分配一个header

2.p = VirtualAlloc(0, 1Mb, MEM_RESERVE,...)

3.HeapAlloc(_crtheap,,sizeof(REGION));//32group

4.virtualAlloc(addr, 32Kb, MEM_COMMIT,...)



### 内存合并

上cookie和下cookie的作用



### 全回收

_sbh_pHeaderDefer指向一个全回收所属的Header

_sbh_indGroupDefer指向哪一个group



## loki::allocator

### 基本结构

[外链图片转存失败,源站可能有防盗链机制,建议将图片保存下来直接上传(img-PJyzl0d6-1651486346484)(C:\Users\whmhurry\AppData\Roaming\Typora\typora-user-images\1651403497745.png)]

#### FixedAllocator::Chunk::Init

```c++
void FixedAllocator::Chunk::Init(std::size_t blockSize, unsigned char blocks){
	pData_ = new unsigned char[blockSize * blocks];
    Reset(blockSize, blocks);
}

```



#### FixedAllocator::Chunk::Reset

```c++
void FixedAllocator::Chunk::Reset(std::size_t blockSize, unsigned char blocks){
    firstAvailableBlock_ = 0;
    blocksAvailable_ = blockSize;
    unsigned char i = 0;
    unsigned char* p = pData_;
    for(;i != blocks; p+=blockSize)
        *p = ++i;
}

```



#### FixedAllocator::Chunk::Release

```c++
void FixedAllocator::Chunk::Release(){
    delete[] pData_;
}

```



#### FixedAllocator::Chunk::Allocate

![在这里插入图片描述](https://img-blog.csdnimg.cn/a8904049817f4bb6879fdf6aea55aa8e.png#pic_center)




#### FixedAllocator::Chunk::Deallocate

![在这里插入图片描述](https://img-blog.csdnimg.cn/88e33938a6a1464aa18613d59622571a.png#pic_center)




## other allocators

### GNU C++ 对于Allocator 的描述

容器的Allocator模板实参默认为allocator<T>

接口中的成员函数重要的有，T* allocate(size_type n, const void* hind = 0); void deallocate(T* p, size_type n);

#### 常见

__gnu_cxx::new_allocator

__gnu_cxx::malloc_allocator

#### 智能型

内存池：fixed-size pooling cache、bitmap_alloctor



##### array_alloctor

![在这里插入图片描述](https://img-blog.csdnimg.cn/9c3c3b2631b348b6af3f1427bb46f49e.png#pic_center)


exemple

![在这里插入图片描述](https://img-blog.csdnimg.cn/45e7cbcdc31f404f8c091a5a75ba928c.png#pic_center)




#### _pool_alloc

GNU4.9: vector<string, __gnu_cxx::_pool_alloc<string>> vec;

test:

![在这里插入图片描述](https://img-blog.csdnimg.cn/7820ee4638f24f109ad078e838eeaf71.png#pic_center)




#### bitmap_alloctor

blocks(64初始)

super-blocks =use count(使用容量) + bitmap[1] +  bitmap[0]  + blocks

_mini_vector

![在这里插入图片描述](https://img-blog.csdnimg.cn/2592126ef9a54ad48e9e6342a2d90e67.png#pic_center)




回收

![在这里插入图片描述](https://img-blog.csdnimg.cn/1c4d94912ccf41599aa713524b7e9124.png#pic_center)



