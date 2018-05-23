#CodingInterviews
[TOC]
##Preface
```
 总结《剑指Offer》 图书中的试题。
 (只含C++部分)
```

##Subjects
###00_复制构造函数问题
####Code
```C++
class A{
private:
	int value;
public:
	A(int n){value=n;}
	A(A other){value=other.value;}
	void Print(){std::cout<<value<<std::endl;}
};

int_tmain(int argc,_TCHAR* argv[]){
	A a=10;
	A b=a;
	b.Print();
	return 0;
}
```
####Explain
* Output:在Visual Studio 和 GCC 中将编译出错。
* Result:C++的标准中不允许复制构造函数传值参数，否则会在复制构造函数内无休止递归调用复制构造函数，从而导致栈溢出。
* Correct:将构造函数更改为A(const A& other)

- - - - -
###01_添加赋值运算符函数
####Code
```C++
class CMyString{
public:
	CMyString(char* pData=nullptr);
	CMyString(const CMyString& str);
	~CMyString(void);
private:
	char* m_pData;
};
```
* Answer(普通解法):
```C++
CMyString& CMyString::operator=(const CMyString &str){
    if(this==&str)
        return *this;
    
    delete[] m_pData;
    m_pData=nullptr;
    
    m_pData=new char[strlen(str.m_pData)+1];
    strcpy(m_pData,str.m_pData);
    
    return *this;
}
```
> 存在问题:内存不足导致new char抛出异常，则m_pData将是一个空指针，容易导致程序崩溃。

* Answer(高级解法):

```C++
CMyString& CMyString::operator=(const CMyString &str){
    if(this!=&str){
        CMyString strTemp(str);
        char* pTemp=strTemp.m_pData;
        strTemp.m_pData=m_pData;
        m_pData=pTemp;        
    }
    return *this;
}
```
####Explain
* 在这个函数中，我们先创建一个临时实例 strTemp，接着把 strTemp.mpData和实例例自身的m_pdata进行交换。由于 strTemp是一个局部变量，但程序运行到if的外面时也就出了该变量的作用域，就会自动调用strTemp的析构函数，把strTemp.m_pData所指向的内存释放掉。由于strTemp.m_pData指向的内存就是实例之前m_pData的内存，这就相当于自动调用析构函数释放实例例的内存。
* 在新的代码中，我们在CMyString的构造函数里用new分配内存。如果由于内存不足抛出诸如 bad_alloc等异常，但我们还没有修改原来实例的状态，因此实例的状态还是有效的，这也就保证了异常安全性。

- - - - -
###02_数组及数组指针大小问题
####Code
```C++
int Getsize(int data[]){
	return sizeof(data); 
} 

int _tmain(int argc, _TCHAR* argv[]){
	int data1[]={1,2,3,4,5};
	int size1=sizeof(data1); 

	int* data2=data1;
	int size2=sizeof(data2);

	int size3=Getsize(data1); 

	printf("%d,%d,%d",size1,size2,size3);
}
```
####Explain
* Output:20,4,4
*   Result:data1是一个数组,sizeof(data1)是求数组的大小。这个数组包含5个整数,每个整数占4字节,因此共占用20字节;data2声明为指针,尽管它指向了数组data1的第一个数字,但它的本质仍然是-个指针。在32位系统上,对任意指针求sizeof,得到的结果都是4。在C/C++中,当数组作为函数的参数进行传递时,数组就自动退化为同类型的指针因此,尽管函数GetSize的参数data被声明为数组,但它会退化为指针,size3的结果仍然是4。

- - - - -
###_
####Code
####Explain
- - - - -