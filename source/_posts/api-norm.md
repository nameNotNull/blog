---
title: API命名规范
date: 2018-08-02 13:02:12
tags: api规范
categories: php
---
###  基本格式
protocol://hostname[:port]/path/action.type[?query]

协议://主机名[:端口]/ 路径/操作.返回类型:[参数]

通用命名
不要关键字作为名称

为了避免增删改查等高频操作使用不同词汇从而增加了沟通和维护的成本，对类似含义的词汇进行合并。

参考了使用频率较高的最为优先选择。

- create

- remove

- modify

- search
以列表方式展现

- detail
以详情方式展现

- summary
以概要方式展现

- suggestion 
搜索提示