# ARP(Address Resolution Protocol)
地址解析协议

Used to resolve IP addresses to MAC addresses 

通过一个ARP高速缓存存储本地局域网的各主机和路由器的IP地址到硬件地址（MAC）的映射表，以从网络层的IP地址解析出在数据链路层使用的硬件地址。

MAC(Devices need the MAC address for communication on a local area network（局域网）)

Devices use ARP to acquire the MAC address for a device

- An IP address is used to locate a decice on a network
- A MAC address is what identifies the actual device 

A -> B 

1. Computer A will first look at its internal list, called an **ARP cache**, to see if computer B's IP address already has a matching MAC address.      -> arp -a
2. Computer A will send out a broadcast message on the network asking every device which computer has the specific IP address and will ask for their MAC address
3. The computer with the matching IP address will then respond back and tell computer A its MAC address 
4. Once computer A receives computer B's MAC address, the communication can take place between the two(also store B's MAC address in its ARP cache) 


ARP 在 OSI 7 层模型中属于数据链路层；而在 TCP/IP 模型中属于网络层。


## ARP cache(table)
The ARP cache(table) is used to make a network more efficient.

It stores IP address to MAC address associations.

2种APR entries:
- Dynamic：A dynamic entry is created automatically when a device sends out a broadcast message out on the network. 
  - not permaanent, flushed out periodically.
- Static: A static entry is where someone manually enters an IP to MAC address assoication using the ARP command line utility.      -> arp -s IP address MAC address 
  - are often used to reduce any unnecessary ARP broadcast traffic on a network.

