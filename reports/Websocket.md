# `Websocket`

## `Websocket`建立的初衷

我们最常用的网络通信协议是`HTTP`协议，那么为什么还需要`Websocket`协议呢？这首先要从`HTTP`协议的缺陷谈起。虽然`HTTP1.1`中有`keep-alive`协议(只是把多个`HTTP`请求合并在一起)，但请求只能由客户端发起请求，服务器接收并处理，`response-request`要一一对应，但如果服务器需要不断向用户推送动态的数据，即主动推送信息，`HTTP`协议却并不能做到，因为如果客户端没有请求服务器并不能主动回应数据。解决这个问题的一种方案是由浏览器每隔一段时间就向服务器发起询问请求，以得知服务器数据是否有更新，这种方案即轮询。轮询的效率很低，服务器需要反复不断解析`HTTP`请求，而且比较浪费资源。由此，`Websocket`就应运而生了。

## `Websocket`的概念和定义

`Websocket`在2008年完成，在2011年成为RFC 6455标准，并由RFC 7936补充协议标准，现已被绝大多数浏览器所支持。`Websocket`是基于`HTML5`的，再耽搁`TCP`连接上的全双工通讯协议。`Websocket`允许服务器和客户端之间只需一次握手就可以建立持久连接，并且客户端可以主动向客户端推送数据，两者之间可以进行双向数据交流通信。`Websocket`使用和`HTTP`相同的默认`80`端口，建立在`TLS`连接上时使用`443`端口。

## `Websocket`的优点

- 节约资源，加快数据同步速度。只需一次握手就可以建立持久连接，双向传送数据，避免了轮询造成的资源浪费，也加快的数据的更新速度。

- 服务器解析的开销变小。`Websocket`协议的通信头部明显短于`HTTP`，方便服务器进行解析和数据传输。

- 可以保存状态。连接一旦建立之后，一段时间内的通信无需再进行诸如身份验证的操作，减少了一些状态信息的传输。

- 拓展性好。`Websocket`定义了拓展，可以实现很多拓展协议和用户自定义的子协议。

- 更好的压缩效果。相比于`HTTP`压缩，`Websocket`可以利用上下文，加上一些拓展，实现更好的压缩效果。

- 更好的二进制支持。由于`Websocket`定义了二进制帧，通信时可以发送文本，也可以直接发送二进制数据。

## `Websocket`通信协议

Request:

```
GET / HTTP/1.1
Upgrade: websocket
Connection: Upgrade
Host: example.com
Origin: http://example.com
Sec-WebSocket-Key: sN9cRrP/n9NdMgdcy2VJFQ==
Sec-WebSocket-Version: 13
```

这段请求里多了

```
Upgrade: websocket
Connection: Upgrade
```

这两行是浏览器通知服务器把通信协议升级为`Websocket`。

```
Sec-WebSocket-Key: sN9cRrP/n9NdMgdcy2VJFQ==
```

这是随机字符串，服务器用这个随机字符串构造出一个SHA-1的信息摘要，处理后返回给客户端。这样可以避免协议错误，也可以对服务器身份进行验证。

Response:

```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: fFBooB7FAkLlXgRSz0BT3v4hq5s=
Sec-WebSocket-Location: ws://example.com/
```

其中

```
Upgrade: websocket
Connection: Upgrade
```

表示协议已经升级成功，之后服务器和客户端之间就按照`Websocket`协议进行通信。
