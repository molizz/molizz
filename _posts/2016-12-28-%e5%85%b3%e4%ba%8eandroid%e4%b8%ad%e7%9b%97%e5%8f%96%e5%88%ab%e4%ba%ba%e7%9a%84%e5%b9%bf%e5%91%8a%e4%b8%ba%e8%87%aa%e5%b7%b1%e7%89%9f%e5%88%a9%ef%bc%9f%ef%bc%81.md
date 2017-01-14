---
layout: post
title: 关于android中盗取别人的广告为自己牟利？！
published: true
author: moli
comments: true
date: 2011-12-24 08:12:48
tags:
    - admod漏洞
    - Android
    - 广告漏洞
    - 破解
categories:
    - life
permalink: /archives/2842.html
---
大家都知道android的应用程序是java 编写的，google的虚拟机的字节码跟原来的java虚拟机不一样，但是他们的底层都是对编译后的字节码解码运行应用程序。

所以android的应用程序跟普通的java程序一样（包括J2ME，jar应用程序），都是可以被反编译。

java反编译工具：ApkTool

国外有一款反编译工具（ApkTool），可以完整的将加密或扰乱后的android应用程序反编译，得到纯文字的xml配置数据，和混淆过的代码（看这些代码很痛苦）。然后还可以对反编译后的数据仅打包再发行！！

这款工具很多人用来进行汉化软件。

今天我也反编译了一款软件（糗事百科的基友：秘密），其实我只是想了解它的一些配置数据中的“秘密”。

这款软件还不错，里面有adMod的广告。这款软件很简单，界面做得也挺不错的！学习。

发现一个问题……

我们打开她的主要配置文件AndroidManifest.xml

[][1]

AndroidManifest.xml  这个文件是所有android软件的主要配置文件！里面包含了权限声明，所有的activity界面声明，还有该软件版本等信息。

如果里面有这样一句话：

[][2]

&lt;activity android:name="com.google.ads.AdActivity" android:configChanges="keyboard|keyboardHidden|orientation" /&gt;

那么，就说明这款软件是AdMod的广告（废话……）

然后我们找找看他都把广告放到哪一个activity里面了？每一个activity都几乎都有一个layout xml文件。

找找看吧。

他一般在 res/layout 目录下

[][3]

《秘密》的主窗口就有一个广告的对吧？

我们打开main.xml 文件，这个文件中有个配置数据，弄出来给大家看看吧。

[][4]

看到了吧，我们并不需要一个一个找layout文件里面是否有广告，直接搜索“**com.google.ads.AdView** ”，有这个字段就说明，里面有admod的广告！

把“**ads:adUnitId=&#8221;xxxxxxxx&#8221;**” 里面的xxxx 改成我们的 ID 号码，这个是《秘密》的作者在admod申请的的广告标识。

大家也可以去申请的，申请到后改上就OK了。。然后保存打包成apk程序。

发布到网上去后，广告的展示和点击全部都是你的了。

如何解决这样的问题？

其实理论上没有解决方法，只有稍微好点的办法，就是用代码实现这些广告。

还有就是adUnitID 这样的ID号码我们需要加密，在使用的时候解密，没有明文的话，大部分人就看不到这些信息了。

祝大家好运。。。其实这样的问题很多，不仅仅是admod，还有微云等……

（上面的东西纯属莫粒乱写，可行性有待考证。由此造成的问题，我也不负责任）

 [1]: http://huoxr.com/wp-content/uploads/2011/12/20111224200221.png
 [2]: http://huoxr.com/wp-content/uploads/2011/12/20111224200742.png
 [3]: http://huoxr.com/wp-content/uploads/2011/12/20111224201259.png
 [4]: http://huoxr.com/wp-content/uploads/2011/12/20111224201625.png