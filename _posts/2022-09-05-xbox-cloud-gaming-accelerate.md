---
title: XBox Cloud Gaming 游戏加速尝试
author: moli
published: true
comments: true
date: 2022-09-05 17:30:00
tags:
  - xbox cloud gaming
categories:
  - xbox
---

# Xbox Cloud Gaming 游戏加速

之前有个很老的xbox游戏机，因为性能有点差劲了，所以卖了。
偶尔还是想玩玩游戏，但是老婆不让给买xbox的物理机（怀恋单身），所以含泪玩xbox cloud gaming（以下简称 xcg），xpg会员游戏还是很丰富的。

于是买了uu加速器，坦白说uu加速器不算便宜的，但是xcg在晚上高峰期，一样卡得怀疑人生，那种被马赛克糊满脸的感受，上一次这种体验还是看小姐姐的电影。

其实用uu加速器玩是ok的，就是国内的网络情况大家都支持，dddd（懂得都懂），晚上高峰期出口网络抖得怀疑人生。

于是我想自己建一个加速网络，会不会更好呢，趁着疫情被关在家里，做了一轮测试和体验，给需要的人做一些参考。

线路方案：

1. 直接买azure的韩国服务器，直连
2. 买联通、移动vps中转到韩国服务器，跳转

这里最大的问题其实高峰期出口会不会抖。

上面2个方案其实会有一些根据实际情况做一些调整，例如你本来就是联通，那可能不用中转了，直连效果就好了。如果你是电信，晚上出口会抖成马赛克，可以考虑联通服务器中转。

## 了解Xbox会请求哪些服务器

xbox是限制区域的，还没对国内开放（未来估计也不太可能），亚洲基本只能使用 韩国、日本服务器，这俩服务器在国内服务器基本能做到50ms的延迟左右，玩非fps游戏是够用了。

于是浏览器开启开发者模式，截网络包，这里不复述了，直接给clash的规则：

```yaml
rules:
  - DOMAIN-SUFFIX,xbox.com,your-proxy
  - DOMAIN-SUFFIX,live.com,your-proxy
  - DOMAIN-SUFFIX,xboxlive.com,your-proxy
  - DOMAIN-SUFFIX,microsoft.com,your-proxy
  - DOMAIN-SUFFIX,visualstudio.com,your-proxy
  - DOMAIN-SUFFIX,msn.com,your-proxy
  - DOMAIN-SUFFIX,gamepass.com,your-proxy
  - DOMAIN-SUFFIX,demdex.net,your-proxy
  - DOMAIN-SUFFIX,microsoftonline.com,your-proxy
```

如果你自己有🪜，上面的规则配到你的 clash 中，也能实现加速了，不过速度嘛就看你的机场良心了～

## 测试

大家都知道azure、gcp之类的云服务商，对国内还是蛮友好对，主要是看高峰期。

azure 怎么买服务器就不复述了，网上大把。

服务器和客户端的加密理论上越简单越好，这样实时性会更好一点。

搭建好了之后，直接上xbox cloud gaming 网站，没有提示「不可用区域」就ok啦。

开了最近玩的「杀手47」，流畅得一批，爱了爱了，心理甚至嘲讽了一波uu加速器。

不过「开心不过3秒」，打脸总是非常及时。

晚上10点左右，高峰期，一样卡得满屏幕的马赛克。尝试用uu加速器，也卡哭了。

有钱还是上电信的精品网，没钱就老老实实买物理机吧（并且你媳妇同意）。

当然后来还常识移动中转，发现我那台移动宽带到韩国的速度也不咋滴，
国内联通中转就没测了，晚点试试阿里云搞台上海到韩国的中转，之后再把结论同步到blog。

## 总结

还是买个xbox的物理机吧，折腾。

放过自己，不要跟自己过不去，就像每次做核酸的时候：心理mmp，嘴张得比谁都大。
