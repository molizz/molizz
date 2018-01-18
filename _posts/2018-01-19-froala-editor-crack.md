---
title: Froala Editor 所见即所得破解（2.7.3）
author: moli
published: true
comments: true
date: 2018-01-19 01:52:01
tags: [ froala editor ]
categories:
 - 生活
---

```
如条件允许，或用于盈利目的，请支持购买正版软件！
```

Froala editor 是一款非常不错的所见即所得web编辑器，并且是收费的，单个域名的授权是99美元~

个人感觉挺好用的编辑器，但是在没有购买正常的情况下，只能编辑10行，且有一个大大的未授权标识。

在 froala_editor.pkgd.min.js 文件中找到如下代码

```
function i(){if(p>10&&b.destroy(),!b.$box)return!1;b.$wp.prepend(o(c(o("NCKB1zwtPA9tqzajXC2c2A7B-16VD3spzJ1C9C3D5oOF2OB1NB1LD7VA5QF4TE3gytXB2A4C-8VA2AC4E1D3GB2EB2KC3KD1MF1juuSB1A8C6yfbmd1B2a1A5qdsdB2tivbC3CB1KC1CH1eLA2sTF1B4I4H-7B-21UB6b1F5bzzzyAB4JC3MG2hjdKC1JE6C1E1cj1pD-16pUE5B4prra2B5ZB3D3C3pxj1EA6A3rnJA2C-7I-7JD9D1E1wYH1F3sTB5TA2G4H4ZA22qZA5BB3mjcvcCC3JB1xillavC-21VE6PC5SI4YC5C8mb1A3WC3BD2B5aoDA2qqAE3A5D-17fOD1D5RD4WC10tE6OAZC3nF-7b1C4A4D3qCF2fgmapcromlHA2QA6a1E1D3e1A6C2bie2F4iddnIA7B2mvnwcIB5OA1DB2OLQA3PB10WC7WC5d1E3uI-7b1D5D6b1E4D2arlAA4EA1F-11srxI-7MB1D7PF1E5B4adB-21YD5vrZH3D3xAC4E1A2GF2CF2J-7yNC2JE1MI2hH-7QB1C6B5B-9bA-7XB13a1B5VievwpKB4LA3NF-10H-9I-8hhaC-16nqPG4wsleTD5zqYF3h1G2B7B4yvGE2Pi1H-7C-21OE6B1uLD1kI4WC1E7C5g1D-8fue1C8C6c1D4D3Hpi1CC4kvGC2E1legallyXB4axVA11rsA4A-9nkdtlmzBA2GD3A13A6CB1dabE1lezrUE6RD5TB4A-7f1C8c1B5d1D4D3tyfCD5C2D2==")))),k=b.$wp.find("> div:first"),l=k.find("> a"),"rtl"==b.opts.direction&&k.css("left","auto").css("right",0).setAttribute("direction","rtl"),p++}
```

改成

```
function i(){return 1}
```

这样就可以绕过验证授权了，并且没有限制了。

