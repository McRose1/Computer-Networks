由于 http 协议是无状态的协议，为了能够记住请求的状态，于是引入了 session 和 cookie 的机制。

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





