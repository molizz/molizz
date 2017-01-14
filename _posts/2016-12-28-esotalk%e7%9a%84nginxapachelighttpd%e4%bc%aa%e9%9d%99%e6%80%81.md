---
layout: post
title: esoTalk的nginx/apache/lighttpd伪静态
published: true
author: moli
comments: true
date: 2014-07-13 02:07:52
tags:
    - apache
    - esoTalk
    - lighttpd
    - nginx
    - 伪静态
categories:
    - life
permalink: /archives/3934.html
---

  esoTalk 的config/config.php 中的配置值，及对应产生的url链接：



  
    
      esoTalk.urls.friendly
    
    
    
      esoTalk.urls.rewrite
    
    
    
      URL format
    
  
  
  
    
      false
    
    
    
      false
    
    
    
      forum.com/?p=member/123
    
  
  
  
    
      true
    
    
    
      false
    
    
    
      forum.com/index.php/member/123
    
  
  
  
    
      true
    
    
    
      true
    
    
    
      forum.com/member/123
    
  



  Apache


&lt;code>&lt;span class="tag" style="color: #000088;">&lt;IfModule&lt;/span>&lt;span class="atn" style="color: #660066;">mod_rewrite&lt;/span>&lt;span class="pln" style="color: #000000;">.&lt;/span>&lt;span class="atn" style="color: #660066;">c&lt;/span>&lt;span class="tag" style="color: #000088;">&gt;&lt;/span>&lt;span class="pln" style="color: #000000;">&lt;br />
RewriteEngine On&lt;br />
RewriteCond %{REQUEST_FILENAME} !-f&lt;br />
RewriteRule ^(.*)$ index.php/$1 [QSA,L]&lt;br />
&lt;/span>&lt;span class="tag" style="color: #000088;">&lt;/IfModule&gt;&lt;/span>


  Nginx


location / {
    try_files $uri $uri/ /index.php/$args;
}



  Lighttpd


url.rewrite-if-not-file = (
    "^(.*)$" =&gt; "index.php/$1"
)


完美支持esoTalk的主机：http://www.rkidc.net