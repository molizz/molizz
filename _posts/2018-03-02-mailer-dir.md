---
title: rails的邮件目录规划统一
author: moli
published: true
comments: true
date: 2018-03-02 23:21:01
tags: [rails, mailer]
categories:
  - rails
---

Rails 的 Mailer 目录停蛋疼的，没统一。

比如订单的邮箱 OrderMailer，目录默认在 views/order_mailer 下。

如果你又有别的邮件，比如提现 WithdrawMailer 默认目录又在 views/withdraw_mailer。

如果你有很多种的邮件类型，每次建立一个 mailer，views 下就多一个 mailer 目录，可能有点强迫症，mailer 很影响 views 的目录结构美观呢！

**所以我决定改一下，把所有的 mailer 都放在 views/mailers 目录下**

我想，大一点的项目，应该有规划 mailer 的目录吧，结果看了下 gitlab 这个项目，也木有。。

所以啥也别说了，先看源码，看看怎么改。

打开 rails 的附带 gem actionmailer 中的 action_mailer/base.rb 文件

找到 def mail 方法

```ruby
def mail(headers = {}, &block)
    ……
    # Render the templates and blocks
    responses = collect_responses(headers, &block)
    ……
end
```

顺着 collect_responses 方法，再找到

```ruby
collect_responses_from_templates(headers)


templates_path = headers[:template_path] || self.class.mailer_name
templates_name = headers[:template_name] || action_name
```

上面的代码发现原来 headers 中可以传参数来指定模板目录……然后 Dash 看了下 mail 方法，上面还真写着。。（心里暗骂自己一句傻逼）

行了，继承 ApplicationMailer，重写 mail 方法

```ruby
class BaseMailer < ApplicationMailer
  def mail(headers, &block)
    subdir = self.class.name.remove('Mailer').downcase
    headers.merge!(template_path: "mailers/#{subdir}")
    super
  end
end
```

```ruby
class OrderMailer < BaseMailer
  def paid(order)
    @order = order
    mail(to: @order.buyer_email, subject: "新的订单邮件")
  end
end
```

现在 paid 将在 views/mailers/order/paid.html.haml  中找到该文件。

完。。
