#C++ STL
[TOC]
##Preface
```
 总结<<大道至简—— C++ STL(标准模版库)精解>>中的知识点。
```
#Chapter 1预备知识与简介
##基本概念
###命名空间
 > 1.C++ STL 内的所有标识符都被定义为一个名为**std**的命名空间中。
 > 2.可以使用语句 **using namespace std;** 。

###头文件
> 1.C语言中，头文件是以**.h**的形式存在的。
> 2.C++中，头文件只有名字，没有扩展名。

###函数中的变量
> 1.**auto**类型是默认类型，可以省略不写。
> 2.**static**类型属于静态存储类型。会使函数中的局部变量的值在函数调用结束后不消失。
> 3.**register**类型，允许将局部变量的值放在CPU寄存器中。
> 4.**extern**类型，外部变量声明，表示该变量是一个已经声明了的外部变量。

###文件
####封装
> C++语言中，关于文件的处理功能更加高级一新封装了部分模板和部分类。常用的类如下:
>  **ifstream**和**wifstream**用来读取文件;其中**wifstream**是字读取操作。
> **ofstream**和**wofstream**用来将数据写入文件;其中**wofstream**是字读取操作。
> **fstream**和**wfstream**用于读写文件;其中**wfstream**是字读取操作。
> **filebuf**和**wfilebuf**用于进行实际的字符读写;其中**wfilebuf**是字读写操作类。
> *注意* :使用上述几个类时，需要包含头文件 **\<fstream\>** 。

####文件标识
> **ios_base::in**      打开文件，用于读取 。
> **ios_base::out**     打开文件，用于改写或写入 。
> **ios_base::ate**      打开文件，用于将文件指针移到文件末尾。
> **ios_base::binary**     打开文件，以二进制形式打开文件。

eg.从源文件读取内容写入目标文件：
```C++
#include <iostream>
#include <fstream>
using namespace std;

int main(int argc, const char * argv[]) {
    ifstream f1;
    ofstream f2;
    
    char filename1[256];
    char filename2[256];
    char content[256];
    cout<<"请输入文件名(源):";
    cin>>filename1;
    cout<<"请输入文件名(目的):";
    cin>>filename2;
    f1.open(filename1,ios::in);
    f2.open(filename2,ios::out);
    while (!f1.eof()) {
        f1.getline(content, 128);   //读取源文件中的数据
        f2<<content<<endl;          //将数据写入目标文件
        
    }
    f1.close();
    f2.close();
}
```
####文件流状态检查
> **fail( );**
> **is_open( );**
> **eof( );**

####使用临时文件
> 1.在软件开发过程中，程序员经常需要使用临时文件。临时文件的存在是短暂的，且必须受程序控制。在C++中，创建临时文件、复制另一个文件的内容并删除文件其实都很简单。首先，需要为临时文件制订一个命名方案，确保每个文件都被指定独一无二的文件名。**cstdio**库函数中声明的 **tmpnam( )**标准函数可以满足这一要求。
> 2. 函数原型: **char* tmpnam(char* pszname)**。
> 3.**tmpnam( )**函数创建一个临时文件名(注意:仅仅产生一个文件名)，将它放在**pszname**指向的**C-**风格字符串中。常量 **L_tmpnam**和**TMP_MAX**限制了文件名包含的字符数以及在确保当前目录中不生成重复文件名的情况下，**tmpnam( )**可被调用的最多次数。

eg.从源文件读取内容写入目标文件：
```C++
#include <iostream>
#include <cstdio>
using namespace std;

int main(int argc, const char * argv[]) {
    cout<<"This system can generate "<<TMP_MAX<<" temporary name of up to "<<L_tmpnam<<" characters. \n";
    char pszName[L_tmpnam]={'\0'};
    cout<<"Here are the names:\n";
    for (int i=0; i<10; i++) {
        tmpnam(pszName);
        cout<<pszName<<endl;
    }
    return 0;
}
```
add.解决报错问题
```C++
/*
 1.template 字符串最后六个字符非XXXXXX
 2.参数template所指的文件名称字符串必须声明为数组，如:
    char template[ ] =”template-XXXXXX”;
 不可以使用下列的表达方式:
    char *template = “template-XXXXXX”;
 3.与mktemp函数区别：mkstemp返回值为文件描述符，mktemp返回值为文件名指针。
 4.mkstemp函数创建的临时文件不能自动删除，所以执行完mkstemp函数后要调用unlink函数，unlink函数删除文件的目录入口。
 */
#include <iostream>
#include <cstdio>
using namespace std;

int main(int argc, const char * argv[]) {
    int fd;
    cout<<"Here are the names:\n";
    for (int i=0; i<10; i++) {
        char pszName[L_tmpnam]="temp-XXXXXX";
        mkstemp(pszName);
        cout<<pszName<<endl;
    }
    return 0;
}
```
