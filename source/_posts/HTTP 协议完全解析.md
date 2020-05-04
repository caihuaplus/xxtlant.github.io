---
title: HTTP 协议完全解析
id: 5
date: 2019-02-12 20:30:27
urlname: http_analysis
tags: 
	- HTTP
	- Android
categories: HTTP
cover: https://ww1.sinaimg.cn/large/007i4MEmgy1g03x0fvse0j3334223kjn.jpg
---

HTTP 的全称是 HyperText Transfer Protocol (超文本传输协议)的缩写，是一种建立在 TCP 上的无状态连接。HTTP 是互联网的**基础协议**，用于客户端与服务器之间的通信，它规定了客户端和服务器之间的**通信格式**，包括请求与响应的格式。
 <!--more-->

基本的工作流程是客户端发送一个 HTTP 请求，服务端收到请求开始处理，处理结束返回给客户端结果，客户端对结果进行处理并展示。

现在最流行的 HTTP 版本还是 1997 年发布的 HTTP/1.1。
![](https://ww1.sinaimg.cn/large/007i4MEmgy1fyuos2jbzvj30fb051t90.jpg)
## 目录
- HTTP 的工作方式
- HTTP 的报文格式
- HTTP 起始行
- HTTP 请求方法
- HTTP 响应码
- HTTP Headers
## 一、 HTTP 的工作方式
### 1.1 请求过程
客户端向服务端发送一段请求报文，服务端收到后，返回响应报文，客户端对响应内容进行展示。

<img src = "https://ww1.sinaimg.cn/large/007i4MEmgy1fyvp5x2xupj317g0nmdjm.jpg" width = 80%/>

一个 HTTP 的请求必定是**由客户端发起，服务器端回复响应**。服务器在没有接收到请求之前不会发送响应。

> 名词解释
> 
> 客户端：请求访问文本或图像等资源的一端。
> 
> 服务端：提供资源响应的一端。
> 
> 资源：网络上的一切内容都是资源，无论是图片文字还是动态代码。

### 1.2 请求方式
#### web 浏览器请求：
<img src="https://ww1.sinaimg.cn/large/007i4MEmgy1fyvq039dv9j30gt048gn8.jpg" width = 20% height = 20% />

请求过程如下 ⬇️
1. 用户输入地址后回车或点击链接
2. 浏览器拼装 HTTP 报文并发送请求给服务器 
3. 服务器处理请求后，发送响应报文给浏览器 
4. 浏览器解析响应报文并使用渲染引擎显示到界面

####  APP 客户端请求：
<img src="https://ww1.sinaimg.cn/large/007i4MEmgy1fyvq1pw7d9j30u009utas.jpg" width = 40% height = 40% />

请求过程如下 ⬇️
1. 用户点击或界面自动触发联网需求
2. Android 代码调用拼装 HTTP 报文并发送请求到服务器
3. 服务器处理请求后发送响应报文给手机
4. Android 代码处理响应报文并作出相应处理(如储存数据、加工数据、显示数据到界面)

### 1.3 报文是什么
报文是在 HTTP 应用程序之间发送的**数据块**。这些数据块以一些文本的元信息 (meta 标签中的信息) 开头，描述了报文的内容及含义。

每条报文都包含**一条来自于客户端的请求，或者一条来自于服务端的响应**。

它由 3 部分组成：对报文进行描述的「**起始行**」、包含属性的「**首部(Header)**」以及可选的、包含数据的「**主体(body)**」

![](https://ww1.sinaimg.cn/large/007i4MEmgy1fz1ph978fpj30nh0anwfy.jpg)

## 二、HTTP 的报文格式
### 2.1 请求报文的格式

```
<method> <path> <HTTP version>
<headers>

<entity-body>
```

### 2.2 响应报文的格式
(注意，**只有起始行的语法与请求报文有所不同**)

```
<HTTP version> <status code> <reason-phrase>
<headers>

<entity-body>
```

下面是对各部分的简要描述，后面会详细介绍。

- 方法 (method)
  客户端希望服务器对资源执行的动作，常见的方法有 Get、Post、HEAD 等。
- 请求路径 (path)
  请求的 URL 描述了要对哪个资源执行这个方法，是给服务器看的。
- HTTP 版本(HTTP version)  
- 状态码(status code) 
  不同状态码对应不同的响应状态
- 原因短句(reason-phrase) 
  对状态码进行简单的描述。
- 首部(headers)
  包含许多键值对，是对响应数据的一些格式信息。

## 三、 Method(请求方法)
最常见的请求方法就是 `GET` 和 `POST`了。除此之外还有 `PUT`、`DELETE`、`HEAD` 等。
### GET 
- 最常见的请求方式
- 指定请求路径，向服务器请求资源
- 只获取资源，不对服务器数据进行修改
- 不发送 body

```
GET  /users/1  HTTP/1.1
Host: api.github.com
```
对应的 Retrofit 的代码：

```
@GET("/users/{id}")
Call<User> getUser(@Path("id") String id, @Query("gender") String gender);
```
### Post 
- 用户增加或者修改资源
- 包含 body，发送给服务器的内容写在 body 里面

```
POST  /users  HTTP/1.1
Host: api.github.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 13
```
对应的 Retrofit 的代码：

```
@FormUrlEncoded
@POST("/users")
Call<User> addUser(@Field("name") String name, @Field("gender") String
gender);
```
### PUT 
- 用于修改资源
- 包含 body，发送给服务器的内容写在 body 里面

```
PUT  /users/1  HTTP/1.1
Host: api.github.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 13
```
对应的 Retrofit 的代码：

```
@FormUrlEncoded
@PUT("/users/{id}")
Call<User> updateGender(@Path("id") String id, @Field("gender") String
gender);
```

### DELETE 
- 用于删除资源
- 不发送 body

```
DELETE  /users/1  HTTP/1.1
Host: api.github.com
```
对应的 Retrofit 的代码：

```
@DELETE("/users/{id}")
Call<User> getUser(@Path("id") String id, @Query("gender") String gender);
```
### HEAD
- `HEAD` 与 `GET` 的使用方式完全相同。
- 区别在于，`HEAD` 请求的返回响应中没有 `Body`
- 用途：比如下载需求，返回的 Headers 中有下载内容的大小，可以用于显示进度。

### 小知识点
`GET`、`PUT`、`DELETE` 都是**幂等**操作，就是说，请求一次和请求多次的结果是一样的。
比如，GET 请求一个数据，请求一次和请求十次返回的结果是一样的，PUT 同样修改一个数据，修改一次和修改十次，结果也都是一样的。

## 四、状态码(status code)
状态码是对结果进行类型化的描述的，比如「请求成功」、「内容未找到」等
主要分为 5 类。

### 1xx：临时性消息。

  ```
  100：继续发送
  101：正在切换协议
  ```
###  2xx：成功。

  ```
  200：OK (最常见) 
  201：创建成功
  ```
###  3xx：重定向。

  ```
  301：域名永久移动
  302：暂时移动
  304：内容未改变，请求被重定向到客户端本地缓存
  ```
###  4xx：客户端错误

  ```
  400：客户端请求错误，服务器不理解请求的语法。
  401：未授权，要求进行身份验证。
  403：被禁止，服务器拒绝请求。
  404：找不到内容，服务器找不到请求的网页。(最常见)
  ```
###  5xx：服务器错误
  ```
  500：服务器内部错误 (最常见)
  503：服务不可用
  ```

## 五、首部(Headers)
首部包含多个请求头，是用来描述消息的元数据(meta data)。

首部字段有很多，主要分为以下几类：
- 通用首部 => 提供了与报文相关的最基本的信息
- 请求首部 => 只在请求报文中有意义的首部
- 响应首部 => 只在响应报文中有意义的首部
- body 首部 => 描述 body 的首部

下面我们就说一些比较常用的。

### 5.1 通用首部
#### Date
提供日期和时间标志，说明报文是在什么时间创建的。
```
Date: Tue, 12 Feb 2019 07:32:07 GMT
```
#### Connection
允许客户端和服务器指定与请求/响应连接有关的选项。
```
Connection: keep-alive
```
#### 其他通用首部
**Via** : 显示报文经过的中间节点（代理、网关）。
```
via: cache25.l2ot7-1[0,304-0,H], cache25.l2ot7-1[1,0], cache5.us14[342,200-0,H]
```
**Transfer-Encoding** : 告知接收端为了保证报文的可靠传输，对报文采用了什么编码方式。
```
Transfer-Encoding: chunked
```
**Cache-Control** : 指定缓存的控制方式。
```
cache-control: no-store, no-cache, must-revalidate, proxy-revalidate
```

### 5.2 请求首部
#### Host
给出了接收请求的服务器的主机名和端口。(注意:不是在网络上用于寻址的，⽽是在目标服务器上定位子服务器。)
```
Host: api.github.com
```
#### Accept
用来告诉**服务端**客户端会接受的媒体类型，包括客户端需要什么，可以使用什么，以及不想要什么。

```
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8

'*' 用来表示通配符。
;q= (q-factor weighting)
值代表优先顺序，用相对质量价值表示，又称为权重。
```
#### Accept-Charset
用来告知服务器，客户端可以处理的字符集类型。
```
Accept-Charset: utf-8, iso-8859-1;q=0.5
```
#### Accept-Encoding
用来将客户端能够理解的内容编码方式告知服务器，服务端会选择一个客户端提议的方式。
```
Accept-Encoding: gzip, deflate, br

gzip：表示采用 Lempel-Ziv coding (LZ77) 压缩算法，以及32位CRC校验的编码方式。
compress：采用 Lempel-Ziv-Welch (LZW) 压缩算法。
deflate：采用 zlib 结构和 deflate 压缩算法。
br：表示采用 Brotli 算法的编码方式。
identity：用于指代自身（例如：未经过压缩和修改）。除非特别指明，这个标记始终可以被接受。
```
#### Accept-Language
用来告诉服务器，客户端能够理解**哪些语言**

```
Accept-Language: zh-CN,zh;q=0.9,en;q=0.8
```
#### Range
告诉服务器，客户端要获取哪段数据。
主要用于：**断点续传**、**多线程下载**。
```
语法：
Range: <unit>=<range-start>-<range-end>, <range-start>-<range-end>, <range-start>-<range-end>
<unit>：范围所采用的单位，通常是字节（bytes）。
<range-start>：一个整数，表示在特定单位下，范围的起始值。
<range-end>：一个整数，表示在特定单位下，范围的结束值。这个值是可选的，如果不存在，表示此范围一直延伸到文档结束。

Range: bytes=200-1000, 2000-6576, 19000-
```
#### From
提供了客户端用户的 E-mail 地址，
用处：比如你有一个爬虫程序，那么 Form 首部应该随请求一起发送，这样的话，在服务器遇到问题的时候，例如爬虫发送了过量的、不希望收到的或者不合法的请求时，站点管理员可以联系到你。
### 5.3 响应首部
#### Server
包含了处理请求的源头服务器所用到的软件相关信息
```
Server: Apache-Coyote/1.1
```
#### Set-Cookie
用来由服务器端向客户端发送 cookie。

```
会话期 cookies 将会在客户端关闭时被移除。 
会话期 cookie 不设置 Expires 或 Max-Age 指令
Set-Cookie: sessionid=38afes7a8; HttpOnly; Path=/

持久化 Cookie 不会在客户端关闭时失效，而是在特定的日期（Expires）或者经过一段特定的时间之后（Max-Age）才会失效。

Set-Cookie: id=a3fWa; Expires=Wed, 21 Oct 2015 07:28:00 GMT; Secure; HttpOnly
```
#### Accept-Ranges
响应中出现，表示服务器支持按字节来取范围数据

```
语法：
Accept-Ranges: bytes
Accept-Ranges: none
none：不支持任何范围请求单位，由于其等同于没有返回此头部，因此很少使用。
bytes：范围请求的单位是 bytes （字节）。

Accept-Ranges: bytes
```

### 5.4 Body 首部
由于请求与响应中都可以包含 body部分，所以在请求报文与响应报文中都可以出现这部分字段。
#### Location 
指定的是需要将页面重新定向至的地址。一般在响应码为 3xx 的响应中才会有意义。
```
Location: /index.html
```
#### Content-Type
告诉客户端实际返回的内容的内容类型。
**主要分为一下 4 类:**
- text/html 

请求 Web ⻚面时返回响应的类型，Body 中返回 html 文本。

```
HTTP/1.1 200 OK
Content-Type: text/html; charset=utf-8
Content-Length: 853
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
```
- x-www-form-urlencoded

Web ⻚面纯⽂本表单的提交方式

```
POST  /users  HTTP/1.1
Host: api.github.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 27
name=xxtlant&gender=male
```

- multitype/form-data

Web ⻚面含有⼆制⽂件时的提交方式。

```
POST  /users  HTTP/1.1
Host: hencoder.com
Content-Type: multipart/form-data; boundary=----
WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Length: 2382
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="name"
rengwuxian
------WebKitFormBoundary7MA4YWxkTrZu0gW
Content-Disposition: form-data; name="avatar"; filename="avatar.jpg"
Content-Type: image/jpeg
JFIFHHvOwX9jximQrWa......
------WebKitFormBoundary7MA4YWxkTrZu0gW--
```

- application/json , image/jpeg , application/zip ...

单项内容(⽂本或非⽂本都可以)，用于 Web Api 的响应或者 POST / PUT 的请求。

> 请求中提交 JSON

```
POST /users HTTP/1.1
Host: api.github.com
Content-Type: application/json; charset=utf-8
Content-Length: 38
{"name":"xxtlant","gender":"male"}
```

> 响应中返回 JSON 

```
Date: Tue, 12 Feb 2019 08:04:14 GMT
Content-Type: application/json; charset=utf-8
Transfer-Encoding: chunked
Server: GitHub.com
Status: 200 OK

{"login": "xxtlant",
"id": 18342980,
"node_id": "MDQ6VXNlcjE4MzQyOTgw",
"avatar_url": "https://avatars2.githubusercontent.com/u/18342980?v=4",
"gravatar_id": "",
....
```

> 请求中提交⼆进制内容

```
POST /user/1/avatar HTTP/1.1
Host: hencoder.com
Content-Type: image/jpeg
Content-Length: 1575
JFIFHH9......
```

> 相应中返回⼆进制内容

```
HTTP/1.1 200 OK
content-type: image/jpeg
content-length: 1575
JFIFHH9......
```
#### Content-length
用来指明发送给接收方的消息主体的大小。

```
Content-Length: <length>
```
#### Content-Encoding
用于对特定媒体类型的数据进行压缩。
这个消息用来告知客户端应该怎样解码才能获取在 Content-Type 中的媒体类型内容。

```
Content-Encoding: gzip

其值受 Accept-Encoding 影响
```

与此对应，Content-Language 也与 Accept-Language 对应。

## 六、参考链接

- [HTTP](https://developer.mozilla.org/zh-CN/docs/Web/HTTP)
- [HTTP Headers](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers)
- [HTTP 协议入门](http://www.ruanyifeng.com/blog/2016/08/http.html)
 <!--more-->
