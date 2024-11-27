---
layout: post
title: GitLab SMTP配置网易企业邮箱发送应用邮件
date: 2024-11-27 00:22
author: admin
comments: true
categories: [GitLab]
tags: [GitLab]
---

本文介绍如何在GitLab中使用网易企业邮箱配置SMTP服务器。

<!-- more -->

### 配置 SMTP 服务器


希望通过一个 SMTP 服务器发送应用邮件，而不是通过 Sendmail 或 Postfix，需要在 `/etc/gitlab/gitlab.rb` 中添加以下配置信息：



```
gitlab_rails['smtp_enable'] = true
gitlab_rails['smtp_address'] = "smtp.qiye.163.com"
gitlab_rails['smtp_port'] = 465
gitlab_rails['smtp_user_name'] = "gitlab-system@your-company.com"
gitlab_rails['smtp_password'] = "your-smtp-password"
gitlab_rails['smtp_authentication'] = "login"
gitlab_rails['smtp_enable_starttls_auto'] = false
gitlab_rails['smtp_tls'] = true
gitlab_rails['smtp_domain'] = "smtp.qiye.163.com"

gitlab_rails['smtp_openssl_verify_mode'] = 'none'

gitlab_rails['gitlab_email_from'] = 'gitlab-system@your-company.com'
gitlab_rails['gitlab_email_display_name'] = 'GitLab平台'
gitlab_rails['gitlab_email_reply_to'] = 'gitlab-system@your-company.com'

user['git_user_email'] = "gitlab-system@your-company.com"
```

注意，密码用的是网易授权码；"gitlab-system@your-company.com"是你要配置发送邮件的公共邮箱。


执行`sudo gitlab-ctl reconfigure`命令使配置生效。




### 测试 SMTP 配置


可以使用 Rails 控制台验证是否可以正确发送电子邮件。 执行`sudo gitlab-rails console`命令进入控制台。然后，可以在控制台提示符下输入以下命令，以使系统发送测试电子邮件

```
Notify.test_email('your-email@your-company.com', 'Message Subject', 'Message Body').deliver_now
```


如果你的'your-email@your-company.com'邮箱能够收到测试邮件，则证明 SMTP 服务器配置完成。



## 参考资料


* 本文同步至：<https://configuring-an-smtp-server-with-netease-enterprise-email-in-gitLab/>
* https://docs.gitlab.com/omnibus/settings/smtp.html#netease-free-enterprise-email