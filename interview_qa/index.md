# Interview_qa


### 计算机网络{#计算机网络}

> TCP UDP 区别

```
都是传输协议
TCP/IP
Transmission Control Protocol / Internet Protocol
TCP/IP互联网各类协议总称 TCP UDP IP FTP HTTP ICMP SMTP等
协议如图
OSI七层模型 应用层 表示层 会话层 传输层 网络层 数据链路层 物理层
UPD(User Datagram Protocol)
UDP协议全称是用户数据报协议，在网络中它与TCP协议一样用于处理数据包，是一种无连接的协议
特点
1.面向无连接 不进行三次握手 只有协议+内容 不对报文拆分拼接
2.有单播,多播,广播 一对一 一对多 多对多  多对一
3.面向报文 自己选择报文大小
4.不可靠 体现在无连接上
5.传输头部小,速度更高效
```

![OSI七层模型](https://image.fundebug.com/2019-03-21-01.png)

> TCP 为什么比 UDP 稳定

```
1.TCP链接前3次握手,并且会确认是否送达
2.UDP是无链接,不管数据是否送达,都会继续发送
3.tcp协议在确认客户端接收到完整的信息之后才会删除服务端操作系统中的缓存，否则就会继续发
```

> 三次握手 四次挥手

```
（1）序号（sequence number）：Seq序号，占32位，用来标识从TCP源端向目的端发送的字节流，发起方发送数据时对此进行标记。

（2）确认号（acknowledgement number）：Ack序号，占32位，只有ACK标志位为1时，确认序号字段才有效，Ack=Seq+1。

（3）标志位（Flags）：共6个，即URG、ACK、PSH、RST、SYN、FIN等。具体含义如下：

URG：紧急指针（urgent pointer）有效。
ACK：确认序号有效。
PSH：接收方应该尽快将这个报文交给应用层。
RST：重置连接。
SYN：发起一个新连接。
FIN：释放一个连接。

需要注意的是：

不要将确认序号Ack与标志位中的ACK搞混了。确认方Ack=发起方Seq+1，两端配对。

第一次握手

客户端向服务端发送连接请求报文段。该报文段中包含自身的数据通讯初始序号。请求发送后，客户端便进入 SYN-SENT 状态。

第二次握手

服务端收到连接请求报文段后，如果同意连接，则会发送一个应答，该应答中也会包含自身的数据通讯初始序号，发送完成后便进入 SYN-RECEIVED 状态。

第三次握手

当客户端收到连接同意的应答后，还要向服务端发送一个确认报文。客户端发完这个报文段后便进入 ESTABLISHED 状态，服务端收到这个应答后也进入 ESTABLISHED 状态，此时连接建立成功。

这里可能大家会有个疑惑：为什么 TCP 建立连接需要三次握手，而不是两次？这是因为这是为了防止出现失效的连接请求报文段被服务端接收的情况，从而产生错误。

为什么要进行第三次握手
为了防止服务器端开启一些无用的连接增加服务器开销以及防止已失效的连接请求报文段突然又传送到了服务端，因而产生错误。

为什么“握手”是三次，“挥手”却要四次？

TCP建立连接时之所以只需要"三次握手"，是因为在第二次"握手"过程中，服务器端发送给客户端的TCP报文是以SYN与ACK作为标志位的。SYN是请求连接标志，表示服务器端同意建立连接；ACK是确认报文，表示告诉客户端，服务器端收到了它的请求报文。

即SYN建立连接报文与ACK确认接收报文是在同一次"握手"当中传输的，所以"三次握手"不多也不少，正好让双方明确彼此信息互通。

为什么客户端在TIME-WAIT阶段要等2MSL?

为的是确认服务器端是否收到客户端发出的ACK确认报文

当客户端发出最后的ACK确认报文时，并不能确定服务器端能够收到该段报文。所以客户端在发送完ACK确认报文之后，会设置一个时长为2MSL的计时器。MSL指的是Maximum Segment Lifetime：一段TCP报文在传输过程中的最大生命周期。2MSL即是服务器端发出为FIN报文和客户端发出的ACK确认报文所能保持有效的最大时长。

服务器端在1MSL内没有收到客户端发出的ACK确认报文，就会再次向客户端发出FIN报文；

如果客户端在2MSL内，再次收到了来自服务器端的FIN报文，说明服务器端由于各种原因没有接收到客户端发出的ACK确认报文。客户端再次向服务器端发出ACK确认报文，计时器重置，重新开始2MSL的计时；
否则客户端在2MSL内没有再次收到来自服务器端的FIN报文，说明服务器端正常接收了ACK确认报文，客户端可以进入CLOSED阶段，完成“四次挥手”。

所以，客户端要经历时长为2SML的TIME-WAIT阶段；这也是为什么客户端比服务器端晚进入CLOSED阶段的原因

挥手之前主动释放连接的客户端结束ESTABLISHED阶段。随后开始“四次挥手”：

（1）首先客户端想要释放连接，向服务器端发送一段TCP报文，其中：

标记位为FIN，表示“请求释放连接“；
序号为Seq=U；
随后客户端进入FIN-WAIT-1阶段，即半关闭阶段。并且停止在客户端到服务器端方向上发送数据，但是客户端仍然能接收从服务器端传输过来的数据。
注意：这里不发送的是正常连接时传输的数据(非确认报文)，而不是一切数据，所以客户端仍然能发送ACK确认报文。

（2）服务器端接收到从客户端发出的TCP报文之后，确认了客户端想要释放连接，随后服务器端结束ESTABLISHED阶段，进入CLOSE-WAIT阶段（半关闭状态）并返回一段TCP报文，其中：

标记位为ACK，表示“接收到客户端发送的释放连接的请求”；
序号为Seq=V；
确认号为Ack=U+1，表示是在收到客户端报文的基础上，将其序号Seq值加1作为本段报文确认号Ack的值；
随后服务器端开始准备释放服务器端到客户端方向上的连接。
客户端收到从服务器端发出的TCP报文之后，确认了服务器收到了客户端发出的释放连接请求，随后客户端结束FIN-WAIT-1阶段，进入FIN-WAIT-2阶段

前"两次挥手"既让服务器端知道了客户端想要释放连接，也让客户端知道了服务器端了解了自己想要释放连接的请求。于是，可以确认关闭客户端到服务器端方向上的连接了

（3）服务器端自从发出ACK确认报文之后，经过CLOSED-WAIT阶段，做好了释放服务器端到客户端方向上的连接准备，再次向客户端发出一段TCP报文，其中：

标记位为FIN，ACK，表示“已经准备好释放连接了”。注意：这里的ACK并不是确认收到服务器端报文的确认报文。
序号为Seq=W；
确认号为Ack=U+1；表示是在收到客户端报文的基础上，将其序号Seq值加1作为本段报文确认号Ack的值。
随后服务器端结束CLOSE-WAIT阶段，进入LAST-ACK阶段。并且停止在服务器端到客户端的方向上发送数据，但是服务器端仍然能够接收从客户端传输过来的数据。

（4）客户端收到从服务器端发出的TCP报文，确认了服务器端已做好释放连接的准备，结束FIN-WAIT-2阶段，进入TIME-WAIT阶段，并向服务器端发送一段报文，其中：

标记位为ACK，表示“接收到服务器准备好释放连接的信号”。
序号为Seq=U+1；表示是在收到了服务器端报文的基础上，将其确认号Ack值作为本段报文序号的值。
确认号为Ack=W+1；表示是在收到了服务器端报文的基础上，将其序号Seq值作为本段报文确认号的值。
随后客户端开始在TIME-WAIT阶段等待2MSL

为什么要客户端要等待2MSL呢？见后文。

服务器端收到从客户端发出的TCP报文之后结束LAST-ACK阶段，进入CLOSED阶段。由此正式确认关闭服务器端到客户端方向上的连接。

客户端等待完2MSL之后，结束TIME-WAIT阶段，进入CLOSED阶段，由此完成“四次挥手”。

后“两次挥手”既让客户端知道了服务器端准备好释放连接了，也让服务器端知道了客户端了解了自己准备好释放连接了。于是，可以确认关闭服务器端到客户端方向上的连接了，由此完成“四次挥手”。

与“三次挥手”一样，在客户端与服务器端传输的TCP报文中，双方的确认号Ack和序号Seq的值，都是在彼此Ack和Seq值的基础上进行计算的，这样做保证了TCP报文传输的连贯性，一旦出现某一方发出的TCP报文丢失，便无法继续"挥手"，以此确保了"四次挥手"的顺利完成。
```

![三次握手](https://image.fundebug.com/2019-03-21-04.png)
![四次挥手](https://pics5.baidu.com/feed/48540923dd54564e5260495ce0006487d0584fb6.jpeg?token=c3a743af38e25ff66deb6a07891be58e&s=C584FC1A71CFF4EE1A75A45203007073)

> http https 区别 加密过程

```
HTTPS和HTTP的区别主要如下：

　　1、https协议需要到ca申请证书，一般免费证书较少，因而需要一定费用。

　　2、http是超文本传输协议，信息是明文传输，https则是具有安全性的ssl加密传输协议。

　　3、http和https使用的是完全不同的连接方式，用的端口也不一样，前者是80，后者是443。

　　4、http的连接很简单，是无状态的；HTTPS协议是由SSL+HTTP协议构建的可进行加密传输、身份认
证的网络协议，比http协议安全。
```

> http 请求头部 断点重传 cache

```
请求头用range
Range:(unit=first byte pos)-[last byte pos]
Range: bytes=0-499 表示第 0-499 字节范围的内容
Range: bytes=500-999 表示第 500-999 字节范围的内容
Range: bytes=-500 表示最后 500 字节的内容
Range: bytes=500- 表示从第 500 字节开始到文件结束部分的内容
Range: bytes=0-0,-1 表示第一个和最后一个字节
Range: bytes=500-600,601-999 同时指定几个范围
请求结果用下面的
200换206
Content-Range: bytes (unit first byte pos) - [last byte pos]/[entity legth]
```

> get/post 区别

```
1.url带参数 不带参数
2.参数类型限制 get值接受ascii类型 post不限制
3.get请求有上线 post没有
4.get请求发一次,post2次
```

> 计算机网络五层 七层

```
更多详细看这里
https://blog.csdn.net/liuchengzimozigreat/article/details/100169829
```

![计算机网络5层7层](https://img-blog.csdnimg.cn/20190831104146686.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L2xpdWNoZW5nemltb3ppZ3JlYXQ=,size_16,color_FFFFFF,t_70)

> https 过程

```
上述过程就是两次HTTP请求，其详细过程如下：

1.客户端想服务器发起HTTPS的请求，连接到服务器的443端口；

2.服务器将非对称加密的公钥传递给客户端，以证书的形式回传到客户端

3.服务器接受到该公钥进行验证，就是验证2中证书，如果有问题，则HTTPS请求无法继续；如果没有问题，则上述公钥是合格的。（第一次HTTP请求）客户端这个时候随机生成一个私钥，成为client key,客户端私钥，用于对称加密数据的。使用前面的公钥对client key进行非对称加密；

4.进行二次HTTP请求，将加密之后的client key传递给服务器；

5.服务器使用私钥进行解密，得到client key,使用client key对数据进行对称加密

6.将对称加密的数据传递给客户端，客户端使用非对称解密，得到服务器发送的数据，完成第二次HTTP请求。



三、HTTPS和HTTP的区别
通过上线详细对HTTPS的分析，我们可得：

（1）HTTPS是密文传输，HTTP是明文传输；

（2）默认连接的端口号是不同的，HTTPS是443端口，而HTTP是80端口；

（3）HTTPS请求的过程需要CA证书要验证身份以保证客户端请求到服务器端之后，传回的响应是来自于服务器端，而HTTP则不需要CA证书；

（4）HTTPS=HTTP+加密+认证+完整性保护。
```

![HTTPS请求过程](https://img-blog.csdnimg.cn/20190509111404989.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3NldWphdmFfZXI=,size_16,color_FFFFFF,t_70)

> DNS 解析过程,服务器 ip 变了客户端怎么知道

```
app请求DNS 拿到域名IP,然后访问
```

[数据结构]

> 时间复杂度顺序

```
O(1) < O(log2n) < O(n) < O(nlog2n) < O(n^2) < O(n^3) < O(2^n) < O(n!) < O(n^n)
```

> 树直径

```
树的直径指树上距离最远的两点间的距离，它在树上问题上有许多应用，往往通过树的直径的性质可以将一个高时间复杂度的解法变为线性求解。对于树上两点间距离通常有三种定义，我们根据这三种情况分别讨论一下它的性质

```

链表 回文 空间复杂度
二叉树完整
排序
dp 单调栈
数据库 左右链接

![join图](https://img-blog.csdn.net/20180329111108680?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTQyMDQ1NDE=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

[数据库]
数据库索引结构
数据库事务

[JAVA 基础]

> 进程 线程区别

进程是并发执行程序在执行过程中资源分配和管理的基本单位

线程：程序执行的最小单位。

为什么要有线程？
每个进程都有自己的地址空间，即进程空间，在网络或多用户换机下，一个服务器通常需要接收大量不确定数量用户的并发请求，为每一个请求都创建一个进程显然行不通（系统开销大响应用户请求效率低），因此操作系统中线程概念被引进

> 进程通信

```
管道(pipe)
管道是一种半双工的通信方式，数据只能单向流动，而且只能在具有亲缘关系的进程间使用。进程的亲缘关系通常是指父子进程关系。

有名管道 (namedpipe)
有名管道也是半双工的通信方式，但是它允许无亲缘关系进程间的通信。

信号量(semaphore)
信号量是一个计数器，可以用来控制多个进程对共享资源的访问。它常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。

消息队列(messagequeue)
消息队列是由消息的链表，存放在内核中并由消息队列标识符标识。消息队列克服了信号传递信息少、管道只能承载无格式字节流以及缓冲区大小受限等缺点。

信号 (sinal)
信号是一种比较复杂的通信方式，用于通知接收进程某个事件已经发生。

共享内存(shared memory)
共享内存就是映射一段能被其他进程所访问的内存，这段共享内存由一个进程创建，但多个进程都可以访问。共享内存是最快的 IPC 方式，它是针对其他进程间通信方式运行效率低而专门设计的。它往往与其他通信机制，如信号量，配合使用，来实现进程间的同步和通信。

套接字(socket)
套接口也是一种进程间通信机制，与其他通信机制不同的是，它可用于不同设备及其间的进程通信。
```

> 线程通信

```
锁机制：包括互斥锁、条件变量、读写锁
互斥锁提供了以排他方式防止数据结构被并发修改的方法。
读写锁允许多个线程同时读共享数据，而对写操作是互斥的。
条件变量可以以原子的方式阻塞进程，直到某个特定条件为真为止。对条件的测试是在互斥锁的保护下进行的。条件变量始终与互斥锁一起使用。

wait/notify 等待

Volatile 内存共享

CountDownLatch 并发工具

CyclicBarrier 并发工具

信号量机制(Semaphore)
包括无名线程信号量和命名线程信号量。

信号机制(Signal)
类似进程间的信号处理。

线程间的通信目的主要是用于线程同步，所以线程没有像进程通信中的用于数据交换的通信机制。


```

> 线程安全

synchronized ReentrantLock

> 什么时候 volatile 可以保证安全

volatile 变量提供了线程的可见性，并不能保证线程安全性和原子性。

应用 volatile 变量的三个原则：

（1）写入变量不依赖此变量的值，或者只有一个线程修改此变量

（2）变量的状态不需要与其它变量共同参与不变约束

（3）访问变量不需要加锁

volatile 关键字不一定能够保证线程安全的问题，其实，在大多数情况下 volatile 还是可以保证变量的线程安全问题的。所以，在满足以下两个条件的情况下，volatile 就能保证变量的线程安全问题：

运算结果并不依赖变量的当前值，或者能够确保只有单一的线程修改变量的值。
变量不需要与其他状态变量共同参与不变约束。

volatile 关键字能够保证代码的有序性，这个也是 volatile 关键字的作用。
总结一下，一个被 volatile 声明的变量主要有以下两种特性保证保证线程安全。

可见性。
有序性。
[volatile 文章不错](https://blog.csdn.net/rocling/article/details/102784843?utm_medium=distribute.pc_relevant.none-task-blog-title-1&spm=1001.2101.3001.4242)

> 除了 sync 还有其他保证线程安全方法

```
Lock 和 ReadWriteLock，主要实现类分别为 ReentrantLock 和 ReentrantReadWriteLock
atomic 原子性+1
threadlocal 注意复杂对象及集合的clear，防止内存溢出
volatile 内存可见（不要将volatile用在getAndOperate场合（这种场合不原子，需要再加锁），仅仅set或者get的场景是适合volatile的）
可重入读写锁 ReentrantReadWriteLock(1、只有一个线程可以获取到写锁。在获取写锁时，只有没有任何线程持有任何锁才能获取成功； 2、如果有线程正持有写锁，其他任何线程都获取不到任何锁； 3、没有线程持有写锁时，可以有多个线程获取到读锁。)

Lock对比Synchronized
支持以非阻塞方式获取锁
可以响应中断
可以限时
支持了公平锁和非公平锁(在竞争锁之前判断一下等待队列中有没有线程在等待)
```

> java 泛型

> object 方法 11 个方法

```
首先说明两点
Object 是一个特殊的类，是所有类的父类；它提供了11个方法
native(本地的)方法：一个java调用 非java 代码的接口，也就是该方法的实现并不是java代码实现的，比如使用c或者c++实现的。native关键字说明其修饰的方法是一个原生态方法，方法对应的实现不是在当前文件，而是在用其他语言（如C和C++）实现的文件中。Java语言本身不能对操作系统底层进行访问和操作，但是可以通过JNI接口调用其他语言来实现对底层的访问。(附一链接: JAVA中的native是什么？)
1. public final native Class<?> getClass()
navtive方法，用于返回当前运行时对象的Class对下你给，使用了 final 关键字修饰，所以不允许子类重写
2. public native int hashCode()
native方法，用于返回对象的哈希吗，主要使用在哈希表中，比如jdk中的 HashMap
3. public boolean equals(Object obj)
用于比较两个对象的内训地址是否相等，String 类对该方法进行了重写用于比较字符串的值是否相等
有以下5个规则
自反性。对于任何非 null 的引用值 x，x.equals(x) 应该返回 true
对称性。对于任何非 null 引用值 x 和 y ，当且仅当： y.equals(x) 返回 true时，x.equals(y)才返回 true
传递性。对于任何非 null 引用值 x y 和 z，如果 y.equals(x)返回 true，y.equals(z)返回 true，那么 x.equals(z)也应该返回true
一致性。对于任何非 null 引用值 x 和 y，假设对象上 equals 比较中的信息没有被修改，则多次调用 x.equals(y) 始终返回 true 或者 始终返回false
对于任何非 null 引用值x，x.equals(null) 用返回 false
4. protected native Object clone() throws CloneNotSupportedException
native 方法，用于创建并返回当前对象的一份拷贝。

x.clone() != x //true

x.clone().getClass() == x.getClass() //true

Object 本身是没有实现Cloneable接口的，所以不重写 0.clone方法并且进行调用的话会发生CloneNotSupportedException异常。

5. public String toString()
返回对象的字符串表示形式。实际上是该符号字符的 ‘类名称的字符串@’ 和 对象的哈希吗的无符号16进制的表示。换句话说就是返回了下列值的字符串：
getClass().getName() + “@” + Integer.toHesString(hashCode)
建议 Object 所有子类都重写这个方法
6. public final native void notify()
native方法，并且不能重写。
唤醒一个在此对象监视器上等待的线程（监视器相当于就是锁的概念）
如果有多个线程在等待只会唤醒一个。
7. public final native void notifyAll()
native方法，并且不能重写。
作用跟 notify() 一样，只不过会唤醒在此对象监视器上等待的所有线程，而不是一个线程。
8. public final native void wait(long timeout) throws InterruptedException
native 方法，并且不能重写。
作用是：暂停线程的执行。
注意：sleep() 方法没有释放锁，wait释放了锁。timeout 是等待时间。
9. public final void wait(long timeout, int nanos) throws InterruptedException
比上者多了 nanos参数，表示额外的时间（以毫秒为单位，范围是 0 - 999999）。
10. public final void wait() throws InterruptedException
跟上面两个一样，只不过该方法会一直等下去，没有超时这个概念
11. protected void finalize() throws Throwable()
实例被垃圾回收器回收的时候出发的操作
一些延伸的问题
为什么重写 equals 时必须重写 hashCode方法？

数据机构中有一个Map接口的类会使用到键对象的哈希码，在我们调用 put() 或者 get() 方法对 Map 容器进行操作时，是根据键对象的哈希码来计算存储位置的，因此如果我们对哈希码的获取没有相关保证，就可能得不到预期的结果。
在java中，我们可以使用 hashCode() 来获取对象的哈希码，其值就是对象的存储地址。（hashCode()的值是通过Object对象的地址计算得出的）因为Object对象只与自身相等，所以同一个对象的地址总是相等的，计算得出的哈希码也必然相等们对于不同的独享，由于地址不用，所获取的哈希码自然也不会相等。
equals() 与 == 的区别

默认情况下，都是从Object 继承而来的 equals() 与 == 是完全等价的，比较都是对象的内存地址，但我们可以重写 equals(),使其按照我们需求的方式进行比较，如 String 类重写了 equals() ，使其比较的是字符的序列，而不是内存地址。
sleep() 和 wait() 的区别

sleep()：可以使正在执行的线程让出 cpu，然后cpu就可以去执行其他任务了，在sleep指定的时间结束后cpu会再次回到该线程继续往下执行（sleep只是让出了cpu，并不会释放同步资源锁）；sleep()属于线程类(Thread)方法，可以在任何地方使用。
wait()：是当前线程让出同步资源锁，以便其他正在等待该资源的线程得到该资源而运行（进入了等待队列），只有调用了notify()方法，之前调用 wait() 的线程才会解除 wait 状态，重新获得 去竞争同步资源的资格，得到资源后，方可执行；wait() 属于 Object 方法，只能在同步方法或同步块中使用。
```

> equls hashcode 重写

两个对象相等，hashcode 一定相等，两个对象不等，hashcode 不一定不等；

hashcode 相等，两个对象不一定相等，hashcode 不等，两个对象一定不等；
保证 equals 时候 hashcode 一致

> hashmap 原理

```
initialCapacity：初始容量。指的是 HashMap 集合初始化的时候自身的容量。可以在构造方法中指定；如果不指定的话，总容量默认值是 16 。需要注意的是初始容量必须是 2 的幂次方。

size：当前 HashMap 中已经存储着的键值对数量，即 HashMap.size()

loadFactor：加载因子。所谓的加载因子就是 HashMap (当前的容量/总容量) 到达一定值的时候，HashMap 会实施扩容。加载因子也可以通过构造方法中指定，默认的值是 0.75 。举个例子，假设有一个 HashMap 的初始容量为 16 ，那么扩容的阀值就是 0.75 * 16 = 12 。也就是说，在你打算存入第 13 个值的时候，HashMap 会先执行扩容。

threshold：扩容阀值。即 扩容阀值 = HashMap 总容量 * 加载因子。当前 HashMap 的容量大于或等于扩容阀值的时候就会去执行扩容。扩容的容量为当前 HashMap 总容量的两倍。比如，当前 HashMap 的总容量为 16 ，那么扩容之后为 32 。

table：Entry 数组。我们都知道 HashMap 内部存储 key/value 是通过 Entry 这个介质来实现的。而 table 就是 Entry 数组。

HashMap 由数组+链表组成的，数组是 HashMap 的主体，链表则是主要为了解决哈希冲突而存在的

```

> finalize 作用及使用场景

一旦垃圾回收器准备好释放对象占用的存储空间，将首先调用其 finalize() 方法，并且在下一次垃圾回收动作发生时，才真正回收对象占用的内存
在里面做一些清理工作?

> gc 回收

```

新生代：新创建的对象

老生代：创建时间比较长的对象

两代主要用于JVM的GC操作，

新生代经常的被GC

老生代不经常被GC

GC的工作流程主要分为如下几个步骤：

1、标记(Mark)---GC的根节点也即GC Root

2、计划(Plan)

3、清理(Sweep)

4、引用更新(Relocate)

5、压缩(Compact)



在Java中，可以当做GC Root的对象有以下几种：

1、虚拟机（JVM）栈中的引用的对象

2、方法区中的类静态属性引用的对象

3、方法区中的常量引用的对象（主要指声明为final的常量值）

4、本地方法栈中JNI的引用的对象


3）GC类型
GC包括两种，一种是minor GC，一种是majar GC，一种是full gcc

1.Minor GC

对新生代进行回收，不会影响到年老代。因为新生代的Java对象大多死亡频繁，所以 Minor GC 非常频繁，一般在这里使用速度快、效率高的算法，使垃圾回收能尽快完成。

2.Full GC

也叫Major GC，对整个堆进行回收，包括新生代和老年代。由于Full GC需要对整个堆进行回收，所以比Minor GC要慢，因此应该尽可能减少Full GC的次数，导致Full GC的原因包括：老年代被写满、永久代（Perm）被写满和System.gc()被显式调用等。

 

问题：在Minor GC时，存在新生代中没有被其它新生代对象引用，但是被老年代的对象引用了的对象，这种情况是怎么处理的？

答案：这就涉及到跨代引用的问题，不光是老年代会引用新生代，新生代也会引用老年代，如果不采用特殊的办法，我们就只能全堆扫描了，还好，每种GC算法都有一些特殊的处理办法：

比如，在新生代内存区域有一个Remembered Set，里面记录了引用了新生代对象的老年代对象。在进行Minor GC的时候，GC Roots会把Remembered Set中的对象也加进去搜索，这样既不会有遗漏，也不需要全表扫描。

再比如，CMS的remark前新加一次youngGC，把新生代对象先清理一遍，这样扫描的对象就少了，可以有效减少fullGC的时间

再比如，CMS引入了卡表，为老年代的内存空间按照每512B建立卡表，如果一张卡中的对象引用了新生代对象，那么这张卡被标记出来，最后minor gc的时间，除了扫描新生代对象，还扫描卡表中标记的对象。

```

> 深拷贝浅拷贝

```
比如对象套对象
浅拷贝 重新生成一个表面对象,里面的还是同一个

深拷贝,里外里全部新对象
```

> 泛型 extends super 使用场景

```

extends 可用于的返回类型限定，不能用于参数类型限定。 限定上界
super 可用于参数类型限定，不能用于返回类型限定。 限定下界

举例来说比较容易理解: List<? extends Fruit> list 你可以理解为这个list可能是List<Apple>也可能是List<Orange>或List<Apple1>,只要是Fruit或其子类就行, 这时候你要存入一个orange,有可能是往List<Apple>里存,这样是不行的,所以编译器判断不了你这个List是Apple还是Apple1还是Orange(因为都可以),所以就只能什么都不能存(null除外); 同理对于List<? super Apple> list,你可以理解为这个list可能是List<Apple>或List<Fruit>或List<Object>,所以编译器允许你存Apple或其子类,因为都能向上转型成功,但是你要存Fruit就不一定成功了,因为这个List可能是List<Apple>,编译器判断不了.

```

> String 类的存储 编码格式

```
1.java String由char组成，从String中的函数CharAt(int index)便可知

2.String类中存放的是UNICODE编码，每个Char是16位，一个Char可能是传统的ASCII字符，也可能是一个汉字，在内存中都 是占据两个字节
byte[] array1 = s.getBytes("utf-8");
byte[] array2 = s.getBytes("gbk");
byte[] array3 = s.getBytes("unicode");
byte[] array4 = s.getBytes();// 默认是GBK

```

> java 进程创建

```java

import java.io.IOException;

public class ProcessDemo {

	//在Java中如何开启一个进程：运行记事本程序
	public static void main(String[] args) throws IOException {
		//方式1：使用Runtime类的exec方法
		Runtime runtime = Runtime.getRuntime();
		runtime.exec("notepad");

		//方式2：使用ProcessBuilder的start方法
		ProcessBuilder pb = new ProcessBuilder("notepad");
		pb.start();
	}


```

> 线程池介绍,满了怎么办

```
Executor    ExecutorService   ThreadPoolExecutor
1. 使用线程池的优点
降低资源消耗：重用线程池的线程，避免因为线程的创建和销毁锁带来的性能开销

提高响应速度：有效控制线程池的最大并发数，避免大量的线程之间因抢占系统资源而阻塞

提高线程的可管理性：能够对线程进行简单的管理，并提供一下特定的操作如：可以提供定时、定期、单线程、并发数控制等功能

2. 使用线程池的风险
虽然线程池是构建多线程应用程序的强大机制，但使用它并不是没有风险的。用线程池构建的应用程序容易遭受任何其它多线程应用程序容易遭受的所有并发风险，诸如同步错误和死锁，它还容易遭受特定于线程池的少数其它风险，诸如与池有关的死锁、资源不足和线程泄漏。

2.1 死锁
任何多线程应用程序都有死锁风险。当一组进程或线程中的每一个都在等待一个只有该组中另一个进程才能引起的事件时，我们就说这组进程或线程 死锁了。死锁的最简单情形是：线程 A 持有对象 X 的独占锁，并且在等待对象 Y 的锁，而线程 B 持有对象 Y 的独占锁，却在等待对象 X 的锁。除非有某种方法来打破对锁的等待（Java 锁定不支持这种方法），否则死锁的线程将永远等下去。

虽然任何多线程程序中都有死锁的风险，但线程池却引入了另一种死锁可能，在那种情况下，所有池线程都在执行已阻塞的等待队列中另一任务的执行结果的任务，但这一任务却因为没有未被占用的线程而不能运行。当线程池被用来实现涉及许多交互对象的模拟，被模拟的对象可以相互发送查询，这些查询接下来作为排队的任务执行，查询对象又同步等待着响应时，会发生这种情况。

2.2 资源不足
线程池的一个优点在于：相对于其它替代调度机制（有些我们已经讨论过）而言，它们通常执行得很好。但只有恰当地调整了线程池大小时才是这样的。线程消耗包括内存和其它系统资源在内的大量资源。除了 Thread 对象所需的内存之外，每个线程都需要两个可能很大的执行调用堆栈。除此以外，JVM可能会为每个 Java 线程创建一个本机线程，这些本机线程将消耗额外的系统资源。最后，虽然线程之间切换的调度开销很小，但如果有很多线程，环境切换也可能严重地影响程序的性能。

如果线程池太大，那么被那些线程消耗的资源可能严重地影响系统性能。在线程之间进行切换将会浪费时间，而且使用超出比您实际需要的线程可能会引起资源匮乏问题，因为池线程正在消耗一些资源，而这些资源可能会被其它任务更有效地利用。除了线程自身所使用的资源以外，服务请求时所做的工作可能需要其它资源，例如 JDBC 连接、套接字或文件。这些也都是有限资源，有太多的并发请求也可能引起失效，例如不能分配 JDBC 连接。

2.3 并发错误
线程池和其它排队机制依靠使用 wait() 和 notify() 方法，这两个方法都难于使用。如果编码不正确，那么可能丢失通知，导致线程保持空闲状态，尽管队列中有工作要处理。使用这些方法时，必须格外小心。而最好使用现有的、已经知道能工作的实现，例如 util.concurrent 包。

2.4 线程泄漏
各种类型的线程池中一个严重的风险是线程泄漏，当从池中除去一个线程以执行一项任务，而在任务完成后该线程却没有返回池时，会发生这种情况。发生线程泄漏的一种情形出现在任务抛出一个 RuntimeException 或一个 Error 时。如果池类没有捕捉到它们，那么线程只会退出而线程池的大小将会永久减少一个。当这种情况发生的次数足够多时，线程池最终就为空，而且系统将停止，因为没有可用的线程来处理任务。

有些任务可能会永远等待某些资源或来自用户的输入，而这些资源又不能保证变得可用，用户可能也已经回家了，诸如此类的任务会永久停止，而这些停止的任务也会引起和线程泄漏同样的问题。如果某个线程被这样一个任务永久地消耗着，那么它实际上就被从池除去了。对于这样的任务，应该要么只给予它们自己的线程，要么只让它们等待有限的时间。

2.5 请求过载
仅仅是请求就压垮了服务器，这种情况是可能的。在这种情形下，我们可能不想将每个到来的请求都排队到我们的工作队列，因为排在队列中等待执行的任务可能会消耗太多的系统资源并引起资源缺乏。在这种情形下决定如何做取决于您自己；在某些情况下，您可以简单地抛弃请求，依靠更高级别的协议稍后重试请求，您也可以用一个指出服务器暂时很忙的响应来拒绝请求。

线程池满了，往线程池里提交任务会发生什么样的情况，具体分几种情况
如果你使用的LinkedBlockingQueue（阻塞队列），也就是无界队列的话，没关系，继续添加任务到阻塞队列中等待执行，因为LinkedBlockingQueue可以近乎认为是一个无穷大的队列，可以无限存放任务；如果你使用的是有界队列比方说ArrayBlockingQueue的话，任务首先会被添加到ArrayBlockingQueue中，ArrayBlockingQueue满了，则会使用拒绝策略RejectedExecutionHandler处理满了的任务，默认是AbortPolicy。
线程池的饱和策略：当阻塞队列满了，且没有空闲的工作线程，如果继续提交任务，必须采取一种策略处理该任务，线程池提供了4种策略：多线程：当你提交任务时，线程队列已经满了，这时会发生什么？
ThreadPoolExecutor.AbortPolicy:丢弃任务并抛出RejectedExecutionException异常。注：默认策略！！！！！！
ThreadPoolExecutor.DiscardPolicy：也是丢弃任务，但是不抛出异常。
ThreadPoolExecutor.DiscardOldestPolicy：丢弃队列最前面的任务，然后重新尝试执行任务（重复此过程）
ThreadPoolExecutor.CallerRunsPolicy：由调用线程处理该任务

```

> 不用 sync 怎么实现 多线程多次操作 读写锁

> 内部类会泄漏吗,内部类为什么能访问外部类变量,为什么还能访问外部类私有变量

```
1 内部类对象的创建依赖于外部类对象；

2 内部类对象持有指向外部类对象的引用。

关于在内部类中如何使用指向外部类的引用访问外部类成员， 就不用多做解释了， 其实和普通的通过引用访问成员的方式是相同的。 在内部类的InnerMethod方法中， 访问了外部类的成员变量outerField， 下面的字节码揭示了访问是如何进行的：

```

> string a = new string("") 与 string a = "adsf" 区别

```
String A="ABC";

String B=new String("ABC");

这两个值，A,B 是否相等，如果都往HashSet里面放，能放下吗？

答：(a)A==B 的判断为false;

         (b)A.equals(B)为true ；因为值相等，所以都往HashSet里面放不下，只能放一个

 问题：==与equals()的区别：
==:比较引用类型比较的是地址值是否相同
equals:比较引用类型默认也是比较地址值是否相同，注意:String类重写了equals()方法，比较的是内容是否相同。
String A = "ABC";内存会去查找永久代(常量池) ，如果没有的话，在永久代中中开辟一块儿内存空间，把地址付给栈指针，如果已经有了"ABC"的内存，直接把地址赋给栈指针；

因此

String str1="aa";

Srting str2="aa";

String Str3="aa";

....

这样下去，str1==Str2==str3;会一直相等下去，(a) ==的判断， (b) equals()的判断；都相等，因为他们的地址都相等，因此只在常量池中有一份内存空间，地址全部相同；

  而String str = new String("a");是根据"a"这个String对象再次构造一个String对象;在堆中从新new一块儿内存，把指针赋给栈，

将新构造出来的String对象的引用赋给str。 因此 只要是new String()，则，栈中的地址都是指向最新的new出来的堆中的地址，

(a)“”==“” 是判断地址的，当然不相同；

(b)至于equals,String类型重写了 equals()方法，判断值是否相等，明显相等，因此 equals 是相等的；

1. String str1 = "abc";
  System.out.println(str1 == "abc");

步骤：
1) 栈中开辟一块空间存放引用str1，
2) String池中开辟一块空间，存放String常量"abc"，
3) 引用str1指向池中String常量"abc"，
4) str1所指代的地址即常量"abc"所在地址，输出为true

2. String str2 = new String("abc");
  System.out.println(str2 == "abc");

步骤：
1) 栈中开辟一块空间存放引用str2，
2) 堆中开辟一块空间存放一个新建的String对象"abc"，
3) 引用str2指向堆中的新建的String对象"abc"，
4) str2所指代的对象地址为堆中地址，而常量"abc"地址在池中，输出为false

3. String str3 = new String（"abc");
  System.out.println(str3 == str2);

步骤：
1) 栈中开辟一块空间存放引用str3，
2) 堆中开辟一块新空间存放另外一个(不同于str2所指)新建的String对象，
3) 引用str3指向另外新建的那个String对象
4) str3和str2指向堆中不同的String对象，地址也不相同，输出为false

4. String str4 = "a" + "b";
  System.out.println(str4 == "ab");

步骤：
1) 栈中开辟一块空间存放引用str4，
2) 根据编译器合并已知量的优化功能，池中开辟一块空间，存放合并后的String常量"ab"，
3) 引用str4指向池中常量"ab"，
4) str4所指即池中常量"ab"，输出为true

5. final String s = "a";
  String str5 = s + "b";
  System.out.println(str5 == "ab");

步骤：
同4  true
注释：如果去掉final 返回false，如6解释

6. String s1 = "a";
  String s2 = "b";
  String str6 = s1 + s2;
  System.out.println(str6 == "ab");

步骤：
1) 栈中开辟一块中间存放引用s1，s1指向池中String常量"a"，
2) 栈中开辟一块中间存放引用s2，s2指向池中String常量"b"，
3) 栈中开辟一块中间存放引用str5，
4) s1 + s2通过StringBuilder的最后一步toString()方法还原一个新的String对象"ab"，因此堆中开辟一块空间存放此对象，
5) 引用str6指向堆中(s1 + s2)所还原的新String对象，
6) str6指向的对象在堆中，而常量"ab"在池中，输出为false

7. String str7 = "abc".substring(0, 2);

步骤：
1) 栈中开辟一块空间存放引用str7，
2) substring()方法还原一个新的String对象"ab"（不同于str6所指），堆中开辟一块空间存放此对象，
3) 引用str7指向堆中的新String对象，

8. String str8 = "abc".toUpperCase();

步骤：
1) 栈中开辟一块空间存放引用str6，
2) toUpperCase()方法还原一个新的String对象"ABC"，池中并未开辟新的空间存放String常量"ABC"，
3) 引用str8指向堆中的新String对象

```

> java 容器

```
Collection
  List
    ArrayList
    LinkedList
    Vector
    Stack
  Set
    HashSet
    LinkedHashSet
    TreeSet
Map
  HashMap
    LinkedHashMap
  TreeMap
  ConcurrentHashMap
  Hashtable

```

[java 容器相关问题](https://blog.csdn.net/qq_37875585/article/details/89335989)

> string 加载机制 双亲委派

```
类加载器有以下三种：

1）启动类加载器（Bootstrap ClassLoader）,或者叫根加载器。这个类加载器主要是去加载你在本机配置的环境变量 Java_Home/jre/lib 目录下的核心API，如rt.jar
2）扩展类加载器（Extension ClassLoader）。这个加载器负责加载 Java_Home/jre/lib/ext 目录下的所有jar包。

3）应用程序类加载器（Application ClassLoader）。这个加载器加载的是你的项目工程的ClassPath目录下的类库。如果用户没有自定义自己的类加载器，这个就是程序默认的类加载器。

另外，如果有需要的话，用户也可以自定义自己的类加载器（去继承ClassLoader类）。

当一个类在加载的时候，都会先委派它的父加载器去加载，这样一层层的向上委派，直到最顶层的启动类加载器。如果顶层无法加载（即找不到对应的类），就会一层层的向下查找，直到找到为止。 这就是类的双亲委派机制。

双亲委派机制
这样做有什么好处呢？这就相当于维护了一个有优先级的层级关系，即总是从最顶层的父加载器开始加载。这就如同，你工作中遇到了问题需要向上反馈，比如先反馈给小组长，然后小组长反馈给上级经理，最后经理反馈给boss。然后boss感觉这问题太简单了不需要他亲自出手，让经理自己解决吧，然后经理又向下交给小组长。小组长一看，这问题不算难，人也比较热心，于是就帮你把问题解决了。（可能例子不是太恰当哈，意思理解即可）

到此，我们就明白了为什么上边的代码会报错。因为双亲委派机制的存在，去加载我们自己定义的“java.lang.String”类的时候，会最终委派到顶层的启动类加载器，然后找到了rt.jar包下的“java.lang.String”。找到之后，就直接加载rt.jar包的String类（也就是我们经常使用的那个字符串类），不再去向下查找，也就加载不了我们自定义的String类了。由于，rt.jar包下的String类中确实没有main方法，所以才会有以上的报错信息。

我们可以试想一下，如果没有双亲委派机制的存在，那我这段代码是不是就可以执行成功了。如果这样的话，岂不是说明我可以随意覆盖rt.jar包中的类（如String，Integer类等）。这样的话将会使程序陷入混乱，Java核心包中的类的安全也无法保证。

```

![双亲委派图](https://img2018.cnblogs.com/blog/1714084/202002/1714084-20200212171715713-2062898619.jpg)

> final 关键字作用

```
修饰类不可继承 修饰方法不可重写,修饰对象无法被重新赋值
```

> java 自动管理内存为什么泄漏 可达性算法

```
由于java的JVM引入了垃圾回收机制，垃圾回收器会自动回收不再使用的对象，了解JVM回收机制的都知道JVM是使用引用计数法和可达性分析算法来判断对象是否是不再使用的对象，本质都是判断一个对象是否还被引用。那么对于这种情况下，由于代码的实现不同就会出现很多种内存泄漏问题（让JVM误以为此对象还在引用中，无法回收，造成内存泄漏）。

1、静态集合类，如HashMap、LinkedList等等。如果这些容器为静态的，那么它们的生命周期与程序一致，则容器中的对象在程序结束之前将不能被释放，从而造成内存泄漏。简单而言，长生命周期的对象持有短生命周期对象的引用，尽管短生命周期的对象不再使用，但是因为长生命周期对象持有它的引用而导致不能被回收。

2、各种连接，如数据库连接、网络连接和IO连接等。在对数据库进行操作的过程中，首先需要建立与数据库的连接，当不再使用时，需要调用close方法来释放与数据库的连接。只有连接被关闭后，垃圾回收器才会回收对应的对象。否则，如果在访问数据库的过程中，对Connection、Statement或ResultSet不显性地关闭，将会造成大量的对象无法被回收，从而引起内存泄漏。

3、变量不合理的作用域。一般而言，一个变量的定义的作用范围大于其使用范围，很有可能会造成内存泄漏。另一方面，如果没有及时地把对象设置为null，很有可能导致内存泄漏的发生。

```

> runnable 中的 this 指的什么

```
runnable 的匿名内部类

```

> lru 算法中 get 的时间复杂度

```
O(1)
```

[Android]

> 生命周期顺序

![生命周期图](https://upload-images.jianshu.io/upload_images/12239817-57bb34bbf201853d.png?imageMogr2/auto-orient/strip|imageView2/2/w/1074/format/webp)

![Activity与fragment生命周期关系](https://img-blog.csdn.net/20130828141319703)
[不错的文章](https://www.jianshu.com/p/b5a72a741025)

```
透明走里圈
不透明走外圈
跳转先走当前页面的onpause
在触发跳转页面的create,start,resume
```

> handler 原理 机制 场景

[相关文章](https://www.jianshu.com/p/f0b23ee5a922)

```
Handler机制也就是消息机制，它与MessageQueue和Looper完成一个体系.

```

> handler 泄漏

① 先说 handler 导致 activity 内存泄露的原因：
handler 发送的消息在当前 handler 的消息队列中，如果此时 activity finish 掉了，那么消息队列的消息依旧会由 handler 进行处理，若此时 handler 声明为内部类（非静态内部类），我们知道内部类天然持有外部类的实例引用，这样在 GC 垃圾回收机制进行回收时发现这个 Activity 居然还有其他引用存在，因而就不会去回收这个 Activity，进而导致 activity 泄露。

② 为何 handler 要定义为 static？
因为静态内部类不持有外部类的引用，所以使用静态的 handler 不会导致 activity 的泄露

③ 为何 handler 要定义为 static 的同时，还要用 WeakReference 包裹外部类的对象？
这是因为我们需要使用外部类的成员，可以通过"activity. "获取变量方法等，如果直接使用强引用，显然会导致 activity 泄露

```java

public class MainActivity extends AppCompatActivity {

    TextView mTextView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        mTextView = findViewById(R.id.tv_handler);
        doInBackground();
    }


    private Handler mHandler = new Handler() {
        @Override
        public void handleMessage(Message msg) {
            super.handleMessage(msg);
            switch (msg.what) {
                case 1:
                    mTextView.setText(msg.obj + "");
                    break;
                default:
                    break;
            }
        }
    };


    private void doInBackground() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    //模拟耗时操作
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                //耗时操作完成，发送消息给UI线程
                Message msg = Message.obtain();
                msg.what = 1;
                msg.obj = "更新UI";
                mHandler.sendMessage(msg);
            }
        }).start();
    }

}

```

> java 四大引用 强 软 弱 虚

[相关文章](https://www.cnblogs.com/liyutian/p/9690974.html)

```java
一，强引用
Java中默认声明的就是强引用，比如：
//只要obj还指向Object对象，Object对象就不会被回收
Object obj = new Object();
//手动置null
obj = null;
只要强引用存在，垃圾回收器将永远不会回收被引用的对象，哪怕内存不足时，JVM也会直接抛出OutOfMemoryError，不会去回收。如果想中断强引用与对象之间的联系，可以显示的将强引用赋值为null，这样一来，JVM就可以适时的回收对象了
二，软引用
在内存不足的情况下，软引用将会被自动回收
三，弱引用
弱引用的引用强度比软引用要更弱一些，无论内存是否足够，只要 JVM 开始进行垃圾回收，那些被弱引用关联的对象都会被回收。在 JDK1.2 之后，用 java.lang.ref.WeakReference 来表示弱引用
四，虚引用
虚引用是最弱的一种引用关系，如果一个对象仅持有虚引用，那么它就和没有任何引用一样，它随时可能会被回收，在 JDK1.2 之后，用 PhantomReference 类来表示，通过查看这个类的源码，发现它只有一个构造函数和一个 get() 方法，而且它的 get() 方法仅仅是返回一个null，也就是说将永远无法通过虚引用来获取对象，虚引用必须要和 ReferenceQueue 引用队列一起使用。

五，引用队列（ReferenceQueue）
引用队列可以与软引用、弱引用以及虚引用一起配合使用，当垃圾回收器准备回收一个对象时，如果发现它还有引用，那么就会在回收对象之前，把这个引用加入到与之关联的引用队列中去。程序可以通过判断引用队列中是否已经加入了引用，来判断被引用的对象是否将要被垃圾回收，这样就可以在对象被回收之前采取一些必要的措施。

与软引用、弱引用不同，虚引用必须和引用队列一起使用。
```

> 接口 抽象类区别

```
接口和抽象类的区别：
含有 abstract 修饰符 class 即为抽象类。

接口中的所有方法都必须是抽象的，接口中的方法定义默认为 public abstract 类型，接口中的成员变量类型默认为 public static final。

抽象类中可以有普通成员变量；接口中没有普通成员变量。

抽象类中可以包含非抽象普通方法；接口中的所有方法必须都是抽象的，不能有非抽象的方法。

抽象类中的抽象方法的访问权限可以是 public、protected 和(默认类型，虽然 eclipse 不报错，但也不能用，默认类型子类不能继承)；接口中的抽象方法只能是 public 类型的，并且默认即为 public abstract 类型。

一个类可以实现多个接口，用逗号隔开，但只能继承一个抽象类；接口不可以实现接口，但可以继承接口，并且可以继承多个接口，用逗号隔开。

```

> 重写 重载

```
重写与重载之间的区别
区别点	重载方法	重写方法
参数列表	必须修改	一定不能修改
返回类型	可以修改	一定不能修改
异常	可以修改	可以减少或删除，一定不能抛出新的或者更广的异常
访问	可以修改	一定不能做更严格的限制（可以降低限制）
总结
方法的重写(Overriding)和重载(Overloading)是java多态性的不同表现，重写是父类与子类之间多态性的一种表现，重载可以理解成多态的具体表现形式。

(1)方法重载是一个类中定义了多个方法名相同,而他们的参数的数量不同或数量相同而类型和次序不同,则称为方法的重载(Overloading)。
(2)方法重写是在子类存在方法与父类的方法的名字相同,而且参数的个数与类型一样,返回值也一样的方法,就称为重写(Overriding)。
(3)方法重载是一个类的多态性表现,而方法重写是子类与父类的一种多态性表现
```

> mvp 理解

![MVP](https://img-blog.csdn.net/20170409164517040?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTQxODk0Mw==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

```
MVP，全称 Model-View-Presenter。它是从 MVC中演变过来的，它的基本思想是相通的；在MVP中，View更加专注于处理数据的可视化以及用户交互，让Model专注于数据的处理，而Presenter则，提供 View 与 Model 之间数据的纽带

我们在使用一种设计模式的时候，首先都会问，为什么要用这种模式，能给我们带来哪些方便？用了这种模式，它的缺点会不会给我的工程造成影响？
首先，优点上，我们上面已经阐述了；

减低耦合，实现了 Model 与View 的真正分离，修改 View 而不影响 Model
模块职责分明，层次分明，便于维护，多人开发首选。
Presenter 可以服用，一个 Presenter可以用于多个 View，不用去改 Presenter
利于单元测试。模块分明，那么我们编写单元测试就变得很方便了，而不用特别是特别搭平台，人工模拟用户操作等等耗时耗力的事情。
缺点：
对于小工程，额外多出来的代码量，和额外的代码复杂度，毕竟那么多 interface ，但对于它的有点来说，完全可以接受。

```

> retrofit 相关

> [文章地址](https://www.jianshu.com/p/491603d9fadc)

```
涉及到的设计模式
外观模式，构建者模式，工厂模式，代理模式，适配器模式，策略模式，观察者模式

概括
Retrofit就是一个网络请求框架的封装，底层的网络请求默认使用的Okhttp，本身只是简化了用户网络请求的参数配置等，还能与Rxjava相结合，使用起来更加简洁方便。

App应用程序通过Retrofit请求网络，实际上是使用Retrofit接口层封装请求参数，之后由OkHttp完成后续的请求操作。
在服务端返回数据之后，OkHttp将原始的结果交给Retrofit，Retrofit根据用户的需求对结果进行解析。
完成数据的转化(converterFactory)，适配(callAdapterFactory)，通过设计模式进行各种扩展。

使用Retrofit的七步骤
添加Retrofit依赖，网络权限
定义接收服务器返回数据的Bean
创建网络请求的接口，使用注解(动态代理，核心)
builder模式创建Retrofit实例，converter，calladapter...
创建接口实例，调用具体的网络请求
call同步/异步网络请求
处理服务器返回的数据
Retrofit网络通信八步骤
创建Retrofit实例
定义网络请求接口，并为接口中的方法添加注解
通过动态代理生成网络请求对象
通过网络请求适配器将网络请求对象进行平台适配
通过网络请求执行器，发送网络请求(call)
通过数据解析器解析数据
通过回调执行器，切换线程
用户在主线程处理返回结果

第一步创建一个Retrofit对象

通过 Retrofit.onCreate()方法把我们所定义的接口转化为接口实例并使用接口中的方法

最终的实例都会调用okHttp的call，retrofit用于OKhttp的，比较灵活
```

关于 create 时候的动态代理
[retrofit 动态代理](https://blog.csdn.net/weixin_34356138/article/details/87947990)

> java 面向对象 3 大特性

![三特性](https://img-blog.csdnimg.cn/20181112133745377.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MTc5MjE2Mg==,size_16,color_FFFFFF,t_70)

```
封装 继承 多态
```

> java 异常机制

![java异常机制](https://img-blog.csdn.net/20160331115514210)

```
try、catch、throw、throws和finally

声明异常（throws）
抛出异常（throw）
捕获异常（try-catch）
finally语句块 无论是否有异常，finally块中的代码总是会被执行的。 finally语句在执行关闭资源的语句时非常有用。

try、catch、finally、throw和throws使用归纳
try、catch和finally都不能单独使用，只能是try-catch、try-finally或者try-catch-finally。
try语句块监控代码，出现异常就停止执行下面的代码，然后将异常移交给catch语句块来处理。
finally语句块中的代码一定会被执行，常用于回收资源 。
throws：声明一个异常，告知方法调用者。
throw ：抛出一个异常，至于该异常被捕获还是继续抛出都与它无关。
```

> anr 原因 排查

```

什么是ANR?

ANR:Application Not Responding，即应用无响应

ANR日志Trace文件获取

系统生成的Trace文件保存在data/anr,可以用过命令adb pull data/anr/取出

traces.txt只保留最后一次ANR的信息,Android系统有个DropBox功能功能,它能记录系统出现的crash错误.因此保留有发生过的ANR的信息.(log路径:/data/system/dropbox)

获取系统crash log: adb shell dumpsys dropbox --print >>log.txt


Trace文件怎么生成的?

当APP(包括系统APP和用户APP)进程出现ANR、应用响应慢或WatchDog的监视没有得到回馈时,

系统会dump此时的top进程,进程中Thread的运行状态就都dump到这个Trace文件中了.
导致ANR的常见几种情况:

1：Input dispatching timed out(5 seconds) 按键或触摸事件处理超时(一般是UI主线程做了耗时的操作,这类ANR最常见)

2：BroadcastTimeout(10 seconds) 广播的分发和处理超时(一般是onReceiver执行时间过长)

3：ServiceTimeout(20 seconds) Service的启动和执行超时

另外还有ProviderTimeout和WatchDog等导致的ANR.还有当系统内存或CPU资源不足时容易出现ANR,一般这种情况会有lowmemorykill的log打印.

应用ANR产生的时候,ActivityManagerService的appNotResponding方法就会被调用,然后在/data/anr/traces.txt文件中写入ANR相关信息.

避免ANR?

UI线程尽量只做跟UI相关的工作

耗时的工作（比如数据库操作，I/O，连接网络或者别的有可能阻碍UI线程的操作）把它放入单独的线程处理

尽量用Handler来处理UIthread和别的thread之间的交互


ZOMBIE 线程死亡，终止运行

RUNNING/RUNNABLE 线程可运行或正在运行

TIMED_WAIT 执行了带有超时参数的wait、sleep或join函数

MONITOR 线程阻塞，等待获取对象锁

WAIT 执行了无超时参数的wait函数

INITIALIZING 新建，正在初始化，为其分配资源

STARTING 新建，正在启动

NATIVE 正在执行JNI本地函数

VMWAIT 正在等待VM资源

SUSPENDED 线程暂停，通常是由于GC或debug被暂停

一些典型的ANR 问题场景
1）最常见错误，UI线程等待其它线程释放某个锁，导致UI线程无法处理用户输入；

2）游戏中每帧动画都进行了比较耗时的大量计算，导致CPU忙不过来；

3）Web应用中，网络状态不稳定，而界面在等待网络数据；

4）UI线程中进行了一些磁盘IO（包括数据库、SD卡等等）的操作，在个别设备上因为硬件损坏等原因阻塞住了；

5）手机被其他App占用着CPU，自己获取不到足够的CPU 时间片，纯属误伤。

排查分析的思路：
1、通过ANR 日志定位问题
当ANR发生时，我们往往通过Logcat和traces文件（目录/data/anr/）的相关信息输出去定位问题。主要包含以下几方面：

1）基本信息，包括进程名、进程号、包名、系统build号、ANR 类型等等；

2）CPU使用信息，包括活跃进程的CPU 平均占用率、IO情况等等；

3）线程堆栈信息，所属进程包括发生ANR的进程、其父进程、最近有活动的3个进程等等。

这一步一般来说能基本定位是什么造成了ANR，是IO高还是CPU高，如两者都不是，需进入第三步trace日志分析环节 查看mobilelog文件夹下的main_log,搜索关键字"ANR in",可以看到当时的CPU以及IO率，这一个环节一般来讲主要是看发生ANR时的CPU使用情况，CPU是否吃紧，

还有需要注意iowait的占有率，如果占比比较高，则排查的方向要倾向与读取文件操作有关的信息，可以看trace日志中有没有一些读取文件或者操作SD卡的动作

Step3:分析ANR时的堆栈(必需，最重要的环节)
ANR发生后会在data/anr下生成trace.txt，这份trace务必要与moile日志匹配，一般来讲直接先看tid=1的堆栈即对应主线程，因为ANR都是主线程执行超时导致

关于trace日志的分析是ANR问题分析最重要的一个环节，下面将列出平时项目中遇到的ANR案例类型


```

> 大图 OOM

```java

inJustDecodeBounds 非常重要,不读取bitmap,但是返回图片大小宽高;

BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true;
BitmapFactory.decodeResource(getResources(), R.id.myimage, options);
int imageHeight = options.outHeight;
int imageWidth = options.outWidth;
String imageType = options.outMimeType;

1、  不要加载原始大图，根据显示控件进行比例缩放后加载其缩略图。

2、  不要在主线程中加载图片，主要在listview和gridview中使用异步加载图片是要注意处理图片错位和无用线程的问题。

3、  使用缓存，根据实际情况确定是否使用双缓存和缓存大小。

```

> 图片占用内存大小计算

```
A RGB指的是一种色彩模式，里面A代表Alpha， R 表示 red ， G 表示 green ， B 表示 blue ，其实所有的可见色都是红绿蓝组成的，所以红绿蓝又称为三原色。
A　　R　　G　　B
透明度　 红色 　 绿色 　 蓝色
简单点说
 图片格式（Bitmap.Config）

 占用内存的计算方向

 一张100*100的图片占用内存的大小

 ALPHA_8

 图片长度*图片宽度

 100*100=10000字节

 ARGB_4444

 图片长度*图片宽度*2

 100*100*2=20000字节

 ARGB_8888

 图片长度*图片宽度*4

 100*100*4=40000字节

 RGB_565

 图片长度*图片宽度*2

 100*100*2=20000字节

 最后Android设备上,160的像素密度下1dp = 1px,
 如果是320dpi 那么1dp = 2px 内存占用翻倍

```

> glide 原理

```
Glide是一个优秀的图片加载库，它有如下优点：

Glide可以监听Activity的生命周期管理，更加合理的管理图片的加载和释放。
加载质量，Picasso默认采用的ARGB-8888, Glide默认采用的是RGB-565，内存占用会减小一半。
Glide可以加载Gif图。
缓存策略和加载速度。Picasso缓存的是全尺寸，而Glide的缓存的图片和ImageView的尺寸相同。Glide的这个特点,让加载显得特别的快,而Picasso则因为需要在显示之前重新调整大小而导致一些延迟。
Glide可以通过自定义GlideMoudle来完成特殊的加载需求，例如加载加密的图片等。
这里简单分析一下Glide是怎么样的一个加载流程。

Glide提供了众多with方法，可以传入Activity, Context, Fragment, FragmentActivity。然后根据不同情况会作不同的处理。

创建RequestManagerFragment对象并添加到FragmentManager中负责监听生命周期变化，创建RequestManager对象。
创建SupportRequestManagerFragment对象并添加到FragmentManager中负责监听生命周期变化，创建RequestManager对象。
普通Context情况，只是创建RequestManager对象。
因此针对是Activity，Fragment的情况，可以监听生命周期，启动和暂停图片的加载等等，更加智能，这个比Picasso要更有优势。

Glide -> with(RequestManagerRetriever对象中(这里会同步生命周期))
->load(RequestManager对象) ->fromString()-> DrawableTypeRequest -> (holder等操作) ->
into
into方法在DrawableTypeRequestBuilder类中，里面调用了super.into方法，真正的实现在DrawableTypeRequestBuilder的父类GenericRequestBuilder中，这个类包括了网络请求，图片解析，图片解码，bitmap生成，缓存处理，图片压缩等大量逻辑操作，最后的最后才将图片展示出来

最关注的是Glide缓存：Glide分为内存缓存和硬盘缓存两个模块，先从内存缓存查找再到硬盘缓存查找最后从网络请求。

每种缓存必须有一个缓存key用来查询，Glide缓存key是在Engine类load方法中实现，通过调用getId方法获取一个字符串id，这个id就是要加载图片的唯一标识。如果是网络图片就是图片的url地址。然后这个id同传入的参数构建出EngineKey也就是Glide的缓存key。

内存缓存：

1 LruCache算法缓存，最近最少使用原则，缓存原理是把最近使用的对象用强引用存储到LinkHashMap中，把最近最少使用的对象在缓存值达到一定上限之前从缓存中移除。

2 弱引用缓存，缓存原理是把正在使用的图片缓存到弱引用的HashMap中，主要防止正在使用的图片被LruCache算法回收。



硬盘缓存：缓存策略有缓存转换后的图片和原始图片，默认是缓存转换后的。缓存算法依然是LruCache是谷歌封装的DisLruCache。

如果有缓存文件的话会调用decodeFromCache从缓存中读取，如果没有调用decodeFromSource，从源头读取比如从网络获取。
Glide缓存key是在Engine类中load方法中生成的。在这个方法中通过getId()方法返回了一个字符串id，发现这个id就是图片的Url地址。然后找这个getId方法，发现在这个方法里又调用了GlideUrl类中getCacheKey方法，在这个方法里直接返回了图片的url地址，最后通过继承这个GlideUrl类重写getCacheKey方法，去掉图片的Url地址后面的token参数，然后将这个继承类传给Glide的load方法就可以解决Glide缓存问题了
```

> 图片压缩 图片大小

```java
压缩图片质量,bitmap中的compress方法

    Bitmap originBitmap = BitmapFactory.decodeFile(originFile.getAbsolutePath());
    ByteArrayOutputStream bos = new ByteArrayOutputStream();
    originBitmap.compress(format, quality, bos);

更改图片大小
BitmapFactory.Options options = new BitmapFactory.Options();
    //设置此参数是仅仅读取图片的宽高到options中，不会将整张图片读到内存中，防止oom
    options.inJustDecodeBounds = true;
    Bitmap emptyBitmap = BitmapFactory.decodeFile(originFile.getAbsolutePath(), options);

    options.inJustDecodeBounds = false;
    options.inSampleSize = inSampleSize;

int height = options.outHeight * 200 / options.outWidth;
options.outWidth = 200；
options.outHeight = height;
options.inJustDecodeBounds = false;
Bitmap bmp = BitmapFactory.decodeFile(path, options);
image.setImageBitmap(bmp);
```

> 三级缓存 lrucache lrudiskCache

> listview recycleview 区别

[文章地址](https://www.jianshu.com/p/257c279a3493)

```
缓存
ListView(两级缓存)
RecyclerView(四级缓存)
ListView和RecyclerView缓存机制基本一致：

1). mActiveViews和mAttachedScrap功能相似，意义在于快速重用屏幕上可见的列表项ItemView，而不需要重新createView和bindView；

2). mScrapView和mCachedViews + mReyclerViewPool功能相似，意义在于缓存离开屏幕的ItemView，目的是让即将进入屏幕的ItemView重用.

3). RecyclerView的优势在于a.mCacheViews的使用，可以做到屏幕外的列表项ItemView进入屏幕内时也无须bindView快速重用；b.mRecyclerPool可以供多个RecyclerView共同使用，在特定场景下，如viewpaper+多个列表页下有优势.客观来说，RecyclerView在特定场景下对ListView的缓存机制做了补强和完善。

局部刷新

以RecyclerView中notifyItemRemoved(1)为例，最终会调用requestLayout()，使整个RecyclerView重新绘制，过程为：

onMeasure()–>onLayout()–>onDraw()

其中，onLayout()为重点，分为三步：

dispathLayoutStep1()：记录RecyclerView刷新前列表项ItemView的各种信息，如Top,Left,Bottom,Right，用于动画的相关计算；

dispathLayoutStep2()：真正测量布局大小，位置，核心函数为layoutChildren()；

dispathLayoutStep3()：计算布局前后各个ItemView的状态，如Remove，Add，Move，Update等，如有必要执行相应的动画.
当调用fill()中RecyclerView.getViewForPosition(pos)时，RecyclerView通过对pos和flag的预处理，使得bindview只调用一次.
当调用notifyItemRemoved时，会对屏幕内ItemView做预处理，修改ItemView相应的pos以及flag(流程图中红色部分)

更灵活,支持各种布局,甚至横向滑动
```

> 事件分发 cancel

![事件分发](https://upload-images.jianshu.io/upload_images/966283-b9cb65aceea9219b.png?imageMogr2/auto-orient/strip|imageView2/2/w/885/format/webp)

```
onInterceptTouchEvent
dispatchTouchEvent


ACTION_DOWN:只在dispatchTouchEvent能被拦截 如果不拦截,ontouchevent依次上传
ACTION_MOVE、ACTION_UP 在两个环节都会马上终结 return true


对于ACTION_MOVE、ACTION_UP总结：ACTION_DOWN事件在哪个控件消费了（return true）， 那么ACTION_MOVE和ACTION_UP就会从上往下（通过dispatchTouchEvent）做事件分发往下传，就只会传到这个控件，不会继续往下传，如果ACTION_DOWN事件是在dispatchTouchEvent消费，那么事件到此为止停止传递，如果ACTION_DOWN事件是在onTouchEvent消费的，那么会把ACTION_MOVE或ACTION_UP事件传给该控件的onTouchEvent处理并结束传递。

```

> recycle 怎么实现滑动功能

```

```

> 收到 intent 广播后,创建子线程下载是否合适

```

在android中Activity的最长执行时间是5秒。
BroadcastReceiver的最长执行时间则是10秒。
Service的最长执行时间则是20秒。
```

> 延迟消息 message quene 顺序 数据结构

```
MessageQuene
MessageQuene在Android中是消息队列的意思，但内部存储结构并不是队列，而是采用单向链表的数据结构形式存储消息。是Handler的具体实现之一（另外一个是Looper）。

MessageQuene#enqueueMessage()
enqueueMessage()是MessageQuene消息插入的实现方法，其实就是一个链表的插入操作。

首先会进入无限循环，结束循环的条件是p == null || when < p.when（即已经遍历到消息链表的最后一个节点或者当前被插入的消息的延迟时间小于当前被遍历到的消息的延迟时间）。结束循环后会把msg插入prev节点和p节点之间。

由上面分析可以看出mMessages始终充当的是消息链表的头结点。当没有消息或者消息延时时间为0或者消息的延时时间比头结点的延时时间短时都采用的是链表头插的方式。其他时候是通过比较延迟时间，按照延迟时间长短顺序插入（延迟时间越短越靠前，会被优先处理）。


```

> 广播种类 注册方式 不同注册方式的生命周期

```
xml注册 app死亡也会收到
动态代码注册,跟随app生命周期可以手动取消
```

> 局部广播 全局广播区别 分别怎么实现的

```
1、本地广播：发送的广播事件不被其他应用程序获取，也不能响应其他应用程序发送的广播事件。本地广播只能被动态注册，不能静态注册。动态注册或方法时需要用到LocalBroadcastManager。

2、全局广播：发送的广播事件可被其他应用程序获取，也能响应其他应用程序发送的广播事件（可以通过 exported–是否监听其他应用程序发送的广播 在清单文件中控制） 全局广播既可以动态注册，也可以静态注册。

三、本地广播的实现原理
我们通过本地广播的基本使用知道本地广播和全局广播在形式上主要的不同源于LocalBroadcastManager这个类。所以要想了解本地广播的实现原理，看这个类就可以。

接下来我们通过主要方法了解内部实现。
1、LocalBroadcastManager.getInstance(this) //实例化本地广播管理者
在了解其他方法前，我们先来了解一下LocalBroadcastManager类的两个内部数据类（ReceiverRecord 和 BroadcastRecord ）和三个数据集合。
最后通过在构造方法中构建的主线程Handler发送消息 执行executePendingBroadcasts()该方法
通过上面对源码的分析我们知道本地广播的实现原理其实就是 利用HashMap和List来维护广播接收者、IntentFileter、Intent三者之间的关系。

全局广播实际是用了intent

```

> activitiy service 通信方式

```
1 通过intent
2 activity 实现 ServiceConnection ,跟service建立连接,然后通过自定义binder,传输值
3 activity 实现 ServiceConnection 然后通过binder拿到service,然后设定回调接口
4 广播

service作为Android四大组件之一，我们会经常使用它。
service的启动方式有两种
1）使用Context.startService（Intent）启动
2）通过bindService启动（该方式service的生命周期跟activity的生命周期相关联）

需要注意的是:
service的onCreate只会执行一次，onBind也只会执行一次，onStartCommand可以执行多次

也就是说一旦service创建过了，而又没有被销毁的话，不管是再次调用bindService还是startService都不会再走onCreate， onBind也只会执行一次，多次调用startService会多次执行onstartCommand。
```

> 并发并行什么意思,多线程是并发还是并行

```
并发并行什么意思,多线程是并发还是并行
并发和并行都可以是很多个线程，就看这些线程能不能同时被（多个）cpu执行，如果可以就说明是并行，而并发是多个线程被（一个）cpu 轮流切换着执行。

你吃饭吃到一半，电话来了，你一直到吃完了以后才去接，这就说明你不支持并发也不支持并行。你吃饭吃到一半，电话来了，你停了下来接了电话，接完后继续吃饭，这说明你支持并发。你吃饭吃到一半，电话来了，你一边打电话一边吃饭，这说明你支持并行。并发的关键是你有处理多个任务的能力，不一定要同时。并行的关键是你有同时处理多个任务的能力。所以我认为它们最关键的点就是：是否是『同时』

```

> android 个版本特性
> 按钮连续点击情况怎么避免

```
1 点击后设置enable = false;
2 或者设置一个flag判断,通过判断flag,看是否能点击
3 通过aop,找到onclick的切入点,在点击前插入判断代码
4 自定义click接口也能实现全局判断,代价就是只能用自己的click
5 写一个clickUtils 其实本质上跟1,2区别不大
```

> alarmmanager postdelay 区别

```
alarmmanager 可以定时执行任务,可以循环后台执行
postdelay是向主线程消息队列发送消息
```

> 项目中后台请求为什么用 service 不用线程

```
 1）：长期后台运行。
 2）：没有界面
 并且,在垃圾回收的时候,线程可能会被回收
 容易泄露内存
 service是可控的
Thread 的运行是独立的，也就是说当一个 Activity 被 finish 之后，如果没有主动停止 Thread 或者 Thread 里的 run 方法没有执行完毕的话，Thread 也会一直执行。因此这里会出现一个问题：当 Activity 被 finish 之后，不再持有该 Thread 的引用，也就是不能再控制该Thread。另一方面，没有办法在不同的 Activity 中对同一 Thread 进行控制
```

> contentprovider 生命周期,四大组件区别

```
 活动（Activity）、服务（Service）、广播（BroadcastReceiver）和内容提供者（Contentprovider）合称Android四大组件。其中除了Activity是以界面的形式呈现的，其他三大组件对于用户都是透明的。

       Activity是一种展示型组件，具有两种启动方式，一种是显示的，通过intent实现；另一种是隐式的，也需要intent，但还需要在AndroidManifest.xml中添加intentfilter。在实现Activity时，需要继承Activity抽象类，并且重写onCreat()方法，因此，Activity具有启动和停止的概念。

       Service是一种计算型组件，其生命是嵌套在主线程中的。Service有两种状态：启动状态和绑定状态，并且只能显示启动，因此在AndroidManifest.xml中注册Service时，只需要注册服务的类名即可。启动状态和绑定状态的区别在于：启动之后，Service的动作不受Activity的控制；而绑定之后，Service的动作是由Activity控制的。在实现Service时，需要继承Service抽象类，并且重写onCreat()方法，因此，Service也具有启动和停止的概念。

      BroadcastReceiver是一种消息型组件。由于BroadcastReceiver可以在不同的组件甚至不同的应用之间传递消息，所以其可以脱离Activity实现，除了要在AndroidManifest.xml中注册广播类名外，还需要添加intentfilter，这样就可以让receiver选择性的接收广播。当注册完成之后，即使没有Activity启动，也可以接收广播。在实现 BroadcastReceiver时，需要继承 BroadcastReceiver抽象类，但是不需要重写onCreat()方法，只需重写onReceive()方法，因此，Service没有启动和停止的概念，更像是一个系统级的监听器。

       ContentProvider是一种数据共享型组件，可以在应用之间共享数据。所以与BroadcastReceiver一样，其可以脱离Activity实现。在实现ContentProvider时，需要继承ContentProvider抽象类，然后在AndroidManifest.xml中注册类名和ContentProvider的域名。同样的，不需要重写onCreat()方法，而是实现CRUD操作，所以ContentProvider没有启动和停止的概念，更像是一个系统级的监听器。与前三个组件不同的是，ContentProvider并没有使用intent，而是使用URI来判定能否为ContentResolver提供数据共享。

总结：

1.Activity与Service有生命周期，而BroadcastReceiver与ContentProvider采用监听机制，没有生命周期。

2. 四大组件都需要在AndroidManifest.xml中注册。

3.除了ContentProvider外，其他组件都要用到intent。

4. 四大组件实现时都要继承其抽象的父类。

5. Service与Activity关系最为密切，BroadcastReceiver与ContentProvider的实现基本不依赖于Activity。
```

> 未下载图片怎么获取宽高

```
如果未下载,可以通过服务器加参数的形式,先拿到宽高在处理
如果已经下载,在bitmapfactory.options里面有个inJustDecodeBounds属性设置成true,就会返回图片信息,但是不实际加载图片到内存
```

> activity 四种启动模式

```
默认启动模式standard
该模式可以被设定，不在manifest设定时候，Activity的默认模式就是standard。在该模式下，启动的Activity会依照启动顺序被依次压入Task中
栈顶复用模式singleTop
在该模式下，如果栈顶Activity为我们要新建的Activity（目标Activity），那么就不会重复创建新的Activity
栈内复用模式singleTask
  与singleTop模式相似，只不过singleTop模式是只是针对栈顶的元素，而singleTask模式下，如果task栈内存在目标Activity实例，则：

将task内的对应Activity实例之上的所有Activity弹出栈。
将对应Activity置于栈顶，获得焦点。
全局唯一模式singleInstance：
    这是我们最后的一种启动模式，也是我们最恶心的一种模式：在该模式下，我们会为目标Activity分配一个新的affinity，并创建一个新的Task栈，将目标Activity放入新的Task，并让目标Activity获得焦点。新的Task有且只有这一个Activity实例。       如果已经创建过目标Activity实例，则不会创建新的Task，而是将以前创建过的Activity唤醒（对应Task设为Foreground状态）
```

> listview 不 notify 怎么刷新 局部刷新

```java

//activity中
	//判断点击的位置是否在屏幕显示的地方，是的话就调用改变item的方法
private void updateView(int position,String data){
    int firstVisi = lv.getFirstVisiblePosition();
	//判断点击的位置是否在屏幕显示的地方
    if (position - firstVisi > 0) {
        View v = lv.getChildAt(position-firstVisi);
		//适配器里面改变点击条目的方法
        adapter.updateData(v, position);
    }

    //adapter中
    public void updateData(View view, int position) {
        ViewHolder holder = (ViewHolder) view.getTag();

        holder.tv.setText("局部更新" + position);
    }

```

> 进程间通信方式 android 有哪些

> 为什么 Android 要采用 Binder 作为 IPC 机制？

```
现有进程间通讯
1. 管道：在创建时分配一个page大小的内存，缓存区大小比较有限；
2. 消息队列：信息复制两次，额外的CPU消耗；不合适频繁或信息量大的通信；
3. 共享内存：无须复制，共享缓冲区直接付附加到进程虚拟地址空间，速度快；但进程间的同步问题操作系统无法实现，必须各进程利用同步工具解决；
4. 套接字：作为更通用的接口，传输效率低，主要用于不通机器或跨网络的通信；
5. 信号量：常作为一种锁机制，防止某进程正在访问共享资源时，其他进程也访问该资源。因此，主要作为进程间以及同一进程内不同线程之间的同步手段。
6. 信号: 不适用于信息交换，更适用于进程中断控制，比如非法内存访问，杀死某个进程等；
Android OS中的Zygote进程的IPC采用的是Socket（套接字）机制，Android中的Kill Process采用的signal（信号）机制等等。而Binder更多则用在system_server进程与上层App层的IPC交互

```

[文章地址](https://www.jianshu.com/p/82b691cbdde4)

> Android Binder 机制？

[文章地址](https://www.jianshu.com/p/4ee3fd07da14)

```
通过内存缓冲区完成数据交互

```

> view 绘制 测量

```


```

[算法]
算法 翻转链表
对称加密 非对称加密
单例 DCL
二叉搜索树转双向链表 原地操作
算法 topK
算法 N 阶 1,2,5 阶梯
各种排序 复杂度 什么适用单向链表
快排 单向链表
算法 二叉树右视图
所有 map 以及他们之间对比适用场景
算法 单链表判断有无环
算法 单链表逆序 算法时间复杂度空间复杂度
加密算法
算法 给定数组基数左边,偶数右边
遍历路径求和
4 个 cpu 16 个数做比较 如何找出最大
两个数字非常大的数字 string 求和 转为 char[] 倒序相加

