# HTTP

## HTTP Request Header
当浏览器请求网页时，它会向 Web服务器发送特定信息，这些信息不能被直接读取，因为它们是作为 HTTP request header 的一部分进行传输的：

| 头信息 | 描述 |
| ----------- | ----------- |
| Accept | 这个头信息指定浏览器或其他客户端可以处理的 MIME 类型。值 image/png 或 image/jpeg 是最常见的两种可能值 |
| Accept-Charset	 | 这个头信息指定浏览器可以用来显示信息的字符集。例如 ISO-8859-1 |
| Accept-Encoding	 | 这个头信息指定浏览器知道如何处理的编码类型。值 gzip 或 compress 是最常见的两种可能值 |
| Accept-Language	 | 这个头信息指定客户端的首选语言，在这种情况下，Servlet 会产生多种语言的结果。例如，en、en-us、ru 等 |
| Authorization		 | 这个头信息用于客户端在访问受密码保护的网页时识别自己的身份 |
| Connection | 这个头信息指示客户端是否可以处理持久 HTTP 连接。持久连接允许客户端或其他浏览器通过单个请求来检索多个文件。值 Keep-Alive 意味着使用了持续连接 |
| Content-Length	| 这个头信息只适用于 POST 请求，并给出 POST 数据的大小（以字节为单位）|
| Cookie | 这个头信息把之前发送到浏览器的 cookies 返回到服务器 |
| Host | 这个头信息指定原始的 URL 中的主机和端口 |
| If-Modified-Since	| 这个头信息表示只有当页面在指定的日期后已更改时，客户端想要的页面。如果没有新的结果可以使用，服务器会发送一个 304 代码，表示 Not Modified 头信息 |
| If-Unmodified-Since	| 这个头信息是 If-Modified-Since 的对立面，它指定只有当文档早于指定日期时，操作才会成功 |
| Referer	| 这个头信息指示所指向的 Web 页的 URL。例如，如果您在网页 1，点击一个链接到网页 2，当浏览器请求网页 2 时，网页 1 的 URL 就会包含在 Referer 头信息中 |
| User-Agent	| 这个头信息识别发出请求的浏览器或其他客户端，并可以向不同类型的浏览器返回不同的内容 |


## HTTP Response Header
当一个 Web服务器 response 一个 HTTP request 时，response 通常包括一个状态行、一些 response header、一个空行和文档：

```html
HTTP/1.1 200 OK
Content-Type: text/html
Header2: ...
...
HeaderN: ...
  (Blank Line)
<!doctype ...>
<html>
<head>...</head>
<body>
...
</body>
</html>
```
状态行包括：
- HTTP 版本（HTTP/1.1）
- 状态码（200）
- 对应于状态码的短消息（OK）

| 头信息 | 描述 |
| ----------- | ----------- |
| Allow | 这个头信息指定服务器支持的请求方法（GET、POST 等） |
| Cache-Control		 | 这个头信息指定响应文档在何种情况下可以安全地缓存。可能的值有：public、private 或 no-cache 等。Public 意味着文档是可缓存，Private 意味着文档是单个用户私用文档，且只能存储在私有（非共享）缓存中，no-cache 意味着文档不应被缓存 |
| Connection	 | 这个头信息指示浏览器是否使用持久 HTTP 连接。值 close 指示浏览器不使用持久 HTTP 连接，值 keep-alive 意味着使用持久连接 |
| Content-Disposition	 | 这个头信息可以让您请求浏览器要求用户以给定名称的文件把响应保存到磁盘 |
| Content-Encoding | 在传输过程中，这个头信息指定页面的编码方式 |
| Content-Language | 这个头信息表示文档编写所使用的语言。例如，en、en-us、ru 等 |
| Content-Length	| 这个头信息指示响应中的字节数。只有当浏览器使用持久（keep-alive）HTTP 连接时才需要这些信息 |
| Content-Type | 这个头信息提供了响应文档的 MIME（Multipurpose Internet Mail Extension）类型 |
| Expires	 | 这个头信息指定内容过期的时间，在这之后内容不再被缓存 |
| Last-Modified	| 这个头信息指示文档的最后修改时间。然后，客户端可以缓存文件，并在以后的请求中通过 If-Modified-Since 请求头信息提供一个日期 |
| Location	| 这个头信息应被包含在所有的带有状态码的响应中。在 300s 内，这会通知浏览器文档的地址。浏览器会自动重新连接到这个位置，并获取新的文档 |
| Refresh	| 这个头信息指定浏览器应该如何尽快请求更新的页面。您可以指定页面刷新的秒数 |
| Retry-After | 这个头信息可以与 503（Service Unavailable 服务不可用）响应配合使用，这会告诉客户端多久就可以重复它的请求 |
| Set-Cookie	| 这个头信息指定一个与页面关联的 cookie |






