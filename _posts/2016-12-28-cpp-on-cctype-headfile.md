---
layout: post
title: 【C++】cctype头文件中字符处理函数
published: true
author: moli
comments: true
date: 2009-11-11 01:11:12
tags:
    - cctype
    - head
    - 函数
categories:
    - uncategorized
permalink: /archives/365.html
---
摘自 c++ primer

#include &lt;cctype&gt;
using namespace std;

int main()
{
char c='b';

//以下函数皆为布尔型 需要导入头文件 &lt;cctype&gt;
isalnum(c);//如果是字母或数字 则 返回true 【下面的雷同】
isalpha(c);//如果c是字母
iscntrl(c);//如果c是控制字符
isdigit(c);//如果c是数字
isgraph(c);//如果c不是空格，但可打印
islower(c);//如果c是小写字母
isprint(c);//如果c是可打印的字符
ispunct(c);//如果c是标点符号
isspace(c);//如果c是空白字符
isupper(c);//如果c是大写字母
isxdigit(c);//如果c是十六进制数

tolower(c);//将c转换为小写字母 并 返回转换后的值
toupper(c);//将c转换为大写字母 并 返回转换后的值
return 0;
}
