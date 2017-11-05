---
layout: post
title: Http Summary
category: network
tags: http
---

## Http方法的幂等性(Idempotence)
- 方法执行多次对服务器状态改变和方法执行一次相同
- 幂等的Http方法: GET, HEAD, PUT, DELETE。注意POST不是幂等的而PUT是幂等的，这是因为对于
```
POST /api/index HTTP/1.1
```
其中/api/index对应的操作本身，也就是哪一个操作，该操作很可能不是幂等的，而
```
PUT /resource/index HTTP/1.1
```
中/resource/index对应的是PUT方法操作的对象，该对象如果存在，就更新它，如果不存在就创建。这里的更新和创建执行多次和一次的效果是一样的，所以PUT是幂等的。
- 在HTTP/1.1中，只有幂等的方法才会支持管线（大多的浏览器默认情况下不开启管线pipelining）


---
## 幂等性在分布式系统中的应用
### 从一个账户中取钱的RPC操作(返回值表示成功或者失败)：
```
boolean withdraw(account_id, count);
```
- 如果操作在服务端成功执行，但是由于网络原因客户端无法得到结果，客户端可能会重复提交这个操作。withdraw这个操作不是幂等的。

### 如果把withdraw分解成下面两个操作：
```
int create_ticket();
boolean withdraw(ticket_id, account_id, count);
```
那么withdraw操作在服务端可以实现为幂等的，保证重复提交多次withdraw操作，和提交一次的结果一致。网络故障不会引起前面提到的问题。


---
## [Http 缓存](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Caching_FAQ)

### 只对GET方法有效
### Cache-control首部的选项(下面的选项设置在响应里)
#### public
- 可以在CDN或者浏览器缓存
#### private
- 只允许在浏览器缓存
#### no-store
- 不允许缓存
#### no-cache
- 并不是表示不允许缓存的意思，而是说浏览器每次请求时，必须向服务器验证缓存是否有效
#### max-age
- 请求到缓存过期的时间间隔
#### etag
- 缓存强校验。如果响应头里含有etag，那么可以在接下来**所有**对同一资源的请求中使用if-none-match来验证缓存。if-none-match判断服务器端是否有匹配客户端保存的etag的资源，如果有，那么缓存有效，否则无效。
#### last-modified
- 缓存弱校验。如果响应头里含有last-modified，那么只可以在接下来的**一次**对同一资源的请求里使用if-modified-since来验证缓存。
### 服务器对缓存校验的响应
- 服务器返回200和最新的资源，说明客户端缓存失效；服务器返回304（not modified），说明客户端缓存有效。
### Vary响应首部
#### request1 <--> response1
- 缓存的结构是下面的键值对
```
request1.url ==> response1.content
```
#### request2 <--> response2 (Vary: Content-Encoding; Content-Encoding: gzip;)
- 缓存的结构是下面的键值对
```
(requset2.url, gzip) ==> response2.content
```

---
## [HTML5 WebSocket](https://stackoverflow.com/questions/11077857/what-are-long-polling-websockets-server-sent-events-sse-and-comet)
### http实现服务端推送的两种方法：
#### *long poll*
- 客户端发送一个http请求，服务器端只有在有结果时才返回响应，否则一直让客户端保持TCP连接并等待响应。就像调用函数，然后阻塞，直到返回结果。当客户端收到响应，立即发送新的http请求。
- 服务器只有在客户端有请求时才发送响应。
- http对浏览器对同一域名的TCP连接数量有限制
- 阻塞其他操作
- 服务器的并发量增加
- [The Myth of Long Polling](https://blog.baasil.io/why-you-shouldnt-use-long-polling-fallbacks-for-websockets-c1fff32a064a)
#### *ajax loop*
- 客户端不断发送http请求，服务器有结果就返回结果，没有结果未生成就返回相应的提示；客户端如果发现响应中包含自己期待的结果，就停止发送请求，否则重复之前的操作。
- 服务器要不断建立TCP连接，浪费CPU和网络资源
- 时延
### WebSocket解决了这些问题
- WebSocket是Html5的技术，应用层协议
- WebSocket通过向服务器发送Http请求，要求服务器转换到WebSocket协议
- WebSocket维持TCP连接，服务器和客户端可以互相发送消息，服务器不必等客户端发送请求之后才发消息。