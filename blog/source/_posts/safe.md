---
title: safe
date: 2022-02-23 16:27:49
tags:
categories:
---

## XSS（Cross Site Scripting）：跨域脚本攻击

XSS的攻击原理

XSS攻击的核心原理是：不需要你做任何的登录认证，它会通过合法的操作（比如在url中输入、在评论框中输入），向你的页面注入脚本（可能是js、hmtl代码块等）

XSS的防范措施
1、转义字符
2、引入js-xss
3、CSP (Content Security Policy,即内容安全策略)

## CSRF（Cross-site request forgery）：跨站请求伪造

CSRF的攻击原理

CSRF攻击的核心原理是：通过合法的操作（比如在url中输入、在评论框中输入），向你的页面发送请求，从而达到攻击的目的

CSRF的防范措施

1、Token 验证

服务端随机生成token，保存在服务端session中，同时保存到客户端中，客户端发送请求时，把token带到HTTP请求头或参数中，服务端接收到请求，验证请求中的token与session中的是否一致。如果请求中没有 token 或者 token 内容不正确，则认为可能是 CSRF 攻击而拒绝该请求

2、Referer 验证

根据 HTTP 协议，在 HTTP 头中有一个字段叫Referer，它记录了该 HTTP 请求的来源地址。通过验证Referer，可以检查请求是否来自合法的"源"。
