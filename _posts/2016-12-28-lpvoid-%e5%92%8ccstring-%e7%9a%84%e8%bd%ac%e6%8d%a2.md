---
layout: post
title: LPVOID 和CString 的转换
published: true
author: moli
comments: true
date: 2008-12-12 12:12:00
tags: [ ]
categories:
    - uncategorized
permalink: /archives/142.html
---
CString str("123");

LPSTR lpstr=str.GetBuffer();//这个不能用于Unicode(LPWSTR)

LPVOID lpvoid=(LPVOID)lpstr;

str.ReleaseBuffer();

思路:

先取得指向CString对象里的内容的指针 LPSTR lpstr = str.GetBuffer();

然后将这个指针转换成LPVOID类型(强制类型转换)