---
layout: post
title:  "http、https、restful"
date:   2018-06-27 12:38:00 +0800--
categories: [计算机网络]
tags: [http、https]  
---

### http简介

http是Hyper Text Transfer Protocol（超文本传输协议）的缩写，是应用层协议，用于从WWW服务器传输超文本到本地浏览器。
http是标准的c/s模型，由请求应答构成，通常是无状态的协议。
http协议问世于1990年，1996年5月被正式作为标准公布，版本命名为http/1.0

### http特性（restfull设计理念）

当前互联网环境的原创api服务设计需要考虑如下特点：

1. 伸缩性：即未知的并发量
2. 安全性：请求嗅探，未知请求
3. 耦合：异构系统之间松耦合
4. 简单性：不可引入过于复杂的编程模型

一个restful风格的设计包括五（六）大风格：

1. c/s模型：支持异构的多端系统
2. 无状态：不保存客户端的状态
3. 缓存：rest系统能恰当的缓存请求数据
4. 统一接口
5. 分层系统：客户端并不会与孤独的一个服务器打交道
6. 支持按需代码（可选）

一个良好的http协议包括上面的几大特性，区别于传统的rpc服务。

### http在协议栈的位置

首先清楚网络传输中协议的概念，为了解决不同硬件、操作系统之间的通信，需要定义一种各端都遵守的规则，称为协议。
http是基于tcp的，一个http请求从上而下包括：

1. 应用层：http所在的一层，有的还会包括TLS或SSL协议层，即所谓的https
2. 传输层tcp：tcp的三次握手确保了链接的可靠。
3. 网络层ip：ip协议的作用是底层数据包的传送，其中包括路由、ARP地址解析等过程
4. 数据链路层

应答传递方向则反过来。
一个http请求（例如用户输入）通常包括以下过程：

1. 客户端：DNS域名解析-》http协议生成请求报文-〉tcp协议分割报文段可靠的传输给对方-》ip协议负责搜索对方地址做中转传输。
2. 服务端：tcp接收报文段并重组-〉http协议对请求的内容做处理，结果回传

### URI和URL

URI是统一资源标识符，URL是统一资源定位符，我们在浏览器输入的地址就是一个URL。URI用字符串标识一个互联网资源，而URL则表示资源的地址，是URI的子集。
一个绝对的URI包括如下几个部分：

1. 协议方案名，如http、https、ftp等
2. 登录信息，用作身份认证
3. 服务器地址，可以使域名如www.yunyyyun.cn，也可使IPv4地址如111.231.2.23
4. 端口号，用于指定连接服务器的网络端口号
5. 带层次的网络路径，如/www/html
6. 查询字符串，如uid=1
7. 片段标识符，通常用于标记子资源

### http方法

http可使用如下多种方法：

1. get方法用于请求访问uri识别的资源，指定资源经由服务器解析后返回响应内容。如果资源是文本，则原样返回；是cgi程序，则结果程序执行后返回执行结果。
2. post方法用于传输实体的主体，其实get和post都可以传输实体的主体，但是一般采用post，其主要目的并不是获取响应的主体内容，这点区别于get。
3. put方法用于传输文件，一般web网站不使用该方法，因为安全问题
4. 其他方法包括head、delete、options等用的不多，这里不再赘述。

### https

http协议可能存在信息窃听、身份伪装等安全问题，列举如下：

1. 通信使用明文，内容可能会被抓包窃听
2. 不验证通信放身份，可能被伪装请求
3. 不能验证报文的完整性，可能会被篡改

所以才会有https来防止，https相对于http多了一层SSL（Secure Socket Layer）或TLS（Transport Layer Security）的组合，用于加密http的通信内容，这样有效解决上述问题1。
SSL提供一种证书手段，可用于确定对方，证书是值得信任的第三方机构颁发的验证手段，用于证明服务器和客户端是实际存在的。在使用https请求资源时，会先通过证书确认对方身份，再进行http请求，所以https比http慢，并且很多SSL证书是要花钱的～）。
SSL提供的加密处理和摘要功能，也保证了报文的完整性。
https里面SSL使用非对称加密（公钥+私钥）加证书的组合解决上面的问题，https通信的过程如下：

1. 客户端发送Client Hello报文开始SSL通信，报文中包含客户端支持的SSL的指定版本、加密组件列表（所用加密算法信息等）
2. 服务器可进行SSL通信时，会以Server Hello报文作为应答。
3. 服务器发送Certificate报文，公开密钥证书；
4. 服务器发送Server Done报文通知客户端，至此SSL握手协商过程结束。
5. SSL第一次握手结束，客户端以Client Key Exchange报文作为回应。报文包含一种pre-master Secret密钥串。
6. 客户端继续发送Change Cipher Spec报文，表示此后报文的通信会使用pre-master Secret加密
7. 客户端发送Finished报文。该报文包括连接至今的全部报文的整体校验值
8. 服务器发送Change Cipher Spec报文
9. 服务器发送Finished报文。
10. 客户端和服务器的Finished报文交换完毕，SSL连接建立完成。
11. 开始http通信。。。
    可知https相比于http多了SSL握手连接的过程。

#### 个人总结一下https建立连接的步骤：

1. 服务端在部署服务时，会网CA机构申请证书（证书包含：公钥+申请者与颁发者的相关信息+签名）
2. 客户端首次连接时，会往服务器请求证书（即上面的Client Hello报文）
3. 服务器返回证书
4. 客户端验证证书的合法性、有效性后，生成一个随机串（作为之后数据传输的密钥），使用证书中的公钥加密成加密串，发送给客户端
5. 服务端收到加密串后，使用私钥解密得到之后数据传输的密钥，之后双方通过此密钥对数据进行加密解密，确保数据安全