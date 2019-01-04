---
title: Use mutt/postfix to send mail in Linux
date: 2019-01-04 15:51:58
tags:
- vps
categories: Technology
---

折腾VPS上发邮件的原因是公司的WIFI从外网下载太慢，如果能够直接把文件下载到VPS上再用邮件塞到公司邮箱中下载，应该会快很多。我选择用Gmail来发送邮件，所以postfix的配置是Gmail的。

# Postfix
Postfix的安装配置主要参考这篇文章：[Configure Postfix to Send Mail Using Gmail and Google Apps on Debian or Ubuntu](https://www.linode.com/docs/email/postfix/configure-postfix-to-send-mail-using-gmail-and-google-apps-on-debian-or-ubuntu/#test-postfix)。在印象笔记中保存一份[备份](https://app.yinxiang.com/shard/s4/nl/266203/6a433dcd-1366-4946-8165-c27b4eaa4342)。

## 安装和配置
1. 安装postfix和sasl
    ``` bash
    apt-get install libsasl2-modules postfix
    ```
    中间会弹出提示：
    General type of mail configuration: Internet Site
    System mail name: 默认的域名就可以了
2. 完成后会生成`/etc/postfix/main.cf`配置文件，后面需要修改。
3. 使用[Generate an App password](https://security.google.com/settings/security/apppasswords)来生成一个app password。App的名称就填Postfix。
4. 用户名和密码保存在`/etc/postfix/sasl/sasl_passwd`文件中，修改这个文件，添加这一行：
    ```
    [smtp.gmail.com]:587 username@gmail.com:password
    ```
    其中username替换成自己的用户名，password替换成刚才生成的app password。
5. 生成db文件，执行：
    ``` bash
    postmap /etc/postfix/sasl/sasl_passwd
    ```
6. 设置文件权限：
    ``` bash
    sudo chown root:root /etc/postfix/sasl/sasl_passwd /etc/postfix/sasl/sasl_passwd.db
    sudo chmod 0600 /etc/postfix/sasl/sasl_passwd /etc/postfix/sasl/sasl_passwd.db
    ```
7. 编辑`/etc/postfix/main.cf`，主要设置relayhost，其他不用改。
    ```
    # See /usr/share/postfix/main.cf.dist for a commented, more complete version

    # Debian specific:  Specifying a file name will cause the first
    # line of that file to be used as the name.  The Debian default
    # is /etc/mailname.
    #myorigin = /etc/mailname

    smtpd_banner = $myhostname ESMTP $mail_name (Debian/GNU)
    biff = no

    # appending .domain is the MUA's job.
    append_dot_mydomain = no

    # Uncomment the next line to generate "delayed mail" warnings
    #delay_warning_time = 4h

    readme_directory = no

    # See http://www.postfix.org/COMPATIBILITY_README.html -- default to 2 on
    # fresh installs.
    compatibility_level = 2

    # TLS parameters
    smtpd_tls_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
    smtpd_tls_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
    smtpd_use_tls=yes
    smtpd_tls_session_cache_database = btree:${data_directory}/smtpd_scache
    smtp_tls_session_cache_database = btree:${data_directory}/smtp_scache

    # See /usr/share/doc/postfix/TLS_README.gz in the postfix-doc package for
    # information on enabling SSL in the smtp client.

    smtpd_relay_restrictions = permit_mynetworks permit_sasl_authenticated defer_unauth_destination
    myhostname = tonybuaa.tk
    alias_maps = hash:/etc/aliases
    alias_database = hash:/etc/aliases
    myorigin = /etc/mailname
    mydestination = $myhostname, tonybuaa.tk, localhost.tk, , localhost
    relayhost = [smtp.gmail.com]:587
    mynetworks = 127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128
    mailbox_size_limit = 0
    recipient_delimiter = +
    inet_interfaces = all
    inet_protocols = all
    # Enable SASL authentication
    smtp_sasl_auth_enable = yes
    # Disallow methods that allow anonymous authentication
    smtp_sasl_security_options = noanonymous
    # Location of sasl_passwd
    smtp_sasl_password_maps = hash:/etc/postfix/sasl/sasl_passwd
    # Enable STARTTLS encryption
    smtp_tls_security_level = encrypt
    # Location of CA certificates
    smtp_tls_CAfile = /etc/ssl/certs/ca-certificates.crt
    ```
    保存退出。
8. 重启postfix。
    ``` bash
    systemctl restart postfix
    ```

# Mutt
Mutt实际上就算是个邮件管理工具，它调用sendmail来发送邮件，调用vim等编辑器来编辑邮件等。postfix把sendmail替换成了自己的，mutt发送邮件的时候自然也就是通过postfix来将邮件发出去。详细说明可以参考这篇文章：[Mutt email 程序使用入门](http://www.ctex.org/documents/shredder/mutt_frame.html)

## 安装和配置
1. 安装mutt
   ``` bash
   apt-get install mutt
   ```
2. 建立配置文件
   首先需要建立一个.muttrc文件，然后修改文件权限
    ```
    touch ~/.muttrc
    sudo chmod 600 ~/.muttrc
    ```
3. 修改配置。可以配置的有很多，一般用默认值就可以了。具体可以参考手册：[The Mutt E-Mail Client](http://www.mutt.org/doc/manual/)
    ```
    set realname="Tony"
    set editor="vim"
    set from="tonybuaa@gmail.com"
    ```

# 发送邮件
现在可以发个邮件试一下了：
```
echo "test" | mutt tonybuaa@gmail.com -s "Subject" -a shadowsocks-all.log.gz
```
这个邮件是自己发给自己的，其中：
- `-s`是指定邮件主题
- `-a`是添加附件

邮件正文使用echo加管道符写进去。
如果对发送结果有疑问，可以查看系统日志：`tail -f /var/log/syslog`。