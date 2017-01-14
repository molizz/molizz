---
layout: post
title: 宽字符串保存到文件，宽字符串转换为整型
published: true
author: moli
comments: true
date: 2008-09-14 03:09:00
tags: [ ]
categories:
    - life
permalink: /archives/174.html
---
如标题所示，俩遇到的问题。 目前已经解决。 所以整理一下。

宽字符串不能直接用CFileDialog保存到文件。。。

即使保存到文件里，打开后也看不到。

因为系统要识别文本文件是不是unicode码是通过2个字节头实现的。

char fx[2]; fx[0]=0xFF; fx[1]=0xFE; sFile.Write(fx,2);

写入成功后再UE中打开，用16进制模式查看，将可以发现文本前面有FF FE


  
    
  
  
  
    这样文本打开的时候就默认为UNICODE模式了。
  
  
  
    CStdioFile sFile(strPath,CFile::modeCreate|CFile::modeReadWrite|CFile::typeBinary);
  
  
  
    这段代码到最后一段CFile::typeBinary 说明是用二进制打开。
  
  
  
    sFile.WriteString(text2);
  
  
  
    这样就直接写入数据。。
  
  
  
    如果想在文本文件中实现换行。
  
  
  
    text2+=text + L"\r\n"; 
  
  
  
    在后面加入&ldquo;\r\n&rdquo;就可以了。。&ldquo;\n&rdquo;是不可以的哦~
  
  
  
    &#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;&#8212;-
  
  
  
    ok,然后就是将宽字符串转换为整型
  
  
  
    比如 CString a=L"123";
  
  
  
    用 _wtoi(a); 就可以了～ 
  
  
  
    上面都是俺的记录～ 
  
