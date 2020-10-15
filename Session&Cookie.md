## Cookie & Session

由于 HTTP 协议是**无状态**的协议，为了能够记住请求的状态，于是引入了 **session** 和 **cookie** 的机制。

Session 是存在于 server 端的，在单体式应用中，他是由 web 容器（如 Tomcat）管理的，存在于容器的内存中；

Cookie 是存在于 client 端的，也就是存在于浏览器

## Session
session 是服务器端使用的一种记录客户端状态的机制。

Session 代表 server 和 client 的一次会话过程，这个过程是连续的，也可以是时断断续的。

Session 是一个容器，可以存放会话过程中的任何对象

如果说 cookie 机制是通过客户身上的“通信证”来确定客户身份的话，那么 session 机制就是通过检查服务器上的“客户明细表”来确认客户身份。session 相当于程序在服务器上简历的一份客户档案，客户来访的时候只需要查询客户档案就可以了。

**Session 的创建和使用总在 server 端，client 端浏览器从来没有得到过 session 对象，只是拿到 sessionId**

在 Servlet 中，session 指的是 javax.servlet.http.HttpSession 类的对象

每个来访者对应一个 Session 对象，所有该客户的状态信息都保存在这个 Session 对象里。

session 也是一种 key-value 的属性对，通过 getAttribute(StringKey) 和 setAttribute(String key, Objectvalue) 方法读写客户状态信息。

Servlet 中通过 request.getSession() 方法获取该客户的 session。

## Session 的创建时间
**Session 对象是客户端第一次请求服务器的时候创建的**。需要注意只有访问 JSP、Servlet 等程序时才会创建 session，只访问 HTML、IMAGE 等静态资源并不会创建 session。

getSession(): retrieve the current session, and if one doesn't exist yet, create it.

getSession(true): retrieve the current session, and if one doesn't exist yet, create it. (same as no boolean)

getSession(false): retrieve the current session, and if one doesn't exist yet, return null.

session 生成后，只要用户继续访问，服务器就会更新 session 的最后访问时间，并维护该 session。用户每访问服务器一次，无论是否读写 session，服务器都认为该用户的 session active 了一次。

## Session 的删除时间
Session 会消耗内存资源，为了防止内存溢出，如果不打算使用 session，应该关闭它，服务器也会把长时间没有 active 的 session 从内存删除，这个时间就是 session 的超时时间，如果超过超时时间没访问过服务器，session 就会自动失效。


1. Session 超时：超时指的是连续一定时间 server 没有收到该 session 所对应 client 的请求，并且这个时间冲过了 server 设置的 session 超时的最大时间
  - maxInactiveInterval
    - getMAXInactiveInterva()
    - setMaxInactiveInterval(longinterval)
  - 在 web.xml 中修改
2. 程序调用 HttpSession.invalidate()
3. server 关闭或服务停止

## Session 存放位置
服务器的内存中。不过 session 可以通过特殊的方式做持久化管理

## sessionId
当 client 一次请求 session 对象时，server 会为 client 创建一个 session，并将通过特殊算法算出一个 sessionId，用来标识该 session 对象；

当浏览器下次（session 继续有效时）请求别的资源的时候，浏览器会将 sessionId 放置到 request header 中，server 接收到请求后就得到该 request 的 sessionId；

server 找到该 ID 的 session 返还给请求者（Servlet）使用。

**一个会话只能有一个 session 对象，对 session 来说 sessionId 是唯一标识**

虽然 session 保存在服务器，对客户端是透明的，它的正常运行仍然需要客户端浏览器的的支持。这是因为**session 需要使用 cookie 作为识别标志**。

由于 HTTP 协议是无状态的，session 不能依据 HTTP 连接来判断是否为同一个客户，因此服务器向客户端浏览器发送一个名为 **JSESSIONID 的 cookie**，它的值为该 session 的 ID（也就是 HttpSession.getId() 的返回值）。session 依据该 cookie 来识别是否为同一用户。所以，sessionId 是存在 cookie 中的。

该 cookie 为服务器自动生成的，它的 maxAge 属性一般为 -1，表示仅在当前浏览器内有效，并且各浏览器窗口间不共享，关闭浏览器就会失效。

一次你同一机器的 2 个浏览器窗口访问服务器时，会生成 2 个不同的 session。但是由浏览器窗口内的链接、脚本等打开的新窗口（也就是说不是双击桌面浏览器图标等打开的窗口）除外。这类子窗口会共享父窗口的 cookie，因此会共享一个 session。

注意：新开的浏览器窗口会生成新的 session，但子窗口除外。子窗口会共有父窗口的 session。

### 同一个 client 机器多次请求同一个资源，session 是一样的吗?
不一定，对于多标签浏览器，在一个浏览器窗口中，多个标签同时访问一个页面，session 是一个；

多个浏览器窗口之间，同时或者相隔很短时间访问一个页面，session 是多个的，和浏览器的进程有关；

对于同一个浏览器窗口，直接录入 URL 访问同一应用的不同资源，session 是一样的。

### 如果客户端浏览器将 cookie 功能禁用，或者不支持 cookie 怎么办？
URL 地址重写是对客户端不支持 cookie 的解决方案。

URL 地址重写的原理是将用户 session 的 ID 信息重写到 URL 中。服务器能够解析重写后的 URL 获取 session 的 ID。

