---
layout: post
title: 如何通过js（jquery）输出rss feed ？
published: true
author: moli
comments: true
date: 2013-01-17 03:01:04
tags:
    - jquery
    - rss feed
categories:
    - jquery
permalink: /archives/3383.html
---
有时候你的网站需要输出某个网站的新闻，比如 [有时候你的网站需要输出某个网站的新闻，比如][1] 右下角的新闻，我使用jqery ajax读取我的这个博客的“魔力网站加速”栏目下的feed文章，作为[有时候你的网站需要输出某个网站的新闻，比如 [有时候你的网站需要输出某个网站的新闻，比如][1] 右下角的新闻，我使用jqery ajax读取我的这个博客的“魔力网站加速”栏目下的feed文章，作为][1] 的新闻。

那么我要如何做呢？

> 以下内容来自：http://stackoverflow.com/questions/10943544/how-to-parse-a-rss-feed-using-javascript 我仅作翻译，扩充

## 方法1：使用JQuery 的JFEED插件

插件下载地址： （自带案例）

代码例子：

    jQuery.getFeed({
       url     : FEED_URL,
       success : function (feed) {
          console.log(feed.title);
          // do more stuff here
       }
    });
    

## 方法2：使用JQuery的XML解析

    $.get(FEED_URL, function (data) {
        $(data).find("entry").each(function () { // or "item" or whatever suits your feed
            var el = $(this);
    
            console.log("------------------------");
            console.log("title      : " + el.find("title").text());
            console.log("author     : " + el.find("author").text());
            console.log("description: " + el.find("description").text());
        });
    });

## 方法3：使用[Google AJAX FEED API][2]

    $.ajax({
      url      : document.location.protocol + '//ajax.googleapis.com/ajax/services/feed/load?v=1.0&num=10&callback=?&q=' + encodeURIComponent(FEED_URL),
      dataType : 'json',
      success  : function (data) {
        if (data.responseData.feed && data.responseData.feed.entries) {
          $.each(data.responseData.feed.entries, function (i, e) {
            console.log("------------------------");
            console.log("title      : " + e.title);
            console.log("author     : " + e.author);
            console.log("description: " + e.description);
          });
        }
      }
    });

这样就完成了ajax解析rss feed的操作，友好，又不影响网站打开速度。

## 后来我发现一个更好的方法

因为wordpress的rss是每次访问，都要动态生成一次，增加了服务器的压力，于是我想了一个更好的办法，就是依托于FeedSky（[飞递][3]）,然后使用其 Feed发布 -》 Feed JS 速出 功能，调用它的js，然后使用其中的变量就可以实现循环输出数据了，而且不消耗主机的CPU等。

&lt;!-- Feedsky FEED JS发布代码开始 --&gt;&lt;script type="text/javascript" src="http://feed.feedsky.com/speedmoliml/jsout&amp;n=3"&gt;&lt;/script&gt;&lt;!-- Feedsky FEED JS发布代码结束 --&gt;

 [1]: http://speed.loveml.com "魔力网站加速"
 [2]: https://developers.google.com/feed/
 [3]: http://www.feedsky.com