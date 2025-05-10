---
title: 1.socket的监听与连接
top: 1
series: c++网络编程
date: 2025-05-10 16:17:56
categories:
- 技术
- c++
- 网络编程
tags:
- 网络编程
- c++
comments: true
---
<!-- toc -->

# 1.socket的监听与连接

## TCP网络编程基本流程
### 服务端
1）socket——创建socket对象。
2）bind——绑定本机ip+port。
3）listen——监听，若在监听到，则建立连接。
4）accept——再创建一个socket对象给其收发消息。原因是现实中服务端都是面对多个客户端，那么为了区分各个客户端，则每个客户端都需再分配一个socket对象进行收发消息。
5）read、write——就是收发消息了。

### 客户端
1）socket——创建socket对象。
2）connect——根据服务端ip+port，发起连接请求。
3）write、read——建立连接后，就可发收消息了。

## 