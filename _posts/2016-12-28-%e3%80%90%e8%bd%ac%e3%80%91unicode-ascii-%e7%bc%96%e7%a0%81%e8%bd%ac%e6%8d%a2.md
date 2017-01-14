---
layout: post
title: 【转】Unicode ASCII 编码转换
published: true
author: moli
comments: true
date: 2008-09-14 04:09:00
tags:
    - Moli
    - 免费
    - 免费空间
    - 博客空间
categories:
    - uncategorized
permalink: /archives/173.html
---
这有两个函数! 
  
第一个是将Unicode变成ASCII 
  
第二个是将ASCII变成Unicode 
  
你可以将Unicode变成ASCII后进行操作,然后再ASCII变成Unicode保存 
  
&nbsp;&nbsp;&nbsp; 
    
inline LPTSTR B2A(BSTR lp) 
    
{ 
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; int nLen=(lstrlenW(lp)+1) < >1-1; 
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; BSTR str = ::SysAllocStringLen(NULL, nLen); 
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; MultiByteToWideChar(CP_ACP, 0, lp, -1, str, nLen); 
  
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; return str; 
    
} 


  
    
      仔细做好串处理 &nbsp;&nbsp;&nbsp; 这一部分将花点时间来讨论如何在COM代码中处理串。如果你熟悉Unicode 和ANSI，并知道如何对它们进行转换的话，你就可以跳过这一部分，否则还是读一下这一部分的内容。 &nbsp;&nbsp;&nbsp; 不管什么时候，只要COM方法返回一个串，这个串都是Unicode串（这里指的是写入COM规范的所有方法）。Unicode是一种字符编码集，类似ASCII，但用两个字节表示一个字符。如果你想更好地控制或操作串的话，应该将它转换成TCHAR类型串。 &nbsp;&nbsp;&nbsp;&nbsp; TCHAR和以_t开头的函数（如_tcscpy()）被设计用来让你用相同的源代码处理Unicode和ANSI串。在大多数情况下编写的代码都是用来处理ANSI串和ANSI WindowsAPIs，所以在下文中，除非另外说明，我所说的字符/串都是指TCHAR类型。你应该熟练掌握TCHAR类型，尤其是当你阅读其他人写的有关代码时，要特别注意TCHAR类型。 &nbsp;&nbsp;&nbsp; 当你从某个COM方法返回得到一个Unicode串时，可以用下列几种方法之一将它转换成char类型串： 
      
      
        1、调用 WideCharToMultiByte() API。 2、调用CRT 函数wcstombs()。 3、使用CString 构造器或赋值操作(仅用于MFC )。 4、使用ATL 串转换宏。   
        
        
          
            WideCharToMultiByte() &nbsp;&nbsp;&nbsp; 你可以用WideCharToMultiByte()将一个Unicode串转换成一个ANSI串。此函数的原型如下：
          
        
        
        
          
            int WideCharToMultiByte (
    UINT    CodePage,
    DWORD   dwFlags,
    LPCWSTR lpWideCharStr,
    int     cchWideChar,
    LPSTR   lpMultiByteStr,
    int     cbMultiByte,
    LPCSTR  lpDefaultChar,
    LPBOOL  lpUsedDefaultChar );
          
        
        
        
          
            以下是参数解释： CodePage  Unicode字符转换成的代码页。你可以传递CP_ACP来使用当前的ANSI代码页。代码页是256个字符集。字符0&mdash;&mdash;127与ANSI编码一样。字符128&mdash;&mdash;255与ANSI字符不同，它可以包含图形字符或者读音符号。每一种语言或地区都有其自己的代码页，所以使用正确的代码页对于正确地显示重音字符很重要。 dwFlags  dwFlags 确定Windows如何处理&ldquo;复合&rdquo; Unicode字符，它是一种后面带读音符号的字符。如è就是一个复合字符。如果这些字符在CodePage参数指定的代码页中，不会出什么事。否则，Windows必须对之进行转换。 传递WC_COMPOSITECHECK使得这个API检查非映射复合字符。 传递WC_SEPCHARS使得Windows将字符分为两段，即字符加读音，如e`。 传递WC_DISCARDNS使得Windows丢弃读音符号。 传递WC_DEFAULTCHAR使得Windows用lpDefaultChar参数中说明的缺省字符替代复合字符。 缺省行为是WC_SEPCHARS。 lpWideCharStr  要转换的Unicode串。 cchWideChar  lpWideCharStr在Unicode 字符中的长度。通常传递-1，表示这个串是以0x00结尾。 lpMultiByteStr  接受转换的串的字符缓冲 cbMultiByte  lpMultiByteStr的字节大小。 lpDefaultChar  可选&mdash;&mdash;当dwFlags包含WC_COMPOSITECHECK | WC_DEFAULTCHAR并且某个Unicode字符不能被映射到同等的ANSI串时所传递的一个单字符ANSI串，包含被插入的&ldquo;缺省&rdquo;字符。可以传递NULL，让API使用系统缺省字符（一种写法是一个问号）。 lpUsedDefaultChar  可选&mdash;&mdash;指向BOOL类型的一个指针，设置它来表示是否缺省字符曾被插入ANSI串。可以传递NULL来忽略这个参数。 我自己都有点晕菜了……!，万事开头难啊……，不搞清楚这些东西就很难搞清楚COM的串处理。何况文档中列出的比实际应用的要复杂得多。下面就给出了如何使用这个API的例子：
          
        
        
        
          
            // 假设已经有了一个Unicode 串 wszSomeString...
char szANSIString [MAX_PATH];

    WideCharToMultiByte ( CP_ACP,                // ANSI 代码页
                          WC_COMPOSITECHECK, // 检查重音字符
                          wszSomeString,         // 原Unicode 串
                          -1,                    // -1 意思是串以0x00结尾
                          szANSIString,          // 目的char字符串
                          sizeof(szANSIString),  // 缓冲大小
                          NULL,                  // 肥缺省字符串
                          NULL );                // 忽略这个参数
调用这个函数后，szANSIString将包含Unicode串的ANSI版本。
          
        
        
        
          
            wcstombs() &nbsp;&nbsp;&nbsp; 这个CRT函数wcstombs()是个简化版，但它终结了WideCharToMultiByte()的调用，所以最终结果是一样的。其原型如下：
          
        
        
        
          
            size_t wcstombs (
    char*          mbstr,
    const wchar_t* wcstr,
    size_t         count );
          
        
        
        
          
            以下是参数解释： mbstr  接受结果ANSI串的字符（char）缓冲。 wcstr  要转换的Unicode串。 count  mbstr参数所指的缓冲大小。 
            
            
              wcstombs()在它对WideCharToMultiByte()的调用中使用WC_COMPOSITECHECK | WC_SEPCHARS标志。用wcstombs()转换前面例子中的Unicode串，结果一样：
            
            
            
              wcstombs ( szANSIString, wszSomeString, sizeof(szANSIString) );  
              
              
                
                  CString &nbsp;&nbsp;&nbsp;&nbsp; MFC中的CString包含有构造函数和接受Unicode串的赋值操作，所以你可以用CString来实现转换。例如： 
                  
                  
                    // 假设有一个Unicode串wszSomeString&#8230;
                  
                  
                  
                    CString str1 ( wszSomeString ); // 用构造器转换 CString str2;
                  
                  
                  
                    str2 = wszSomeString; // 用赋值操作转换
                  
                  
                  
                    ATL宏 &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ATL有一组很方便的宏用于串的转换。W2A()用于将Unicode串转换为ANSI串（记忆方法是&ldquo;wide to ANSI&rdquo;&mdash;&mdash;宽字符到ANSI）。实际上使用OLE2A()更精确，&ldquo;OLE&rdquo;表示的意思是COM串或者OLE串。下面是使用这些宏的例子：
                  
                  
                  
                    #include 
                  
                  
                  
                    // 还是假设有一个Unicode串wszSomeString&#8230;
                  
                  
                  
                    { char szANSIString [MAX_PATH]; USES_CONVERSION; // 声明这个宏要使用的局部变量
                  
                  
                  
                    lstrcpy ( szANSIString, OLE2A(wszSomeString) ); }
                  
                  
                  
                    OLE2A()宏&ldquo;返回&rdquo;转换的串的指针，但转换的串被存储在某个临时栈变量中，所以要用lstrcpy()来获得自己的拷贝。其它的几个宏是W2T()（Unicode 到 TCHAR）以及W2CT()（Unicode到常量TCHAR串）。  有个宏是OLE2CA()（Unicode到常量char串），可以被用到上面的例子中，OLE2CA()实际上是个更正宏，因为lstrcpy()的第二个参数是一个常量char*，关于这个问题本文将在以后作详细讨论。 另一方面，如果你不想做以上复杂的串处理，尽管让它还保持为Unicode串，如果编写的是控制台应用程序，输出/显示Unicode串时应该用全程变量std::wcout，如：
                  
                  
                  
                    wcout 