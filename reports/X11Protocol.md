# X11 协议详解

## 建立连接

客户端首先和服务器协商字节序。

如果客户端希望 MSB First，发送`0x42`(ASCII: 大写B)；否则发送`0x6C`(ASCII: 小写 l)。之后的 16-bit 和 32-bit 都会按照这个字节序传送。服务端也会根据客户端报送的字节序发送数据。

然后发送如下信息，根据协商好的字节序：

```
protocol-major-version: CARD16
protocol-minor-version: CARD16
authorization-protocol-name: STRING8
authorization-protocol-data: STRING8
```

其中的`CARD16`表示16位无符号数；`STRING8`就是 C 语言的字符串。

version 字段指出了客户端希望服务器实现的版本。

如果两个 authroization 的字段均为空，那么就视为「没有显式认证」。authorization 不属于核心协议的一部分。

##