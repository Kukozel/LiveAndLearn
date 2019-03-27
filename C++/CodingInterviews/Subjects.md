#CodingInterviews
[TOC]
##Preface
```
 总结《剑指Offer》 图书中的试题。
 (只含C++部分)
```

#Subjects
##00_复制构造函数问题
###Code
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
###Explain
* Output:在Visual Studio 和 GCC 中将编译出错。
* Result:C++的标准中不允许复制构造函数传值参数，否则会在复制构造函数内无休止递归调用复制构造函数，从而导致栈溢出。
* Correct:将构造函数更改为A(const A& other)

- - - - -
##01_添加赋值运算符函数
###Code
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
###Explain
* 在这个函数中，我们先创建一个临时实例 strTemp，接着把 strTemp.mpData和实例例自身的m_pdata进行交换。由于 strTemp是一个局部变量，但程序运行到if的外面时也就出了该变量的作用域，就会自动调用strTemp的析构函数，把strTemp.m_pData所指向的内存释放掉。由于strTemp.m_pData指向的内存就是实例之前m_pData的内存，这就相当于自动调用析构函数释放实例例的内存。
* 在新的代码中，我们在CMyString的构造函数里用new分配内存。如果由于内存不足抛出诸如 bad_alloc等异常，但我们还没有修改原来实例的状态，因此实例的状态还是有效的，这也就保证了异常安全性。

- - - - -
##02_数组及数组指针大小问题
###Code
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
###Explain
* Output:20,4,4
*   Result:data1是一个数组,sizeof(data1)是求数组的大小。这个数组包含5个整数,每个整数占4字节,因此共占用20字节;data2声明为指针,尽管它指向了数组data1的第一个数字,但它的本质仍然是-个指针。在32位系统上,对任意指针求sizeof,得到的结果都是4。在C/C++中,当数组作为函数的参数进行传递时,数组就自动退化为同类型的指针因此,尽管函数GetSize的参数data被声明为数组,但它会退化为指针,size3的结果仍然是4。

- - - - -
##03_数组中重复的数字
###找出数组中重复的数字
####Question
* 在一个长度为n的数组里的所有数字都在0~n-1的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中**任意**一个重复的数字。例如，如果输入长度为7的数组{2，3，1，0，2，5，3}，那么对应的输出是重复的数字2或者3。

####Code
```C++
bool duplicate(int numbers[],int length,int* duplication){
    //检查是否为空指针，数组是否为空
    if(numbers==nullptr || length<=0)
        return false;
    //检查数组是否符合要求
    for (int i=0; i<length;i++) {
        if(numbers[i]<0 || numbers[i]>length -1)
            return false;
    }
    
    for (int i=0; i<length; i++) {
        while (numbers[i]!=i) {
            if (numbers[i]==numbers[numbers[i]]) {
                *duplication=numbers[i];
                return true;
            }
            //交换numbers[i]与numbers[numbers[i]]
            int temp=numbers[i];
            numbers[i]=numbers[temp];
            numbers[temp]=temp;
        }
    }
    return false;
}
```
####Explain
* 我们重排这个数组(从头到尾依次扫描这个数组中的每个数字。当扫描到下标为i的数字时，首先比较这个数字(用m表示)是不是等于i。如果是，则接着扫描下一个数字；如果不是，则再拿它和第m个数字进行比较。如果它和第m个数字相等，就找到了一个重复的数字(该数字在下标为i和m的位置都都出现了)；如果它和第m个数字不相等，就把第i个数字和第m个数字交换，把m放到属于它的位置。接下来再重复这个比较交换的过程，直到我们发现一个重复的数字。
* 时间复杂度O(n)，空间复杂度O(1)。

###不修改数组找出重复的数字
####Question
* 在一个长度为n+1的数组里的所有数字都在1~n的范围内，所以数组中至少有一个数字是重复的。请找出数组中任意个重复的数字，但不能修改输入的数组。例如，如果输入长度为8的数组{2，3，5，4，3，2，6，7}，那么对应的输出是重复的数字2或者3。

####Code
```C++
int countRange(const int* numbers,int length,int start,int end){
    if(numbers==nullptr)
        return 0;
    
    int count=0;
    for(int i=0;i<length;i++)
        if(numbers[i]>=start && numbers[i]<=end)
            ++count;
    return count;
}

int getDuplication(const int* numbers,int length){
   
    if (numbers==nullptr || length<=0)
        return -1;
    
    int start=1;
    int end=length-1;
    while(end>=start){
        int middle=((end-start)>>1)+start;
        int count=countRange(numbers,length,start,middle);
        
        if(end==start){
            if(count>1)
                return start;
            else
                break;
        }
        
        if(count>(middle-start+1))
            end=middle;
        else
            start=middle+1;
    }
    return -1;
}

```
####Explain
* 我们把从1~n的数字从中间的数字m分为两部分，前面一半为1～m，后面一半为m+1～n。如果1～m的数字的数目超过m，那么这一半的区间里一定包含重复的数字；否则，另一半m+1～n的区间里一定包含重复的数字。我们可以继续把包含重复数字的区间一分为二，直到找到一个重复的数字。这个过程和二分查找算法很类似，只是多了一步统计区间里数字的数目。
* 这种算法不能保证找出所有的重复的数字。

- - - - -
##04_二维数组中的查找
###Code
###Explain