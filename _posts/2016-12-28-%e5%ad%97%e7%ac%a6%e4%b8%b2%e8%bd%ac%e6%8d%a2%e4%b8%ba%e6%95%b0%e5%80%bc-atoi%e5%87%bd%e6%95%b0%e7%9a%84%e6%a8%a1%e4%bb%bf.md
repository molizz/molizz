---
layout: post
title: 字符串转换为数值..atoi函数的模仿
published: true
author: moli
comments: true
date: 2008-12-12 12:12:00
tags: [ ]
categories:
    - uncategorized
permalink: /archives/121.html
---
#include 
  
#include 
  
using namespace std;

int toInt(char *p)
  
{ 
   
while (isspace(*p))
  
&nbsp;&nbsp; p++;

int b = *p++;
   
int sign = b;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; //sign用于保存第一位值
   
//cout