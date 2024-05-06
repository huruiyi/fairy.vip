---
title: 本地开发，此站点(localhost)的连接不安全解决
tags:
 - Web开发
 - 环境配置
categories:
- [development]
---


# 本地开发，此站点(localhost)的连接不安全解决



打开Edge浏览器，输入：[edge://net-internals/#hsts](edge://net-internals/#hsts)

点击左侧 Domain Security Policy

 

#### Add HSTS domain

Domain 输入 localhost ，点击 Add，localhost:port  无法访问。

### Delete domain security policies

Domain 输入 localhost ，点击 Delete，localhost:port 正常访问。

#### Query HSTS/PKP domain

Domain 输入 localhost ，显示当前的策略信息。