---
layout: post
title: C++异常处理
published: true
author: moli
comments: true
date: 2009-11-16 04:11:16
tags:
    - c++异常处理
categories:
    - uncategorized
permalink: /archives/368.html
---
异常处理，这是C++强大的地方之一（当然C语言也可以实现类似的功能（Google一下））。

异常处理在实际编程中是非常重要的，比如用 new 关键进行分配内存的时候，如果内存已被消耗，无法分配，那将抛出（thorw）一个 bad_alloc 异常，如果你不用try截获这个异常的话，将导致整个程序崩溃，这是非常不友好的（自己看着也不舒服）。

自定义一个异常处理

> #include &lt; iostream &gt;
#include &lt; stdexcept &gt;
#include &lt; string &gt;
using namespace std;

class myError
{
private:
	string sz_Error;
public:
	myError(string sz)
	{
		this-&gt;sz_Error = sz;
	}
	string what()
	{
		return sz_Error;
	}
};

int main()
{
	int i;
	cin&gt;&gt;i;
	try
	{
		if (i == 1)
		{
			throw myError("error");
		}
	}
	catch (myError e)
	{
		cout &lt; &lt; "Error:" &lt; &lt; e.what() &lt; &lt; endl;
	}
	return 1;
}

> 头文件：
> 
> exception 定义了常见的异常类 类名：exception 这个类只通知异常的产生，不提供更多的信息
> 
> stdexcept 头文件定义了几种常见的异常类 如下：
> 
>   * exception 最常见的问题
>   * runtime_error 运行时错误：仅在运行时才能检测到的问题
>   * range_error 运行时错误：生产的问题超出了有意义的值域范围
>   * overflow_error 运行时错误：计算上溢
>   * underflow_error 运行时错误：计算下溢
>   * logic_error 逻辑错误: 可在运行前检测到的问题
>   * domain_error 逻辑错误: 参数的结果值不存在
>   * invalid_argument 逻辑错误: 不合适的参数
>   * length_error 逻辑错误： 试图生成一个超出该类型最大长度的对象
>   * out\_of\_range 逻辑错误：使用一个超出有效范围的值
> 
> new 头文件定义了bad_alloc异常类型异常，无法分配内存而由new 抛出的异常
> 
> type\_info 头文件定义了 bad\_cast 异常类型，后续讨论。。
> 
> C++ 标准异常处理类 
> 
>  