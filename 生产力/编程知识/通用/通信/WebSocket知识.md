---
created: 2025-03-22T23:10
updated: 2024-05-21T18:17
创建时间: 2025-03-22T23:10
更新时间: 2024-05-21T18:17
---
# WebSocket知识

## 简介

### 什么是WebSocket

WebSocket是一种协议，用于在Web应用程序和服务器之间建立实时、双向的通信连接。它通过一个单一的TCP连接提供了持久化连接，这使得Web应用程序可以更加实时地传递数据。WebSocket协议最初由W3C开发，并于2011年成为标准。



### WebSocket的优势和劣势

**WebSocket的优势包括：**

- **实时性：** 由于WebSocket的持久化连接，它可以实现实时的数据传输，避免了Web应用程序需要不断地发送请求以获取最新数据的情况。
- **双向通信：** WebSocket协议支持双向通信，这意味着服务器可以主动向客户端发送数据，而不需要客户端发送请求。
- **减少网络负载：** 由于WebSocket的持久化连接，它可以减少HTTP请求的数量，从而减少了网络负载。

**WebSocket的劣势包括：**

- **需要浏览器和服务器都支持：** WebSocket是一种相对新的技术，需要浏览器和服务器都支持。一些旧的浏览器和服务器可能不支持WebSocket。
- **需要额外的开销：** WebSocket需要在服务器上维护长时间的连接，这需要额外的开销，包括内存和CPU。
- **安全问题：** 由于WebSocket允许服务器主动向客户端发送数据，可能会存在安全问题。服务器必须保证只向合法的客户端发送数据。



## 基本概念

### WebSocket协议

WebSocket 协议是一种基于TCP的协议，用于在客户端和服务器之间建立持久连接，并且可以在这个连接上实时地交换数据。WebSocket协议有自己的握手协议，用于建立连接，也有自己的数据传输格式。

当客户端发送一个 WebSocket 请求时，服务器将发送一个协议响应以确认请求。在握手期间，客户端和服务器将协商使用的协议版本、支持的子协议、支持的扩展选项等。一旦握手完成，连接将保持打开状态，客户端和服务器就可以在连接上实时地传递数据。

WebSocket 协议使用的是双向数据传输，即客户端和服务器都可以在任意时间向对方发送数据，而不需要等待对方的请求。它支持二进制数据和文本数据，可以自由地在它们之间进行转换。

总之，WebSocket协议是一种可靠的、高效的、双向的、持久的通信协议，它适用于需要实时通信的Web应用程序，如在线游戏、实时聊天等。



### WebSocket生命周期

WebSocket 生命周期描述了 WebSocket 连接从创建到关闭的过程。一个 WebSocket 连接包含以下四个主要阶段：

- **连接建立阶段（Connection Establishment）：** 在这个阶段，客户端和服务器之间的 WebSocket 连接被建立。客户端发送一个 WebSocket 握手请求，服务器响应一个握手响应，然后连接就被建立了。
- **连接开放阶段（Connection Open）：** 在这个阶段，WebSocket 连接已经建立并开放，客户端和服务器可以在连接上互相发送数据。
- **连接关闭阶段（Connection Closing）：** 在这个阶段，一个 WebSocket 连接即将被关闭。它可以被客户端或服务器发起，通过发送一个关闭帧来关闭连接。
- **连接关闭完成阶段（Connection Closed）：** 在这个阶段，WebSocket 连接已经完全关闭。客户端和服务器之间的任何交互都将无效。

> 需要注意的是，WebSocket 连接在任何时候都可能关闭，例如网络故障、服务器崩溃等情况都可能导致连接关闭。因此，需要及时处理 WebSocket 连接关闭的事件，以确保应用程序的可靠性和稳定性。

下面是一个简单的 WebSocket 生命周期示意图：

<img src="http://images.xiaohai-hx.cn/复习笔记/面试题/生命周期.png" alt="生命周期" style="zoom:67%;" />

在这个示意图中，客户端向服务器发送一个 WebSocket 握手请求，服务器响应一个握手响应，连接就被建立了。一旦连接建立，客户端和服务器就可以在连接上互相发送数据，直到其中一方发送一个关闭帧来关闭连接。在关闭帧被接收后，连接就会被关闭，WebSocket 连接关闭完成。



### WebSocket消息格式

WebSocket 的消息格式与 HTTP 请求和响应的消息格式有所不同。WebSocket 的消息格式可以是文本或二进制数据，并且 WebSocket 消息的传输是在一个已经建立的连接上进行的，因此不需要再进行 HTTP 请求和响应的握手操作。

WebSocket 消息格式由两个部分组成：消息头和消息体。

消息头包含以下信息：

- **FIN：** 表示这是一条完整的消息，一般情况下都是1。
- **RSV1、RSV2、RSV3：** 暂时没有使用，一般都是0。
- **Opcode：** 表示消息的类型，包括文本消息、二进制消息等。
- **Mask：** 表示消息是否加密。
- **Payload length：** 表示消息体的长度。
- **Masking key：** 仅在消息需要加密时出现，用于对消息进行解密。

