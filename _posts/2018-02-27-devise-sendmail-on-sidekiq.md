---
title: 通过Sidekiq来发送devise的邮件
author: moli
published: true
comments: true
date: 2018-02-27 00:35:01
tags: [v2geek, sidekiq, devise]
categories:
 - v2geek
---

### V2Geek

把V2Geek的基本销售功能上线后，就开始找工作了~~~ 休息了一个月了~~~ 再休人会废掉。

V2Geek从1月11日初始化成git项目，然后到现在每天一直写一直写…… 去掉中间半个月过年，基本上还算高效了~~

点击打开 [v2geek](https://v2geek.com)

完全上线后，这几天开始找工作吧~  看看有没有有缘人介绍个【深圳】的ruby on rails或其他语言的后端工作~~~（语言不是问题）

联系 molisoft#qq.com

### 回到正题

今天发现devise的邮件不是异步，然后网上找了下方法，似乎都是用什么devise-async，然而我本地测试似乎不行。

然后看了下devise的源码，发现改一下还是比较简单的。

配置devise

```ruby
config.mailer = 'AsyncDeviseMailer'
```

增加 AsyncDeviseMailer 类 

```ruby
class AsyncDeviseMailer
  %w(confirmation_instructions reset_password_instructions unlock_instructions email_changed password_change).each do |method|
    class_eval <<-RUBY, __FILE__, __LINE__+1
      def #{method}(record, token, opts={})
        DeviseMailJob.perform_later("#{method}", record, token, opts={})
      end
    RUBY
  end
end
```

上面用ruby的class_eval定义的方法，你也可以一个一个方法的写，也是可以的。

下面是队列的方法

```
class DeviseMailJob < ApplicationJob
  queue_as :default

  def perform(method, record, token, opts={})
    DeviseMailer.new.send(method, record, token, opts)
  end
end
```

下面是我们继承 Devise::Mailer 后的类

```
class DeviseMailer < Devise::Mailer
  default from: "xxx"
  default reply_to: 'xxx'

  layout 'devise_mailer'

  def confirmation_instructions(record, token, opts={})
    @token = token
    devise_mail(record, :confirmation_instructions, opts)
  end

  def reset_password_instructions(record, token, opts={})
    @token = token
    devise_mail(record, :reset_password_instructions, opts)
  end

  def unlock_instructions(record, token, opts={})
    @token = token
    devise_mail(record, :unlock_instructions, opts)
  end

  def email_changed(record, token, opts={})  # 注意这里额外加了token参数，为了上面的队列方法中参数对其
    devise_mail(record, :email_changed, opts)
  end

  def password_change(record, token, opts={})
    devise_mail(record, :password_change, opts)
  end
end

```