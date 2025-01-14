﻿﻿#DNS 解析流程
DNS( Domain Name System)是“域名系统”的英文缩写，用于 TCP/IP 网络，将主机名和域名转换为 IP 地址。

DNS 主要基于 UDP 运输层协议，而不是 TCP。DNS 是应用层协议，client 端（一般指浏览器）构建 DNS 查询请求，依次被传输层，网络层，数据链路层等封装传送到达 DNS 服务器端，最终 client 端接收到 DNS 响应消息

###具体过程：

1. 在浏览器中输入 www . qq .com 域名，先检查本地的 hosts 文件是否有这个网址映射关系，如果有，就先调用这个 IP 地址映射，完成域名解析。
2. 如果 hosts 里没有，则查找本地 DNS 解析器缓存，如果有，直接返回，完成域名解析。
3. 如果本地 DNS 解析器缓存没有，会找 TCP/ip 参数中设置的首选 DNS 服务器（叫它本地 DNS 服务器）查询，如果包含在本地配置区域资源中，则返回解析结果给客户机，完成域名解析，此解析具有权威性。
4. 如果不由本地 DNS 服务器区域解析，但该服务器已缓存了此网址映射关系，则调用这个 IP 地址映射，完成域名解析，此解析不具有权威性。
5. 如果本地 DNS 服务器本地区域文件与缓存解析都失效，则根据本地 DNS 服务器的设置（是否设置转发器）进行查询，如果未用转发模式，本地 DNS 就把请求发至根 DNS 服务器，根 DNS 服务器收到请求后会判断这个域名(.com)是谁来授权管理，并会返回一个负责该顶级域名服务器的一个 IP。本地 DNS 服务器收到 IP 信息后，将会联系负责.com 域的这台服务器。这台负责.com 域的服务器收到请求后，如果自己无法解析，它就会找一个管理.com 域的下一级 DNS 服务器地址(http://qq.com) 给本地 DNS 服务器。当本地 DNS 服务器收到这个地址后，就会找 http://qq.com 域服务器，重复上面的动作，进行查询，直至找到 www . qq .com 主机。
6. 如果用的是转发模式，此 DNS 服务器就会把请求转发至上一级 DNS 服务器，由上一级服务器进行解析，上一级服务器如果不能解析，或找根 DNS 或把转请求转至上上级，以此循环。不管是本地 DNS 服务器用是是转发，还是根提示，最后都是把结果返回给本地 DNS 服务器，由此 DNS 服务器再返回给客户机。

---

1、解析 URL：首先会对 URL 进行解析，分析所需要使用的传输协议和请求的资源的路径。如果输入的 URL 中的协议或者主机名不合法，将会把地址栏中输入的内容传递给搜索引擎。如果没有问题，浏览器会检查 URL 中是否出现了非法字符，如果存在非法字符，则对非法字符进行转义后再进行下一过程。
2、缓存判断：浏览器会判断所请求的资源是否在缓存里，如果请求的资源在缓存里并且没有失效，那么就直接使用，否则向服务器发起新的请求。
3、DNS 解析： 下一步首先需要获取的是输入的 URL 中的域名的 IP 地址，首先会判断本地是否有该域名的 IP 地址的缓存，如果有则使用，如果没有则向本地 DNS 服务器发起请求。本地 DNS 服务器也会先检查是否存在缓存，如果没有就会先向根域名服务器发起请求，获得负责的顶级域名服务器的地址后，再向顶级域名服务器请求，然后获得负责的权威域名服务器的地址后，再向权威域名服务器发起请求，最终获得域名的 IP 地址后，本地 DNS 服务器再将这个 IP 地址返回给请求的用户。用户向本地 DNS 服务器发起请求属于递归请求，本地 DNS 服务器向各级域名服务器发起请求属于迭代请求。
4、获取 MAC 地址： 当浏览器得到 IP 地址后，数据传输还需要知道目的主机 MAC 地址，因为应用层下发数据给传输层，TCP 协议会指定源端口号和目的端口号，然后下发给网络层。网络层会将本机地址作为源地址，获取的 IP 地址作为目的地址。然后将下发给数据链路层，数据链路层的发送需要加入通信双方的 MAC 地址，本机的 MAC 地址作为源 MAC 地址，目的 MAC 地址需要分情况处理。通过将 IP 地址与本机的子网掩码相与，可以判断是否与请求主机在同一个子网里，如果在同一个子网里，可以使用 APR 协议获取到目的主机的 MAC 地址，如果不在一个子网里，那么请求应该转发给网关，由它代为转发，此时同样可以通过 ARP 协议来获取网关的 MAC 地址，此时目的主机的 MAC 地址应该为网关的地址。
5、TCP 三次握手： 下面是 TCP 建立连接的三次握手的过程，首先客户端向服务器发送一个 SYN 连接请求报文段和一个随机序号，服务端接收到请求后向客户端发送一个 SYN ACK 报文段，确认连接请求，并且也向客户端发送一个随机序号。客户端接收服务器的确认应答后，进入连接建立的状态，同时向服务器也发送一个 ACK 确认报文段，服务器端接收到确认后，也进入连接建立状态，此时双方的连接就建立起来了。
6、HTTPS 握手： 如果使用的是 HTTPS 协议，在通信前还存在 TLS 的一个四次握手的过程。首先由客户端向服务器端发送使用的协议的版本号、一个随机数和可以使用的加密方法。服务器端收到后，确认加密的方法，也向客户端发送一个随机数和自己的数字证书。客户端收到后，首先检查数字证书是否有效，如果有效，则再生成一个随机数，并使用证书中的公钥对随机数加密，然后发送给服务器端，并且还会提供一个前面所有内容的 hash 值供服务器端检验。服务器端接收后，使用自己的私钥对数据解密，同时向客户端发送一个前面所有内容的 hash 值供客户端检验。这个时候双方都有了三个随机数，按照之前所约定的加密方法，使用这三个随机数生成一把秘钥，以后双方通信前，就使用这个秘钥对数据进行加密后再传输。
7、返回数据： 当页面请求发送到服务器端后，服务器端会返回一个 html 文件作为响应，浏览器接收到响应后，开始对 html 文件进行解析，开始页面的渲染过程。
8、页面渲染： 浏览器首先会根据 html 文件构建 DOM 树，根据解析到的 css 文件构建 CSSOM 树，如果遇到 script 标签，则判端是否含有 defer 或者 async 属性，要不然 script 的加载和执行会造成页面的渲染的阻塞。当 DOM 树和 CSSOM 树建立好后，根据它们来构建渲染树。渲染树构建好后，会根据渲染树来进行布局。布局完成后，最后使用浏览器的 UI 接口对页面进行绘制。这个时候整个页面就显示出来了。
9、TCP 四次挥手： 最后一步是 TCP 断开连接的四次挥手过程。若客户端认为数据发送完成，则它需要向服务端发送连接释放请求。服务端收到连接释放请求后，会告诉应用层要释放 TCP 链接。然后会发送 ACK 包，并进入 CLOSE_WAIT 状态，此时表明客户端到服务端的连接已经释放，不再接收客户端发的数据了。但是因为 TCP 连接是双向的，所以服务端仍旧可以发送数据给客户端。服务端如果此时还有没发完的数据会继续发送，完毕后会向客户端发送连接释放请求，然后服务端便进入 LAST-ACK 状态。客户端收到释放请求后，向服务端发送确认应答，此时客户端进入 TIME-WAIT 状态。该状态会持续 2MSL（最大段生存期，指报文段在网络中生存的时间，超时会被抛弃） 时间，若该时间段内没有服务端的重发请求的话，就进入 CLOSED 状态。当服务端收到确认应答后，也便进入 CLOSED 状态。

---

#UDP 用户数据报协议（User Datagram Protocol）
UDP 是一种无连接的协议。  
###特点：

1.  面向无连接: UDP 不需要在发送数据前建立连接，想发数据就可以开始发送了。
2.  有单播，多播，广播的功能: UDP 不止支持一对一的传输方式，同样支持一对多，多对多，多对一的方式，也就是说 UDP 提供了单播，多播，广播的功能。
3.  面向报文: 只是数据报文的搬运工，不会对数据报文进行任何拆分和拼接操作。UDP 只会给数据增加一个 UDP 头标识下是 UDP 协议，然后就传递给网络层了。
4.  不可靠性：收到什么数据就传递什么数据，不备份数据，不关心对方是否已经正确接收到数据。没有拥塞控制，一直会以恒定的速度发送数据，网络条件不好的情况下可能会导致丢包。
5.  开销小：头部开销小，只有八字节，相比 TCP 的至少二十字节要少得多。不需要建立连接，相比 TCP 省去三次握手以及四次分手的包交换的开销。  
    ###适用场景
    UDP 适用于对丢包要求比较低，但速度必须快的一些场景，即时通信，在线视频，网络语音电话等等。  
    TCP 正相反，适用于 web 请求，文件传输，邮件发送等场景。

#TCP:传输控制协议(Transmission Control Protocol) ###建立连接：

1. 客户端向服务端发送连接请求报文段（SYN）; 客户端进入 SYN-SENT 状态。
2. 服务端收到连接请求报文段后，发送一个同意连接应答(SYN+ACK)，进入 SYN-RECEIVED 状态
3. 客户端收到连接同意的应答后，还要向服务端发送一个确认报文(ACK)，进入 ESTABLISHED 状态；服务端收到这个应答后也进入 ESTABLISHED 状态，此时连接建立成功。  
   ###为什么建立连接需要第三次握手？
   防止出现过期失效的连接请求报文（第一次握手报文）被服务端接收的情况，从而产生错误。
   为了防止已经失效的连接请求报文段突然又传到服务端，因而产生错误

---

###断开连接时客户端突然挂掉了怎么办？
正常连接时，客户端突然挂掉了，如果没有措施处理这种情况，那么就会出现客户端和服务器端出现长时期的空闲。

解决办法是在服务器端设置保活计时器，每当服务器收到客户端的消息，就将计时器复位。超时时间通常设置为 2 小时。

若服务器超过 2 小时没收到客户的信息，他就发送探测报文段。若发送了 10 个探测报文段，每一个相隔 75 秒，还没有响应就认为客户端出了故障，因而终止该连接。

###断开链接：

1. 客户端 C 向 服务端 S 发送连接释放请求(FIN)，C 进入 FIN-WAIT-1 状态。
2. S 收到连接释放请求后，会告诉应用层要释放 TCP 链接。然后会发送 ACK 包， 并进入 CLOSE_WAIT 状态，C 收到 ACK, 进入 FIN-WAIT-2 状态。C 到 S 的连接已经释放，不再接收 C 发的数据。S 可以继续向 C 发送数据。
3. S 要向 C 发送的数据发送后, 向 C 发送连接释放请求(FIN)，S 进入 LAST-ACK 状态。
4. C 收到释放请求后，向 S 发送确认应答(ACK)。 C 进入 TIME-WAIT 状态, 持续 2MSL（报文段最大生存时间，指报文段在网络中生存的时间，超时会被抛弃） 时间。若该时间段内没有 S 的重发请求的话，就进入 CLOSED 状态。当 S 收到确认应答后，也便进入 CLOSED 状态。  
   ###客户端第四次挥手后为什么要等待 2MSL 时间才转换为 close 状态？
   第 4 次挥手， C 向 S 发送 ACK，但是 C 无法知道 ACK 是否已经到达 S , 于是 C 开始等待，如果 S 未收到 ACK， 会向 C 重发第 3 次挥手的 FIN 包，所以 C 可以等待是否收到重发的 FIN 来判断 S 是否收到了 ACK，这个等待时间至少是:S 的 timeout ＋ FIN 的传输时间,为了保证可靠,采用更加保守的等待时间 2MSL。

###TCP 特点：

1. 面向连接，是指发送数据之前必须在两端建立连接。
2. 仅支持单播传输，每条 TCP 传输连接只能有两个端点，只能进行点对点的数据传输。
3. 面向字节流，TCP 不像 UDP 一样那样一个个报文独立地传输，而是在不保留报文边界的情况下以字节流方式进行传输。
4. 可靠传输，TCP 为了保证报文传输的可靠，就给每个包一个序号，同时序号也保证了传送到接收端实体的包的按序接收。然后接收端实体对已成功收到的字节发回一个相应的确认(ACK)；如果发送端实体在合理的往返时延(RTT)内未收到确认，那么对应的数据（假设丢失了）将会被重传。
5. 拥塞控制：当网络出现拥塞的时候，TCP 通过慢启动的机制和控制滑动窗口(sliding window)大小，减小向网络注入数据的速率和数量，保证流量传输不超过设备的负载能力，缓解拥塞。
6. 全双工通信，TCP 允许通信双方的应用程序在任何时候都能发送数据。

###TCP 协议如何保证数据传输可靠性？

1. 校验和：发送方和接收方都计算校验和，如果接收方比对校验和与发送方不一致，那么数据一定传输有误。
2. 序列号：TCP 传输时将每个数据都进行了编号，这就是序列号。
3. 确认应答：每次接收方收到数据后，都会对传输方进行确认应答。也就是发送 ACK 报文。这个 ACK 报文当中带有对应的确认序列号，告诉发送方，接收到了哪些数据，下一次的数据从哪里发。防止丢包。
4. 超时重传：发送方在发送完数据后等待一个时间，时间到达没有接收到 ACK 报文，那么对刚才发送的数据进行重新发送。
5. 连接管理：连接管理就是三次握手与四次挥手的过程。保证可靠的连接，是保证可靠性的前提。
6. 流量控制：通过滑动窗口控制。
7. 拥塞控制：TCP 传输的过程中，发送端开始发送数据的时候，如果刚开始就发送大量的数据，那么就可能造成一些问题。网络可能在开始的时候就很拥堵，如果给网络中在扔出大量数据，那么这个拥堵就会加剧。拥堵的加剧就会产生大量的丢包，就对大量的超时重传，严重影响传输。所以 TCP 引入了慢启动的机制，在开始发送数据时，先发送少量的数据探路。探清当前的网络状态如何，再决定多大的速度进行传输。这时候就引入一个叫做拥塞窗口的概念。发送刚开始定义拥塞窗口为 1，每次收到 ACK 应答，拥塞窗口加 1。在发送数据之前，首先将拥塞窗口与接收端反馈的窗口大小比对，取较小的值作为实际发送的窗口。拥塞窗口的增长是指数级别的。慢启动的机制只是说明在开始的时候发送的少，发送的慢，但是增长的速度是非常快的。为了控制拥塞窗口的增长，不能使拥塞窗口单纯的加倍，设置一个拥塞窗口的阈值，当拥塞窗口大小超过阈值时，不能再按照指数来增长，而是线性的增长。在慢启动开始的时候，慢启动的阈值等于窗口的最大值，一旦造成网络拥塞，发生超时重传时，慢启动的阈值会为原来的一半（这里的原来指的是发生网络拥塞时拥塞窗口的大小），同时拥塞窗口重置为 1。

###固定窗口与滑动窗口
固定窗口：每次只能发送的数据包数固定。  
滑动窗口：滑动窗口通俗来讲就是一种流量控制技术。它本质上是描述接收方的 TCP 数据报缓冲区大小的数据，发送方根据这个数据来计算自己最多能发送多长的数据，如果发送方收到接收方的窗口大小为 0 的 TCP 数据报，那么发送方将停止发送数据，等到接收方发送窗口大小不为 0 的数据报的到来；滑动窗口的最大优势为不需要对每个数据包进行确认，而是可以进行累积确认。

---

#http1.1/2/3 差异
###http1.1：

1. 增加了 keep-alive 长连接；
2. 管线化发送请求，不用等一个请求回来再发另外一个请求；
3. 提供了可以指定发送长度的数据块,请求头引入了 range 首部

###http2:

1. 多路复用：HTTP/2 使用多路复用技术，使用一个 TCP 连接并发处理多个请求，不但节约了开销而且可处理请求的数量也比 HTTP 1.1 大了很多.在 HTTP 1.x 中，如果客户端要想发起多个并行请求以提升性能，则必须使 用多个 TCP 连接。这种模型也会导致队首阻塞，从而造成底层 TCP 连接的效率低下。
2. 二进制数据传输：HTTP/2 采用二进制格式传输数据，而非 HTTP 1.x 的文本格式，二进制协议解析起来更高效
3. 头部压缩：HTTP 1.1 不支持 Header 数据压缩，HTTP/2 使用 HPACK 算法对 Header 的数据进行压缩，使得数据传输更快
4. 服务器推送（Server Push）：当对支持 HTTP/2 的服务器请求数据的时候，服务器会顺便把一些客户端需要的资源一起推送到服务器，这种方式适用于加载静态资源，节约带宽

###http3:

1. 运行在 QUIC 之上的 HTTP 协议被称为 HTTP/3(HTTP-over-QUIC)，QUIC 协议(Quick UDP Internet Connection)基于 UDP，正是看中了 UDP 的速度与效率。同时 QUIC 也整合了 TCP、TLS 和 HTTP/2 的优 点，并加以优化
2. 减少了握手的延迟
3. 多路复用，并且没有 TCP 的阻塞问题，TCP 连接中的任何中断（packet loss）会阻塞所有数据流（Head of line blocking）。因为 HTTP/3 基于 UDP 协议，所以如果数据包丢失，只会中断一个数据流，而不会中断所有流。

###HTTP2 多路复用原理
HTTP 1.1 基于**串行**文件传输数据，服务器处理这些请求必须是有序的，存在队头阻塞，这也是 http1.1 管线化与 http2 多路复用的本质差异  
而 HTTP/2 引入二进制数据帧和流的概念，其中帧对数据进行顺序标识，这样浏览器收到数据之后，就可以按照序列对数据进行合并，而不会出现合并后数据错乱的情况。同样是因为有了序列，服务器就可以**并行**的传输数据。  
HTTP/2 对同一域名下所有请求都是基于流，也就是说同一域名不管访问多少文件，也只建立一路连接。

---

#https 详细流程
HTTPS 协议 = HTTP 协议 + SSL/TLS 协议
###SSL
全称是 Secure Sockets Layer，即安全套接层协议，是为网络通信提供安全及数据完整性的一种安全协议。
###TLS
全称是 Transport Layer Security，即安全传输层协议，它建立在 SSL 3.0 协议规范之上，是 SSL 3.0 的后续版本，与 SSL 主要差别是它们所支持的加密算法不同  
###流程：

1. 客户端向服务器发起 HTTPS 请求
2. 服务器端有一个密钥对，即公钥和私钥，是用来进行非对称加密使用的
3. 服务器将证书（包含公钥）发送给客户端。
4. 客户端收到证书之后，验证证书合法性。客户端会生成一个随机值，用于后面的对称加密，称之为客户端密钥，然后用服务器的公钥对客户端密钥进行非对称加密。
5. 客户端会发起 HTTPS 中的第二个 HTTP 请求
6. 服务器接收到客户端发来的密文之后，会用自己的私钥对其进行非对称解密，解密之后的明文就是客户端密钥，然后用客户端密钥对数据进行对称加密，这样数据就变成了密文。
7. 然后服务器将加密后的密文发送给客户端。
8. 客户端收到服务器发送来的密文，用客户端密钥对其进行对称解密，得到服务器发送的数据。这样 HTTPS 中的第二个 HTTP 请求结束，整个 HTTPS 传输完成。 ###为什么最后会使用对称加密，而不是全过程使用非对称加密？
   HTTPS 为了兼顾安全与效率，同时使用了对称加密和非对称加密。非对称加密加密解密算法效率较低，不适合客户端和服务器端这样高频率的通信过程，一般情况下，对称加密比非对称加密慢上 10-1000 倍。

---

#如何判断 http 请求已发送完毕 ###服务端
Content-Length 是一个实体消息首部，用来指明发送给接收方的消息主体的大小。 ###客户端
Conent-Length（静态资源） 和 Transfer-Encoding（动态资源）  
传输动态资源时，服务器是不可能预先知道内容大小，这时使用 Transfer-Encoding：chunk 模式来传输数据。  
chunk 编码将数据分成一块一块的发生。Chunked 编码将使用若干个 Chunk 串连而成，**由一个标明长度为 0 的 chunk 标示结束**。

---

#请求在客户端报 504gateway timeout 时，在服务端看到的状态码是什么
不知道

---

#请求在客户端报 413 是什么错误，怎么解决
413 的错误码是表示：请求实体太大。
修改服务器（nginx 或 apache）的配置，修改【客户端请求体最大值】和【请求体缓存区大小】

---

#浏览器请求缓存机制

1. 浏览器每次发起请求，都会先在浏览器缓存中查找该请求的结果以及缓存标识
2. 浏览器每次拿到返回的请求结果，会根据响应报文中 HTTP 头的缓存标识，决定是否将该结果和缓存标识存入浏览器缓存中
3. 浏览器在请求某一资源时，会先获取该资源缓存的 header 信息，判断是否命中强缓存；若命中直接从本地缓存中获取；本次请求不会与服务器进行通信；状态码 200。控制强制缓存的字段分别是 Expires 和 Cache-Control，其中 Cache-Conctrol 的优先级比 Expires 高。
4. 若没有命中强缓存，浏览器会发送请求到服务器，请求会携带第一次请求返回的有关缓存的 header 字段信息（Last-Modified/If-Modified-Since 和 Etag/If-None-Match，后者优先级更高），由服务器根据请求中的相关 header 信息来比对结果是否协商缓存命中；若命中，则服务器返回新的响应 header 信息更新缓存中的对应 header 信息，但是并不返回资源内容，它会告知浏览器可以直接从缓存获取； 状态码 304。否则返回最新的资源内容。 ###已缓存未过期的文件，如何强制刷新？
   ctrl+F5, 更新请求 url 中的 query。

---

#301 和 302 表现在客户端的区别，浏览器对 301 和 302 的处理有什么不同
301 是永久重定向，而 302 是临时重定向  
301 重定向是永久的重定向，搜索引擎在抓取新的内容的同时也将旧的网址替换为了重定向之后的网址。  
302 重定向只是暂时的重定向，搜索引擎会抓取新的内容而保留旧的地址，因为服务器返回 302，所以，搜索搜索引擎认为新的网址是暂时的。

---

#如何区分简单请求和复杂请求，options 请求在什么场景下会出现，其作用是什么 ###简单请求：

1. 请求方法：GET，POST，HEAD
2. 不能自定义请求头 header, 以下头部信息除外：Accept，Accept-Language，Content-Language，Content-Type
3. Content-Type 的值仅限于下列三者之一：text/plain，multipart/form-data，application/x-www-form-urlencoded
4. 关于 XMLHttpRequestUpload：请求中的任意 XMLHttpRequestUpload 对象均没有注册任何事件监听器，XMLHttpRequestUpload 对象可以使用 XMLHttpRequest.upload 属性访问
5. 请求中没有使用 ReadableStream 对象
   除简单请求外，其他均为复杂请求。  
   CORS 跨域请求中，浏览器会先使用 OPTIONS 方法发起一个预检请求到服务器，以获知服务器是否允许该实际请求。"预检请求“的使用，可以避免跨域请求对服务器的用户数据产生未预期的影响。

---

#TCP 的半连接攻击和全连接攻击 ###半连接:
建立连接时，服务器在向客户端发送第 2 次握手包（SYN+ACK）后，在收到第 3 次握手包前，处理半连接状态。 ###半连接攻击：
恶意攻击者故意大量发送不断发送的伪造的 SYN 报文（第一次握手），那么服务器就会分配大量注定无用的资源，并且服务器保存半连接的队列是有长度限制的，如果当服务器收到大量攻击报文的时候，它就不能接收正常的连接了，这就是大名鼎鼎的 SYN-Flood 攻击的原理，它是一种典型的 DoS 攻击。  
半连接攻击耗尽的是全局的内存，服务器用资源保存每次请求的关键信息，用来指导后续的报文收发。 ###半连接攻击解决方案：

1. SYN cookies 算法：大致原理是将第一次握手的 SYN 包中的信息通过算法装在 ACK 包中，第 2 次握手发回客户端，然后第 3 次握手收到 ACK 后将最初的 SYN 信息解出来，而不是放在内存中。SYN Cookies 算法的缺点：增加了密码学运算，不要小瞧这个缺点，如果密码学运算比较复杂，在 DoS 大量攻击的情况下，可能会打爆服务器的 cpu。
2. SYN 网关：防火墙收到客户端的 SYN 包时，直接转发给服务器，防火墙收到服务器的 SYN+ACK 后，一方面将 SYN+ACK 转发给客户端，另一个方面以客户端的名义给服务器回送一个 ACK 包，完成 TCP 的三次握手，让服务器端从半连接状态变成连接状态。当客户端真正的 ACK 到达时，如果有携带数据，那么就转发给服务器，否则就丢弃该包。由于服务器承受连接状态的能力比半连接状态要高很多，所以这种办法能够有效的减轻对服务器的攻击。
3. SYN 中继：防火墙收到客户端的 SYN 包之后，并不是向服务器转发而是记录该状态消息，再主动给客户端发送 SYN+ACK 包，如果收到客户端的 ACK 包，说明是正常访问，由防火墙向服务器发送 SYN 包并且完成三次握手。这种方式用防火墙做为代理来实现客户端和服务端的连接，可以做到完全过滤不可用连接发往服务器。 ###全连接攻击：
   所谓的全连接攻击说的就是客户端仅仅“连接”到服务器，然后再也不发送任何数据，直到服务器超时后处理或者耗尽服务器的处理进程。  
   为何不发送任何数据呢？因为一旦发送了数据，服务器检测到数据不合法后就可能断开此次连接，如果不发送数据的话，很多服务器只能阻塞在 recv 或者 read 调用上。  
   很多的服务器架构都是每连接一个进程的方式，这种服务器更容易受到全连接攻击，即使是进程池/线程池的方式也不例外，症状就是服务器主机建立了大量的客户端处理进程，然后阻塞在 recv/read 而无所事事，大量的这种连接会耗尽服务器主机的处理进程。  
   全连接攻击耗尽的是主机的处理进程和连接数量，因此可以限制处理进程的创建或者限制预创建的进程池进程的分配。 ###解决全连接攻击
   可以通过不为全连接分配进程处理的方式来防范全连接攻击，具体的情况是当收到数据之后，在为其分配一个处理线程。  
   具体的处理方式在 accept 返回之前是不分配处理线程的。直到接收相关的数据之后才为之提供一个处理过程。

---

#服务器推送技术

###WebSocket

1. 建立在 TCP 协议之上，握手阶段（浏览器向服务器传送 WebSocket 支持的版本号等信息）采用 HTTP 协议。
2. 数据格式比较轻量，性能开销小，通信高效。
3. 可以发送文本，也可以发送二进制数据。
4. 没有同源限制，客户端可以与任意服务器通信。
5. 协议标识符是 ws（如果加密，则为 wss）

```
const websocket = new WebSocket("ws://localhost:8080/websocket");
//接收消息
websocket.onmessage = function (event) {
  console.log(event.data);
}
//发送消息
websocket.send(message);
```

###SSE(Server-Sent Events)

1. eventsource 其实是单向通信，而 websocket 是全双工通信。
2. SSE 是使用 HTTP 传输的，这意味着我们不需要一个特殊的协议和服务器去处理。
3. SSE 限定是一个长连接，MIME 类型要设置为 text/event-stream。

```
var evtSource = new EventSource('/get/data');
evtSource.onmessage = function(e) {
  console.log(e.data);
}
```

---

#WebSocket 与 Socket 的关系
两者没有关系  
WebSocket 则是一个典型的应用层协议  
Socket 其实并不是一个协议，是位于应用层和传输控制层之间的一组接口。
Socket 它把复杂的 TCP/IP 协议族隐藏在 Socket 接口后面，对用户来说，一组简单的接口就是全部，让 Socket 去组织数据，以符合指定的协议。
