# DNS(Domain Name System)
域名解析系统

DNS 被设计为一个联机分布式数据库系统，并采用客户服务器方式。

DNS 使大多数名字都在本地进行解析，仅少量解析需要在互联网上通信，因此DNS效率很高。

由于DNS是分布式系统，即使单个计算机出现了故障也不会妨碍到整个DNS系统的正常运行。

resolves domain names(yahoo.com) to IP addresses(74.125.44.25)

像电话簿一样

**主机向本地域名服务器的查询一般都采用递归查询**，递归查询是指如果主机所访问的本地域名服务器不知道被查询域名的IP地址，那么本地域名服务器就以DNS客户的身份向其他根域名服务器继续发出查询请求报文。

递归查询的结果要么是查询的IP地址，或者是报错，表示无法查询到所属的IP地址。

**本地域名服务器向根域名服务器查询通常采用迭代查询**，迭代查询是指当根域名服务器收到本地域名服务器发出的迭代查询请求报文时，要么给出所要查询的IP地址，要么告诉它该向哪一个域名服务器进行查询。

本地域名服务器也可以采用递归查询，这取决于最初的查询请求报文设置的查询方式。

                                    root server 
                              1 /
client -> resolve server(ISP) 2 - TLD server
                              3 \        
                                   AN server

## Root Server 
- The top or the root, of the DNS hierarchy.
- 13 sets of these root servers strategically placed around the world
- Operated by 12 different organizations
- Each set has their own unique IP address 

## Top Level Domain Server 
- Stores the address information for top level domains 
- such as .COM / .NET / .ORG etc.

## Authoritative Name Server 
- Responsible for knowing everything about the domain 
- Including the IP address 