HttpServletResponse 类提供了 encodeURL(Stringurl) 实现 URL 地址重写。该方法会自动判断客户端是否支持 cookie。如果支持，会将 URL 原封不动地输出来，如果不支持，则会将用户 session 的 ID 重写到 URL 中。即在文件名的后面，在 URL 参数的前面添加了字符串“;jsessionid=XXX”。其中 XXX 为 session 的 ID。



## Cookie 
HTTP 的无状态性简化了服务器的设计，并且提高了 Web 服务器的性能，使其可以同时处理大量的 TCP 连接。但是一个 Web 站点通常希望能够识别用户，可能是为了限制用户的访问，也可能为了把内容与用户身份关联起来，为此 HTTP 使用了 cookie，cookie 是一种客户端的会话技术，允许站点对用户进行追踪。

### Cookie 的 4 个组件
1. 在 HTTP 响应报文中有一个 cookie 首部行。
2. 在 HTTP 请求报文中有一个 cookie 首部行。
3. 在用户端系统中保留有一个 cookie 文件，并由用户的浏览器关联。
4. 位于 Web 站点的一个后端数据库。

### 工作流程
当用户通过浏览器第一次访问某个站点时，该 Web 站点将产生一个唯一识别码，并以此作为索引在它的后端数据库中产生一个表项。接下来服务器会用一个包含 **Set-cookie 首部的 HTTP Response 报文**对浏览器进行响应，当浏览器收到后将其添加到自己管理的 cookie 文件中，在下次访问该站点时，请求报文的首部行中就会包括这个识别码，尽管浏览器不知道客户是谁，但是可以确定是同一个客户进行了访问。

### cookie 的有效期
maxAge 决定着 cookie 的有效期，cookie 中通过 getMaxAge() 和 setMaxAge(int maxAge) 方法来读写 maxAge 属性。

- 如果 maxAge 是正数，则表示该 cookie 会在 maxAge 秒之后自动失效。浏览器会将 maxAge 为正数的 cookie 持久化，即写到对应的 cookie 文件中。无论客户关闭了浏览器还是电脑，只要还在 maxAge 秒之前，登录网站时该 cookie 仍然有效。
- 如果 maxAge 为负数，则表示该 cookie 仅在本浏览器创窗口以及本窗口打开的子窗口内有效，关闭窗口后该 cookie 即失效。maxAge  为负数的 cookie，为临时性 cookie，不会被持久化，不会被写到 cookie 文件中。cookie 信息保存在浏览器内存中，因此关闭浏览器该 cookie 就消失了。cookie 默认的 maxAge 为 -1。
- 如果 maxAge 为 0，则表示删除该 cookie。cookie 机制没有提供删除 cookie 的方法，因此通过设置该 cookie 即时失效实现删除 cookie 的效果。失效的 cookie会被浏览器从 cookie 文件中或者内存中删除。

```java
Cookie cookie = new Cookie("username", "123455"); // 新建 cookie
cookie.setMaxAge(0);          // 设置声明周期为 0，不能为负数
response.addCookie(cookie);   // 必须执行这一句
```

response 对象提供的 cookie 操作方法只有一个添加操作 addCookie(Cookie).

要想修改 cookie 只能使用一个同名的 cookie 来覆盖原来的 cookie，达到修改的目的。

### cookie 的安全属性
因为 HTTP 协议本身是不安全的，所以在 HTTP 里使用 cookie 也是不安全的。

如果不希望 cookie 在 HTTP 等非安全协议中传输，可以设置 cookie 的 secure 属性为 true。浏览器只会在 HTTPS 和 SSL 等安全协议中传输此类 cookie。

```java
Cookie cookie = new Cookie("username", "123455"); // 新建 cookie
cookie.setSecure(true);          // 设置安全属性
response.addCookie(cookie);      // 输出到客户端 
```

提示：secure 属性并不能对 cookie 内容加密，因而不能保证绝对的安全性。如果需要高安全性，需要在程序中对 cookie 内容加密、解密，以防泄密。

## Cookie 和 Session 的区别
1. session 存储在服务器，而 cookie 存储在客户浏览器中；cookie 会随着 HTTP header 在网络层进行传输，而 session 存在于服务器端，不经过传输
  - 当你登录一个网站的时候，如果 web 服务器端使用的是 session，那么所有的数据都保存在服务器上，而 session ID 这一数据则是保存在客户端的，用 cookie 保存的。客户端每次请求服务器的时候会发送**当前会话的 session ID**，服务器根据当前的 session ID 来判断相应的用户数据标志，以确定用户是否登录，或具有某种权限。由于数据是存储在服务器上的，所以你不能伪造，但是如果你能够获取某个登录用户的 session ID，用特殊的浏览器伪造该用户的请求也是能够成功的。
2. cookie 不安全，容易被恶意查看。别人可以分析存档在本地的 cookie 并进行 cookie 欺骗，如果非要将一些隐私数据存在 cookie 中，可以将 cookie 值进行加密，然后再服务器进行解密，或者直接使用 session。
3. cookie 只能存储 ASCII 码字符串，而 session 则可以存储任何类型的数据，因此在考虑数据复杂性时互选 session。
4. 单个 cookie 保存的数据不能超过 4K，很多浏览器都限制一个站点最多保存 20 个 cookie。session 对象没有对存储的数据量的限制
5. 最大区别在于生命周期，session 的生命周期在浏览器启动到关闭（session destory()）；而 cookie 是预先设置的生命周期，或永久的保存于本地的文件。








