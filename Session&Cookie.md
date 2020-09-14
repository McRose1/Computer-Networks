由于 HTTP 协议是**无状态**的协议，为了能够记住请求的状态，于是引入了 **session** 和 **cookie** 的机制。

Session 是存在于 server 端的，在单体式应用中，他是由 web 容器（如 Tomcat）管理的，存在于容器的内存中；

Cookie 是存在于 client 端的，也就是存在于浏览器

## Session
Session 代表 server 和 client 的一次会话过程，这个过程是连续的，也可以是时断断续的。

Session 是一个容器，可以存放会话过程中的任何对象

Session 的创建和使用总在 server 端，client 端浏览器从来没有得到过 session 对象，只是拿到 sessionId

在 Servlet 中，session 指的是 HttpSession 类的对象

## Session 的创建时间
直到某 server 端程序调用 HttpServletRequest.getSession(true) 这样的与语句时，session 才被闯进。

Session 会消耗内存资源，如果不打算使用 session，应该关闭它

## Session 的删除时间
1. Session 超时：超时指的是连续一定时间 server 没有收到该 session 所对应 client 的请求，并且这个时间冲过了 server 设置的 session 超时的最大时间
2. 程序调用 HttpSession.invalidate()
3. server 关闭或服务停止

## Session 存放位置
服务器的内存中。不过 session 可以通过特殊的方式做持久化管理

## sessionId
当 client 一次请求 session 对象时，server 会为 client 创建一个 session，并将通过特殊算法算出一个 sessionId，用来标识该 session 对象；

当浏览器下次（session 继续有效时）请求别的资源的时候，浏览器会将 sessionId 放置到 request header 中，server 接收到请求后就得到该 request 的 sessionId；

server 找到该 ID 的 session 返还给请求者（Servlet）使用。

一个会话只能有一个 session 对象，对 session 来说 sessionId 是唯一标识

## 同一个 client 机器多次请求同一个资源，session 是一样的吗?
不一定，对于多标签浏览器，在一个浏览器窗口中，多个标签同时访问一个页面，session 是一个；

多个浏览器窗口之间，同时或者相隔很短时间访问一个页面，session 是多个的，和浏览器的进程有关；

对于同一个浏览器窗口，直接录入 URL 访问同一应用的不同资源，session 是一样的。

** Cookie 
HTTP 的无状态性简化了服务器的设计，并且提高了 Web 服务器的性能，使其可以同时处理大量的 TCP 连接。但是一个 Web 站点通常希望能够识别用户，可能是为了限制用户的访问，也可能为了把内容与用户身份关联起来，为此 HTTP 使用了 cookie，cookie 是一种客户端的会话技术，允许站点对用户进行追踪。

*** Cookie 的 4 个组件
1. 在 HTTP 响应报文中有一个 cookie 首部行。
2. 在 HTTP 请求报文中有一个 cookie 首部行。
3. 在用户端系统中保留有一个 cookie 文件，并由用户的浏览器关联。
4. 位于 Web 站点的一个后端数据库。

*** 工作流程
当用户通过浏览器第一次访问某个站点时，该 Web 站点将产生一个唯一识别码，并以此作为索引在它的后端数据库中产生一个表项。接下来服务器会用一个包含 Set-cookie 首部的 HTTP 响应报文对浏览器进行响应，当浏览器收到后将其添加到自己管理的 cookie 文件中，在下次访问该站点时，请求报文的首部行中就会包括这个识别码，尽管浏览器不直到客户是谁，但是可以确定是同一个客户进行了访问。

** Cookie 和 Session 的区别
1. cookie 只能存储 ASCII 码字符串，而 session 则可以存储任何类型的数据，因此在考虑数据复杂性时互选 session。
2. session 存储在服务器，而 cookie 存储在客户浏览器中，容易被恶意查看。如果非要将一些隐私数据存在 cookie 中，可以将 cookie 值进行加密，然后再服务器进行解密。








