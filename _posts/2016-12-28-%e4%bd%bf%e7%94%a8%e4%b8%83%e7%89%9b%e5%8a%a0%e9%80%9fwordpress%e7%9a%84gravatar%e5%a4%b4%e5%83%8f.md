---
layout: post
title: 使用七牛加速wordpress的gravatar头像
published: true
author: moli
comments: true
date: 2014-03-28 03:03:55
tags:
    - Gravatar
    - 七牛云存储
    - 修改头像
categories:
    - life
permalink: /archives/3808.html
---
因为gravatar在国外的原因，可能加载速度比较慢，所以我们可以用七牛的镜像来为gravatar加速，提供网站的整理速度。

## 首先：【注册一个七牛帐号】

## 步骤1：添加七牛镜像

[][1]

&nbsp;

在后台添加一个镜像。其实七牛这玩意儿就相当于nginx的proxy功能。不过很好用呢。

## 步骤2：修改wordpress的 get_avatar 函数

找到 wp-include/pluggable.php ，在第1635行左右。

if ( is_ssl() ) {
		$host = 'https://secure.gravatar.com';
	} else {
		if ( !empty($email) )
			//$host = sprintf( "http://%d.gravatar.com", ( hexdec( $email_hash[0] ) % 2 ) ); //注视掉这一行
			$host = "http://mgravatar.qiniudn.com"; //添加这一行
		else
			$host = 'http://0.gravatar.com';
	}

这样就可以了。你也可以直接使用我的这个地址：

http://mgravatar.qiniudn.com

当是中国的gravatar缓存地址吧：）

当然，如果你要自己申请一个七牛空间，也是可以的。

狂点这里：【点击注册】

 [1]: http://img.huoxr.com/huoxr/2014/03/2014-03-282.52.53.png