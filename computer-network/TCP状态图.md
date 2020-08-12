## TCP状态图

![](https://github.com/scottkaykay/common-knowledge/blob/master/computer-network/tcp%E7%8A%B6%E6%80%81%E5%9B%BE.png)

## 说明

· CLOSED: 起始点。在超时或者连接关闭的时候进入此状态。这不是一个真正的状态，而是这个状态图的假想起点。\
· LISTEN：服务器等待连接的状态，服务器经过socket创建，bind绑定IP和端口，listen监听端口，等待客户端的连接请求。\
· SYN_SENT：第一次握手发生阶段，客户端调用connect发起连接，发送SYN给服务端，进入SYN_SENT状态，等待服务器确认，如果服务器不能连接，则直接进入CLOSED状态\
· SYN_RCVD:第二次握手发生阶段，和上一步对应，这里是服务器接收到了客户端的SYN,服务器由LISTEN进入SYN_RCVD状态，同时回应一个ACK，再发送SYN+ACK给客户端。**此外还有一种情况，即客户端在发送SYN的同时服务器也发送了SYN报文，两端同时发起连接请求，则客户端接收到SYN会从SYN_SENT转为SYN_RCVD。**\
· ESTABLISHED:第三次握手发生阶段，客户端接收到服务器端的 ACK 包（ACK，SYN）之后，也会发送一个 ACK 确认包，客户端进入 ESTABLISHED 状态，表明客户端这边已经准备好，但TCP 需要两端都准备好才可以进行数据传输。服务器端收到客户端的 ACK 之后会从 SYN_RCVD 状态转移到 ESTABLISHED 状态，表明服务器端也准备好进行数据传输了。这样客户端和服务器端都是 ESTABLISHED 状态，就可以进行后面的数据传输了。所以 ESTABLISHED 也可以说是一个数据传送状态。
· FIN_AIT_1:第一次挥手，主动关闭的一方（以客户端为例），终止连接时发送FIN给对方，然后等对方返回ACK.\
· CLOSE_WAIT:接收到FIN报文后，被动关闭的一方进入此状态，同时发送ACK. 之所以叫CLOSE_WAIT，可以理解为被动关闭的一方正在等待上层应用程序发出关闭连接指令。即等待发送FIN\
· FIN_WAIT_2: 主动方接收到服务端发送来的ACK，进入此状态\
· LAST_ACK: 被动方发起关闭请求，由CLOSE_WAIT进入LAST_ACK，等待客户端响应的ACK\
· CLOSING: **两边同时发起关闭请求，主动方发送FIN,等待被动方发送ACK,同时被动方也发送FIN,主动方接收到FIN后返回ACK响应，进入CLOSING状态**.\
· TIME_WAIT：有三个状态可以进入TIME_WAIT

i. 由CLOSING进入： 同时发起关闭的情况下，当主动端接收到ACK，进入此状态。客户端发起关闭请求，发送FIN后等待服务器回应ACK，但此时服务器也发送了FIN,并在回应ACK到达前被客户端接收。\
ii.由FIN_WAIT_1进入：发起关闭后，先发送了FIN,等待ACK的时候，服务器也发送了FIN,客户端先收到了响应ACK,又收到了FIN,然后发送ACK给服务器，进入TIME_WAIT状态\
iii.由FIN_WAIT2进入: 主动方在完成自身发起的主动关闭请求后，接收到对方发送的FIN,然后回应ACK.
