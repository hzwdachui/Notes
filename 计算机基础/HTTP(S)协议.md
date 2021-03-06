# TCP/IP协议簇
https://zhuanlan.zhihu.com/p/72587882?utm_source=wechat_session&utm_medium=social&utm_oi=681967281391669248

![](https://pic4.zhimg.com/80/v2-6cacedff60b984ce8063f76c4f876da3_720w.jpg)

**什么时候应该使用TCP？**
当对网络通讯质量有要求的时候，比如：整个数据要准确无误的传递给对方，这往往用于一些要求可靠的应用，比如HTTP、HTTPS、FTP等传输文件的协议，POP、SMTP等邮件传输的协议。

**什么时候应该使用UDP？**
当对网络通讯质量要求不高的时候，要求网络通讯速度能尽量的快，这时就可以使用UDP。


# 什么是HTTP协议
https://blog.csdn.net/xiaoming100001/article/details/81109617
- **超文本传输协议**
	- 基于请求响应
	- 无状态
	- 应用层协议，常基于TCP/IP协议传输数据
	- 目前使用http/1.1或http/2

**进化史：**
- http/1.1开始支持**长连接**
- http/2支持**多路复用**

Q:无状态
A:协议对客户端没有状态存储，对事物处理没有“记忆”能力，比如访问一个网站需要反复进行登录操作

Q:无连接和长连接
A:
- **无连接**是每次请求都要经历三次握手四次挥手
- **长连接**是在请求头加入``Connection: keep-alive``，没有明确提出断开连接则保持TCP连接状态

Q:多路复用
A:通过单一的HTTP/2连接请求发起多重的请求-响应消息，多个请求stream共享一个TCP连接，实现多留并行而不是依赖建立多个TCP连接。

### HTTP通信传输过程（输入ip以后）
- 客户端输入URL回车，DNS解析域名得到服务器的IP地址，服务器在80端口监听客户端请求
- 端口通过TCP/IP协议（可以通过Socket实现）建立连接

### DNS解析过程
略

### 建立/断开连接过程
三次握手四次挥手

为什么三次挥手

为什么四次握手

### HTTP请求方法
GET 和 POST方法的对比

### Cookie，Session
1. 由于HTTP协议是**无状态的协议**，所以服务端需要记录用户的状态时，就需要用某种机制来识具体的用户，这个机制就是Session.典型的场景比如购物车，当你点击下单按钮时，由于HTTP协议无状态，所以并不知道是哪个用户操作的，所以**服务端要为特定的用户创建了特定的Session，用用于标识这个用户，并且跟踪用户**，这样才知道购物车里面有几本书。这个Session是保存在服务端的，有一个唯一标识。在服务端保存Session的方法很多，内存、数据库、文件都有。集群的时候也要考虑Session的转移，在大型的网站，一般会有专门的Session服务器集群，用来保存用户会话，这个时候 Session 信息都是放在内存的，使用一些缓存服务比如Memcached之类的来放 Session。
2. 思考一下服务端如何识别特定的客户？这个时候Cookie就登场了。每次HTTP请求的时候，客户端都会发送相应的Cookie信息到服务端。**实际上大多数的应用都是用 Cookie 来实现Session跟踪的，第一次创建Session的时候，服务端会在HTTP协议中告诉客户端，需要在 Cookie 里面记录一个Session ID，以后每次请求把这个会话ID发送到服务器，**我就知道你是谁了。有人问，如果客户端的浏览器禁用了 Cookie 怎么办？一般这种情况下，会使用一种叫做URL重写的技术来进行会话跟踪，即每次HTTP交互，URL后面都会被附加上一个诸如 sid=xxxxx 这样的参数，服务端据此来识别用户。
3. Cookie其实还可以用在一些方便用户的场景下，设想你某次登陆过一个网站，下次登录的时候不想再次输入账号了，怎么办？这个信息可以写到Cookie里面，访问网站的时候，网站页面的脚本可以读取这个信息，就自动帮你把用户名给填了，能够方便一下用户。这也是Cookie名称的由来，给用户的一点甜头。所以，总结一下：Session是在服务端保存的一个数据结构，用来跟踪用户的状态，这个数据可以保存在集群、数据库、文件中；Cookie是客户端保存用户信息的一种机制，用来记录用户的一些信息，也是实现Session的一种方式。

# 什么是HTTPS协议
- HTTPS是一种通过计算机网络进行安全通信的传输协议，
- 经由HTTP进行通信，利用SSL/TLS建立全信道，加密数据包。

# 简述SSL和CA
## SSL加密
- 内容加密：**混合加密技术**，中间者无法直接查看明文内容
	- 结合对称+非对称加密技术
- 验证身份：通过证书认证客户端访问的是自己的服务器
- 保护数据完整性：防止传输的内容被中间人冒充或者篡改
	- 非对称加密

![](https://img-blog.csdn.net/20180719103559793?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3hpYW9taW5nMTAwMDAx/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)
**解释：**
- 非对称加密：
	- 私钥加密，公钥解密
- 混合加密：
	- 网络上传输的数据是被秘钥加密的密文和用公钥加密后的秘密秘钥
	- 传输数据：对称密钥
	- 使用公钥加密上一步的密钥
- 数字摘要：
	- 单向hash
	- MD5，SHA1，SHA256
- 数字签名：
	- 数字摘要经过加密，使用非对称加密

## CA证书
- 数字证书通常来说是由受信任的数字证书颁发机构CA，在验证服务器身份后颁发，证书中包含了一个密钥对（公钥和私钥）和所有者识别信息。
- 数字证书被放到服务端，具有服务器身份验证和数据传输加密功能

**验证过程**
证书 = 公钥 + 申请者与颁发者信息 + 签名
客户端会内置信任 CA 的证书信息(包含公钥)，如果CA不被信任，则找不到对应 CA 的证书，证书也会被判定非法。
- 客户端向服务器**发出请求**
- 服务器**返回证书文件**， **加密原文**
- **客户端读取证书信息**
	- 客户端然后验证证书相关的域名信息、有效时间等信息；
	- 对比摘要（验证是否被篡改）


