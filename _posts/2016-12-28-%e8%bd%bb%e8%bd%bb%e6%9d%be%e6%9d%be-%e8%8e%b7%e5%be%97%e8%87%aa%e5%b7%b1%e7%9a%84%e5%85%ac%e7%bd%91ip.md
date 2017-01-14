---
layout: post
title: 轻轻松松..获得自己的公网IP..
published: true
author: moli
comments: true
date: 2008-12-12 12:12:00
tags: [ ]
categories:
    - uncategorized
permalink: /archives/220.html
---
CString CWinDlg::getMyIp()
  
{
  
CString str="";

CString myWeb; //源码
  
CString everWeb; //整理后的源码
  
CInternetSession localsession;&nbsp;&nbsp; //定义网络连接
  
CInternetFile *webfile=NULL;&nbsp;&nbsp;&nbsp;&nbsp; //定义一个文件对象指针
  
try
  
{
  
&nbsp;&nbsp; webfile=(CInternetFile*) localsession.OpenURL(str);
  
}
  
catch (CInternetException *pException)
  
{
  
&nbsp;&nbsp; webfile=NULL;
  
&nbsp;&nbsp; pException->Delete();
  
}
  
if (webfile)
  
{
  
&nbsp;&nbsp; while (webfile->ReadString(myWeb))
  
&nbsp;&nbsp; {
  
&nbsp;&nbsp;&nbsp; everWeb+=myWeb;
  
&nbsp;&nbsp; }
  
}

//下面是将获得的源码进行分解.. 获得IP&#8230;
  
int ipTemp=everWeb.Find("IPMessage")+11;

CString ip="";
  
//获得IP&#8230;
  
do
  
{
  
&nbsp;&nbsp; ip+=everWeb.GetAt(ipTemp++); 
  
} while (everWeb.GetAt(ipTemp)!='