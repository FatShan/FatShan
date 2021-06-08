# DLL

## 如何建立DLL

**动态链接库（DLL）**是从C语言函数库和Pascal库单元的概念发展而来的。所有的C语言标准库函数都存放在某一函数库中。在链接应用程序的过程中，链接器从库文件中拷贝程序调用的函数代码，并把这些函数代码添加到可执行文件中。这种方法同只把函数储存在已编译的OBJ文件中相比更有利于**代码的重用**。但随着Windows这样的多任务环境的出现，函数库的方法显得过于累赘。如果为了完成屏幕输出、消息处理、内存管理、对话框等操作，每个程序都不得不拥有自己的函数，那么Windows程序将变得非常庞大。Windows的发展要求允许同时运行的几个程序共享一组函数的单一拷贝。动态链接库就是在这种情况下出现的。动态链接库不用重复编译或链接，一旦装入内存，DLL函数可以被系统中的任何正在运行的应用程序软件所使用，而不必再将DLL函数的另一拷贝装入内存。

下面我们一步一步来建立一个DLL。

### 一、建立一个DLL工程
​	新建一个工程，选择Win32 控制台项目（Win32 Console Application），并且在应用程序设置标签（the advanced tab）上，选择DLL和空项目选项。

### 二、声明导出函数
​	这里有两种方法声明导出函数：一种是通过使用`__declspec(dllexport)`，添加到需要导出的函数前，进行声明；另外一种就是通过模块定义文件（Module-Definition File即.DEF）来进行声明。
  第一种方法，建立头文件DLLSample.h，在头文件中，对需要导出的函数进行声明。

```cpp
#ifndef _DLL_SAMPLE_H
#define _DLL_SAMPLE_H

// 如果定义了C++编译器，那么声明为C链接方式
#ifdef __cplusplus
extern "C" {
#endif

// 通过宏来控制是导入还是导出
#ifdef _DLL_SAMPLE
#define DLL_SAMPLE_API __declspec(dllexport)
#else
#define DLL_SAMPLE_API __declspec(dllimport)
#endif

// 导出/导入函数声明
DLL_SAMPLE_API void TestDLL(int);

#undef DLL_SAMPLE_API

#ifdef __cplusplus
}
#endif

#endif
```

这个头文件会分别被DLL和调用DLL的应用程序引入，当被DLL引入时，在DLL中定义\_DLL_SAMPLE宏，这样就会在DLL模块中声明函数为导出函数；当被调用DLL的应用程序引入时，就没有定义_DLL_SAMPLE，这样就会声明头文件中的函数为从DLL中的导入函数。 

### 三、编写DllMain函数和导出函数

​	DllMain函数是DLL模块的默认入口点。当Windows加载DLL模块时调用这一函数。系统首先调用全局对象的构造函数，然后调用全局函数DLLMain。DLLMain函数不仅在将DLL链接加载到进程时被调用，在DLL模块与进程分离时（以及其它时候）也被调用。

```cpp
#include "stdafx.h"
#define _DLL_SAMPLE

#ifndef _DLL_SAMPLE_H
#include "DLLSample.h"
#endif

#include "stdio.h"

//APIENTRY声明DLL函数入口点
BOOL APIENTRY DllMain(HANDLE hModule, DWORD ul_reason_for_call, LPVOID lpReserved)
{
　switch (ul_reason_for_call)
　{
　　case DLL_PROCESS_ATTACH:
　　case DLL_THREAD_ATTACH:
　　case DLL_THREAD_DETACH:
　　case DLL_PROCESS_DETACH:
　　　break;
　}
　return TRUE;
}

void TestDLL(int arg)
{
  printf("DLL output arg %d\n", arg);
}
```

如果程序员没有为DLL模块编写一个DLLMain函数，系统会从其它运行库中引入一个不做任何操作的缺省DLLMain函数版本。在单个线程启动和终止时，DLLMain函数也被调用。

然后，F7编译，就得到一个DLL了。

