编辑config/configuration.yml

```yaml
  email_delivery:
    delivery_method: :smtp
    smtp_settings:
        tls: true
        enable_starttls_auto: true
        address: "smtp.mxhichina.com"
        port: 465
        authentication: :login
        domain: 'smtp.mxhichina.com'
        user_name: 'business@babyun.cn'
        password: 'Babyun2020'
```

然后，在管理->配置->邮件通知里“邮件发送人地址” 必须和configuration.yml中的user_name保持一致为： [business@babyun.cn](http://mailto:business@babyun.cn) 然后重启redmine 服务器即可.

