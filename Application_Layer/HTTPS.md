# HTTPS
Hypertext Transfer Protocol over Secure Socket Layer

是以安全为目标的 HTTP 通道，简单讲是 HTTP 的安全版。

即 HTTP 下加入 SSL 层，HTTPS 的安全基础是 SSL。其所用的端口号是 443。

过程大致如下：
1. 建立连接获取证书：SSL 客户端通过 TCP 和服务器建立连接之后（443 端口），并且在一般的 TCP 连接协商（握手）过程中请求证书。即客户端发出一个消息给服务器，这个消息里面包含了自己可实现的算法列表和其它一些需要的消息，SSL 的服务器端会回应一个数据报，这里面确定了这次通信所需要的算法，然后服务器向客户端返回证书。（证书里面包含了服务器信息：域名。申请证书的公司，公共密钥）。
2. 证书验证：client 在收到服务器返回的证书后，判断签发这个整数的公共签发机构，并使用这个机构的公共密钥确认签名是否有效，客户端还会确保证书中列出的域名就是它正在连接的域名。
3. 数据加密和传输：如果确认证书有效，那么生成对称密钥并使用服务器的公共密钥进行加密。然后发送给服务器，服务器使用它的私钥对它进行解密，这样两台计算机可以开始进行对称加密进行通信。