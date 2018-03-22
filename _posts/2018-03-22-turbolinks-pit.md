---
title: Turbolinks无法执行js的坑
author: moli
published: true
comments: true
date: 2018-03-22 19:40:00
tags: [turbolinks]
categories:
 - rails
---

之前去面了一家rails的公司……然后当时被问了Turbolinks的问题，当时面试官看了一下我的v2geek。

然后特么，我的所见即所得编辑器掉链子了……

所以我想好好玩玩前端了……

当然，还是先把眼前的bug先搞定。

然后用了一个很傻比的方法：

```JavaScript
  var intId = setInterval(function() {
    if ($('.wysiwyg').froalaEditor == undefined) {
      return
    }
    clearInterval(intId);

    $('.wysiwyg').froalaEditor( {
      language: 'zh',
    })
  }, 1000)
```
原因很简单，其实你在js路径上加上 'data-turbolinks-track': 'reload' 标识后。

Turbolinks是会加载这个js的，但是页面上的js已经先执行了，所以导致编辑器没有初始化，没有展示出来。

所以简单的解决方案是 每隔一秒检查froalaEditor的js文件是否已经加载完毕，然后再在后面启动编辑器，如果有更好的解决方案，请在下面留言。。。

然后最近想把vue好好学一学。

不然做不出良好的用户体验了~~~

今天唠叨完毕。。