消息体就是实际传输的数据，可以是文本或二进制数据。



### WebSocket的API

WebSocket API 是用于在 Web 应用程序中创建和管理 WebSocket 连接的接口集合。WebSocket API 由浏览器原生支持，无需使用额外的 JavaScript 库或框架，可以直接在 JavaScript 中使用。

下面是一些常用的 WebSocket API：

**WebSocket 构造函数：** WebSocket 构造函数用于创建 WebSocket 对象。它接受一个 URL 作为参数，表示要连接的 WebSocket 服务器的地址。例如：

```js
let ws = new WebSocket('ws://example.com/ws');
```

**WebSocket.send() 方法：** `WebSocket.send()` 方法用于向服务器发送数据。它接受一个参数，表示要发送的数据。数据可以是字符串、Blob 对象或 ArrayBuffer 对象。例如：

```js
ws.send('Hello, server!');
```

**WebSocket.onopen 事件：** `WebSocket.onopen` 事件在 WebSocket 连接成功建立时触发。例如：

```js
ws.onopen = function() {
  console.log('WebSocket 连接已经建立。');
};
```

**WebSocket.onmessage 事件：** `WebSocket.onmessage` 事件在接收到服务器发送的消息时触发。它的 event 对象包含一个 data 属性，表示接收到的数据。例如：

```js
ws.onmessage = function(event) {
  console.log('收到服务器消息：', event.data);
};
```

**WebSocket.onerror 事件：** `WebSocket.onerror` 事件在 WebSocket 连接出现错误时触发。例如：

```js
ws.onerror = function(event) {
  console.error('WebSocket 连接出现错误：', event);
};
```

**WebSocket.onclose 事件：** `WebSocket.onclose` 事件在 WebSocket 连接被关闭时触发。例如：

```js
ws.onclose = function() {
  console.log('WebSocket 连接已经关闭。');
};
```

以上是一些常用的 WebSocket API。



## WebSocket的性能

### 与传统的HTTP请求/响应模型比较

- **双向通信性能更好：** WebSocket协议使用单一的TCP连接，允许客户端和服务器在同一个连接上进行双向通信。这种实时的双向通信可以更快地传输数据，而不需要建立多个HTTP请求/响应连接。
- **更小的网络流量：** 与HTTP相比，WebSocket协议需要更少的网络流量来维护连接，因为它不需要在每个请求/响应交换中发送头部信息。
- **更低的延迟：** WebSocket协议允许服务器主动向客户端推送消息，而不需要客户端先发送请求。这种实时通信可以减少响应延迟，并提高应用程序的性能。
- **更好的服务器资源管理：** 由于WebSocket连接可以保持活动状态，服务器可以更好地管理客户端连接，减少服务器开销和处理时间。

WebSocket协议的性能比传统的HTTP请求/响应模型更好，特别是在实时通信和低延迟方面。WebSocket协议适用于需要实时通信和实时数据更新的应用程序，如在线聊天、多人游戏、实时监控等。



### 优化WebSocket 的性能

- **减少消息大小：** WebSocket 传输的数据大小对性能有很大影响。尽量减少消息的大小，可以降低网络带宽和服务器负载。例如，可以使用二进制传输协议来代替文本传输，或使用压缩算法对消息进行压缩。
- **使用CDN加速：** 使用 CDN（内容分发网络）可以将静态资源缓存到离用户更近的节点上，提高传输速度和性能。CDN 可以缓存 Websocket 的初始握手请求，避免不必要的网络延迟。
- **使用负载均衡：** WebSocket 服务可以使用负载均衡来分配并平衡多个服务器的负载。负载均衡可以避免单个服务器被过载，并提高整个服务的可伸缩性。
- **优化服务端代码：** WebSocket 服务端代码的性能也是关键因素。使用高效的框架和算法，避免使用过多的内存和 CPU 资源，可以提高服务端的性能和响应速度。
- **避免网络阻塞：** WebSocket 的性能也会受到网络阻塞的影响。当有太多的连接同时请求数据时，服务器的性能会下降。使用合适的线程池和异步 IO 操作可以避免网络阻塞，提高 WebSocket 服务的并发性能。



## WebSocket的扩展应用和未来发展方向

- **更加完善的标准规范：** WebSocket 标准规范还有很多可以优化的地方，未来可能会继续完善 WebSocket 的标准规范，以适应更加复杂的应用场景。
- **更加安全的通信方式：** 由于 WebSocket 的开放性，使得它可能会受到一些安全威胁，未来可能会通过加密、身份验证等方式来增强 WebSocket 的安全性。
- **更好的兼容性：** WebSocket 协议需要在 HTTP 协议的基础上建立连接，因此可能会遇到兼容性问题，未来可能会通过技术手段来解决这些问题。
- **更好的性能和可伸缩性：** WebSocket 协议的性能和可伸缩性对于复杂的应用场景非常关键，未来可能会通过技术手段来进一步提高 WebSocket 的性能和可伸缩性。



> 转载：
>
> [公众号](https://mp.weixin.qq.com/s/F5bg9bAe3H6hLQ4XLeex4A)





