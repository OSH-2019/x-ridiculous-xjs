#  WebSocket 

##  WebSocket 建立的初衷

我们最常用的网络通信协议是`HTTP`协议，那么为什么还需要 WebSocket 协议呢？这首先要从`HTTP`协议的缺陷谈起。虽然`HTTP1.1`中有`keep-alive`协议(只是把多个`HTTP`请求合并在一起)，但请求只能由客户端发起请求，服务器接收并处理，`response-request`要一一对应，但如果服务器需要不断向用户推送动态的数据，即主动推送信息，`HTTP`协议却并不能做到，因为如果客户端没有请求服务器并不能主动回应数据。解决这个问题的一种方案是由浏览器每隔一段时间就向服务器发起询问请求，以得知服务器数据是否有更新，这种方案即轮询。轮询的效率很低，服务器需要反复不断解析`HTTP`请求，而且比较浪费资源。由此， WebSocket 就应运而生了。

##  WebSocket 的概念和定义

 WebSocket 在2008年完成，在2011年成为RFC 6455标准，并由RFC 7936补充协议标准，现已被绝大多数浏览器所支持。 WebSocket 是基于`HTML5`的，再耽搁`TCP`连接上的全双工通讯协议。 WebSocket 允许服务器和客户端之间只需一次握手就可以建立持久连接，并且客户端可以主动向客户端推送数据，两者之间可以进行双向数据交流通信。 WebSocket 使用和`HTTP`相同的默认`80`端口，建立在`TLS`连接上时使用`443`端口。 WebSocket 的结构如下图所示。

![websocket-architecture](websocket-architecture.jpg)

下图为`HTTP`和 WebSocket 的对比，从中我们可以清晰地看出二者的区别。

![http_vs_websocket](http_vs_websocket.png)


##  WebSocket 的优点

- 节约资源，加快数据同步速度。只需一次握手就可以建立持久连接，双向传送数据，避免了轮询造成的资源浪费，也加快的数据的更新速度。

- 服务器解析的开销变小。 WebSocket 协议的通信头部明显短于`HTTP`，方便服务器进行解析和数据传输。

- 可以保存状态。连接一旦建立之后，一段时间内的通信无需再进行诸如身份验证的操作，减少了一些状态信息的传输。

- 拓展性好。 WebSocket 定义了拓展，可以实现很多拓展协议和用户自定义的子协议。

- 更好的压缩效果。相比于`HTTP`压缩， WebSocket 可以利用上下文，加上一些拓展，实现更好的压缩效果。

- 更好的二进制支持。由于 WebSocket 定义了二进制帧，通信时可以发送文本，也可以直接发送二进制数据。

##  WebSocket 通信协议

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

这两行是浏览器通知服务器把通信协议升级为 WebSocket 。

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

表示协议已经升级成功，之后服务器和客户端之间就按照 WebSocket 协议进行通信。

##  WebSocket 用法的简单示例

### 构造函数，实例化 WebSocket 对象，实现客户端与服务器的连接

```js
var websocket = new WebSocket('ws://loaclhost:8000');
```

### 实例的当前状态

通过`websocket.readyState`返回实例当前状态。共有四种状态，分别是：

* CONNECTING(正在连接): value = 0;

* OPEN(连接成功): value = 1;

* CLOSING(连接正在关闭): value = 3;

* CLOSED(连接关闭或连接建立失败): value = 4;

通过状态的值可以知道当前连接的状况。下为示例：

```js
switch (websocket.readState)
{
    case WebSocket.CONNECTING:
    //PASS
    break;
    case WebSocket.OPEN:
    //PASS
    break;
    case WebSocket.CLOSED:
    //PASS
    break;
    case WebSocket.CLOSING:
    //PASS
    break;
    default:
    //PASS
    break;
}

```

### 指定连接成功后的回调函数

使用实例对象的`onopen`属性，可以指定连接成功后的回调函数。下为示例：

```js
websocket.onopen = function(){
    websocket.send('Hello OSH-2019 lab X!');
}
```

### 指定连接关闭后的回调函数

使用实例对象的`onclose`属性，可以指定连接关闭后的回调函数。

```js
websocket.onclose = function(e){
    var code = e.code;
    var reason = e.reason;
    var wasClean = e.wasClean;
    //PASS
}
```

### 指定收到服务器数据后的回调函数

使用实例对象的`onmessage`属性，可以指定收到服务器数据后的回调函数。

```js
websocket.onmessage = function(e){
    var web_data= e.data;
}
```

### 发送数据

使用实例对象的`send`方法向建立连接的服务器发送数据。

```js
websocket.send('Hello OSH-2019 lab X!');
```

除了发送文本内容之外，也可以发送`ArrayBuffer`对象。

```js
//Send canvas imagedata.
var img = canvas_context.getImageData(0, 0, 600, 480);
var binary = new Uint8Array(img.data.length);
for (var i = 0; i < img.data.length; i++) {
  binary[i] = img.data[i];
}
websocket.send(binary.buffer);
```

这时，我们可以通过实例对象的`bufferedAmount`属性来获知还有多少字节的数据没有发送完成，这可以用来判断发送是否结束。

```js
if (websocket.bufferedAmount === 0)
{
    //Sending is completed.
}
else
{
    //Sending is not completed now.
}
```

### 指定报错时的回调函数

使用实例对象的`onerror`属性，可以指定报错时的回调函数。

```js
websocket.onerror = function(e){
    var error = e.error;
    //You can handle the error there.
}
```