原文地址：[DLL入门浅析（1）——如何建立DLL](http://www.cppblog.com/suiaiguo/archive/2009/07/20/90619.html)

## \__declspec(dllexport)与__declspec(dllimport)

他们都是DLL内的关键字，即导出与导入。他们是将DLL内部的类与函数以及数据导出与导入时使用的。

`dllexport`是在这些类、函数以及数据的申明的时候使用。用他表明这些东西可以被外部函数使用，即`（dllexport）`是把 DLL中的相关代码（类，函数，数据）暴露出来为其他应用程序使用。使用了`（dllexport）`关键字，相当于声明了紧接在`（dllexport）`关键字后面的相关内容是可以为其他程序使用的。

dllimport是在外部程序需要使用DLL内相关内容时使用的关键字。当一个外部程序要使用DLL 内部代码（类，函数，全局变量）时，只需要在程序内部使用`（dllimport）`关键字声明需要使用的代码就可以了，即`（dllimport）`关键字是在外部程序需要使用DLL内部相关内容的时候才使用。`（dllimport）`作用是把DLL中的相关代码插入到应用程序中。

`__declspec(dllexport)`与``__declspec(dllimport)``是相互呼应，只有在DLL内部用`dllexport`作了声明，才能在外部函数中用`dllimport`导入相关代码。

常见用法

在为方便使用，我们经常在代码中定义宏`DLL_EXPORT`，此宏用在需要导出的类和函数前，而此宏我们定义如下：

```cpp
#ifdef DLL_EXPORTS
 #define SIMPLE_CLASS_EXPORT __declspec(dllexport)
#else
 #define SIMPLE_CLASS_EXPORT __declspec(dllimport)
#endif
```

作为动态库，在需要导出的类或函数前必须使用关键字`__declspec(dllexport)`声明，因此动态库需要定义宏`DLL_EXPORTS`(使用Vistualstudio建立动态库工程时，此宏已经定义好)。

  应用程序需要使用关键字`__declspec(dllimport)`，因此不能定义宏`DLL_EXPORTS`。



# 附加知识点

## extern "C"

extern "C"的主要作用就是为了能够正确实现C++代码调用其他C语言代码。加上extern "C"后，会指示编译器这部分代码按C语言（而不是C++）的方式进行编译。由于C++支持函数重载，因此编译器编译函数的过程中会将函数的参数类型也加到编译后的代码中，而不仅仅是函数名；而C语言并不支持函数重载，因此编译C语言代码的函数时不会带上函数的参数类型，一般只包括函数名。



*看下面的一个面试题：为什么标准头文件都有类似的结构？*

```cpp
#ifndef __INCvxWorksh /*防止该头文件被重复引用*/
#define __INCvxWorksh
#ifdef __cplusplus             //告诉编译器，这部分代码按C语言的格式进行编译，而不是C++的
extern "C"{
#endif
 
/*…*/
 
#ifdef __cplusplus
}
 
#endif
#endif /*end of __INCvxWorksh*/
```

**extern "C"包含双重含义**，从字面上可以知道，首先，被它修饰的目标是"extern"的；其次，被它修饰的目标代码是"C"的。

- 被extern "C"限定的函数或变量是extern类型的

extern是C/C++语言中表明函数和全局变量的作用范围的关键字，该关键字告诉编译器，其申明的函数和变量可以在本模块或其他模块中使用。

**记住**，语句：**extern int a; 仅仅是一个变量的声明，其并不是在定义变量a，也并未为a分配空间。变量a在所有模块中作为一种全局变量只能被定义一次，否则会出错。**

通常来说**，在模块的头文件中对本模块提供给其他模块引用的函数和全局变量以关键字extern生命。**例如，如果模块B要引用模块A中定义的全局变量和函数时只需包含模块A的头文件即可。这样模块B中调用模块A中的函数时，在编译阶段，模块B虽然找不到该函数，但并不会报错；它会在链接阶段从模块A编译生成的目标代码中找到该函数。

**extern对应的关键字是static，static表明变量或者函数只能在本模块中使用，因此，被static修饰的变量或者函数不可能被extern C修饰。**

- **被extern "C"修饰的变量和函数是按照C语言方式进行编译和链接的：这点很重要！！！！**

上面也提到过，由于C++支持函数重载，而C语言不支持，因此函数被C++编译后在**符号库**中的名字是与C语言不同的；C++编译后的函数需要加上参数的类型才能唯一标定重载后的函数，而加上extern "C"后，是为了向编译器指明这段代码按照C语言的方式进行编译

未加extern "C"声明时的链接方式：

```cpp
//模块A头文件 moduleA.h
#idndef _MODULE_A_H
#define _MODULE_A_H
 
int foo(int x, int y);
#endif　
```

在模块B中调用该函数

```cpp
//模块B实现文件 moduleB.cpp
#include"moduleA.h"
foo(2,3);
```

实际上，**在链接阶段，链接器会从模块A生成的目标文件moduleA.obj中找_foo_int_int这样的符号，显然这是不可能找到的，因为foo()函数被编译成了_foo的符号，因此会出现链接错误。**

**extern "C"的使用要点总结**

1，可以是如下的单一语句：

```cpp
extern "C" double sqrt(double);
```

2，可以是复合语句, 相当于复合语句中的声明都加了extern "C"

```
extern "C"
{
      double sqrt(double);
      int min(int, int);
}
```

3，可以包含头文件，相当于头文件中的声明都加了extern "C"

```cpp
extern "C"
{
    ＃include <cmath>
}　
```

- 不可以将extern "C" 添加在函数内部
- 如果函数有多个声明，可以都加extern "C", 也可以只出现在第一次声明中，后面的声明会接受第一个链接指示符的规则。
- 除extern "C", 还有extern "FORTRAN" 等。



## _MSC_VER

\_MSC_VER是微软公司推出的C/C++编译器在ANSI/ISO C99标准之外扩展的宏定义，用来定义当前微软公司自己的编译器的主版本。需要注意的是，这并不是Visual Studio 的版本号，也不是Visual C++的版本号。如Visual Studio 2005的Vistual C++版本为8.0，所附带编译器的\_MSC_VER定义是1400；最新的Visual Studio 2015的Visual C++版本为14.0，相应_MSC_VER为1900。



在程序中加入\_MSC_VER宏可以根据编译器版本让编译器选择性地编译一段程序。例如一个版本编译器产生的lib文件可能不能被另一个版本的编译器调用，那么在开发应用程序的时候，在该程序的lib调用库中放入多个版本编译器产生的lib文件。在程序中加入_MSC_VER宏，编译器就能够在调用的时根据其版本自动选择可以链接的lib库版本，如下所示。

```cpp
#if _MSC_VER >= 1400 // for vc8, or vc9
 #ifdef _DEBUG
  #pragma comment( lib, "SomeLib-vc8-d.lib" )
 #elif
  #pragma comment( lib, "SomeLib-vc8-r.lib" )
 #endif
#elif _MSC_VER >= 1310 // for vc71
 #ifdef _DEBUG
  #pragma comment( lib, "SomeLib-vc71-d.lib" )
 #elif
  #pragma comment( lib, "SomeLib-vc71-r.lib" )
 #endif
#elif _MSC_VER >=1200 // for vc6
 #ifdef _DEBUG
  #pragma comment( lib, "SomeLib-vc6-d.lib" )
 #elif
  #pragma comment( lib, "SomeLib-vc6-r.lib" )
 #endif
#endif
```

