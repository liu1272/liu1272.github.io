title: Java IO
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-12-15 01:48:00
---
#### Java IO一览表
<a href="https://smms.app/image/lxEK1eG4MWaQvqS" target="_blank"><img src="https://s2.loli.net/2023/12/15/lxEK1eG4MWaQvqS.png" ></a>

## 同步|异步|阻塞|非阻塞
#### 同步
  指的是协同步调。既然叫协同，所以至少要有2个以上的事物存在。协同的结果就是：多个事物不能同时进行，必须一个一个的来，上一个事物结束后，下一个事物才开始。
#### 异步
  就是步调各异。既然是各异，那就是都不相同。所以结果就是：多个事物可以你进行你的、我进行我的，谁都不用管谁，所有的事物都在同时进行中。
#### 阻塞
  所谓阻塞：指的是阻碍堵塞。它的本意可以理解为由于遇到了障碍而造成的动弹不得。
#### 非阻塞
  所谓非阻塞：自然是和阻塞相对，可以理解为由于没有遇到障碍而继续畅通无阻。


## BIO
**同步并阻塞**，服务器实现模式为一个连接一个线程
  如果这个连接不做任何事情会造成不必要的线程开销，当然可以通过线程池机制改善。
  BIO方式适用于连接数目比较小且固定的架构，这种方式对服务器资源要求比较高，并发局限于应用中。
<a href="https://smms.app/image/LnWFysMRue63GE2" target="_blank"><img src="https://s2.loli.net/2023/12/15/LnWFysMRue63GE2.jpg" ></a>
#### 服务端：
```Java

public static void main(String args[]) throws IOException {

	//新建连接
    ServerSocket serverSocket = new ServerSocket(30888);
    System.out.println("Start accept...");
    Socket socket = serverSocket.accept();
    
    //缓冲区读入
    BufferedReader reader = new BufferedReader(new InputStreamReader(socket.getInputStream()));
    PrintWriter writer = new PrintWriter(socket.getOutputStream());
    writer.println("Connection Success!");
    writer.flush();
    
    //关闭读取
    reader.close();
    writer.close();
    socket.close();
    serverSocket.close();
}
```


#### 客户端：
```Java

public static void main(String args[]) throws IOException, UnknownHostException {
	//新建连接
    Socket socket = new Socket(InetAddress.getLocalHost(), 30888);
    PrintWriter writer = new PrintWriter(socket.getOutputStream());
    
    //输入
    Scanner scanner = new Scanner(System.in);
    String readline = "";
    
    //单方法使用lamda表达式
    Thread thread = new Thread(() -> {
        int size = -1;
        byte[] bytes = new byte[1024];
        StringBuilder sb = new StringBuilder(1024);
        try {
        	//执行代码
        } catch (IOException e) {
            e.printStackTrace();
        }
    });
    
    //开启线程，关闭连接
    thread.start();
    scanner.close();
    writer.close();
    socket.close();
    thread.interrupt();
}
```


## 伪异步IO
  BIO模式的服务端是1:1的线程开销，访问量越大，系统将发生线程栈溢线程创建失败，最终导致进程宕机或者僵死，从而不能对外提供服务。
  伪异步IO的通信采用线程池和任务队列实现，当客户端接入时，将客户端的Socket封装成一个Task交给后端的线程池中进行处理。
  JDK的线程池维护一个消息队列和N个活跃的线程，对消息队列中Socket任务进行处理，由于线程池可以设置消息队列的大小和最大线程数，因此，它的资源占用是可控的，无论多少个客户端并发访问，都不会导致资源的耗尽和宕机。
  伪异步io采用了线程池实现，因此避免了为每个请求创建一个独立线程造成线程资源耗尽的问题，但由于底层依然是采用的同步阻塞模型，因此无法从根本上解决问题。
  如果单个消息处理的缓慢，或者服务器线程池中的全部线程都被阻塞，那么后续scket的io消息都将在队列中排排队。新的Socket请求将被拒绝，客户端会发生大量连接超时。
<a href="https://smms.app/image/6MEzeTd9OHurkcY" target="_blank"><img src="https://s2.loli.net/2023/12/15/6MEzeTd9OHurkcY.jpg" ></a>


## NIO
**同步非阻塞**，服务器实现模式为一个请求一个线程
  多路复用器轮询到连接有I/O请求时才启动一个线程进行处理。
  NIO 新增了 Channel、Selector、Buffer 等抽象概念，支持面向缓冲、基于通道的 I/O 操作方法。
  NIO方式适用于连接数目多且连接比较短（轻操作）的架构，比如聊天服务器，并发局限于应用中。
<a href="https://smms.app/image/f9czQpCtwxXGyae" target="_blank"><img src="https://s2.loli.net/2023/12/15/f9czQpCtwxXGyae.jpg" ></a>

- **通道channel：** 绑定一个缓存区，缓存区与磁盘文件的相互传输依靠 channel 双向通道。
- **缓存区Buffer：** Buffer 就像一个数组，可以保存多个相同类型的数据。BIO的Buffer只能单向，NIO可以双向。
- **选择器Selector（多路复用器）：** 将 Channel 注册之后可用于检查一个或多个 Channel 的状态是否处于连接就绪、接受就绪、可读就绪、可写就绪。

#### 直接缓冲区与非直接缓冲区
<a href="https://smms.app/image/FQq3BDO7KityXRU" target="_blank"><img src="https://s2.loli.net/2023/12/15/FQq3BDO7KityXRU.jpg" ></a>
  **应用程序想从磁盘中读取一个数据时会向操作系统发起一个读请求，首先磁盘中的数据被读取到内核地址空间中，然后会把内核地址空间中的数据拷贝到用户地址空间中（其实就是 JVM 内存中），最后再把这个数据读取到应用程序中来。**

<a href="https://smms.app/image/Wa9jEf7qu5UyOJt" target="_blank"><img src="https://s2.loli.net/2023/12/15/Wa9jEf7qu5UyOJt.jpg" ></a>
**直接用物理内存作为缓冲区，读写数据直接通过物理内存进行。**

#### 分散读取与聚集写入
  分散读取是指从 Channel 中读取的数据按照缓冲区的顺序 "分散" 到多个 Buffer 中
  聚集写入是指将多个 Buffer 中的数据按照缓冲区的顺序 "聚集" 到 Channel
```Java

public static void test4() throws Exception {
    RandomAccessFile raf = new RandomAccessFile("a.txt", "rw");
    // 获取通道
    FileChannel channel = raf.getChannel();
    // 分配指定大小缓冲区
    ByteBuffer buf1 = ByteBuffer.allocate(2);
    ByteBuffer buf2 = ByteBuffer.allocate(1024);
    // 分散读取
    ByteBuffer[] bufs = {buf1, buf2};
    channel.read(bufs);  // 参数需要一个数组
    for (ByteBuffer byteBuffer : bufs) {
        byteBuffer.flip();  // 切换到读模式
    }
    System.out.println(new String(bufs[0].array(), 0, bufs[0].limit()));  // 打印 he
    System.out.println(new String(bufs[1].array(), 0, bufs[1].limit()));  // 打印 llo

    // 聚集写入
    RandomAccessFile raf2 = new RandomAccessFile("e.txt","rw");
    // 获取通道
    FileChannel channel2 = raf2.getChannel();
    channel2.write(bufs);  // 把 bufs 里面的几个缓冲区聚集到 channel2 这个通道中，聚集到通道中，也就是到了 e.txt 文件中
    channel2.close();
}
```

#### 服务端
```Java

public static void main(String[] args) throws  Exception{

        //创建ServerSocketChannel，-->> ServerSocket
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        InetSocketAddress inetSocketAddress = new InetSocketAddress(5555);
        serverSocketChannel.socket().bind(inetSocketAddress);
        
        //设置成非阻塞
        serverSocketChannel.configureBlocking(false); 
 
        //开启selector,并注册accept事件
        Selector selector = Selector.open();
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        
        //循环接收请求
        while(true) {
            selector.select(2000);  //监听所有通道
            //遍历selectionKeys
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            Iterator<SelectionKey> iterator = selectionKeys.iterator();
            while (iterator.hasNext()) {
                SelectionKey key = iterator.next();
                
                //处理连接事件
                if(key.isAcceptable()) {  
                    SocketChannel socketChannel = serverSocketChannel.accept();
                    
                    //设置为非阻塞
                    socketChannel.configureBlocking(false);  
                    System.out.println("client:" + socketChannel.getLocalAddress() + " is connect");
                    
                    //注册客户端读取事件到selector
                    socketChannel.register(selector, SelectionKey.OP_READ); 
                } else if (key.isReadable()) {  //处理读取事件
                    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
                    SocketChannel channel = (SocketChannel) key.channel();
                    channel.read(byteBuffer);
                    System.out.println("client:" + channel.getLocalAddress() + " send " + new String(byteBuffer.array()));
                }
                iterator.remove();  //事件处理完毕，要记得清除
            }
        }
 
    }
```

#### 客户端
```Java

public class NIOClient {
 
	public static void main(String[] args) throws Exception{
       		SocketChannel socketChannel = SocketChannel.open();
                    
        	//设置成非阻塞
        	socketChannel.configureBlocking(false);
        	InetSocketAddress inetSocketAddress = new InetSocketAddress("127.0.0.1", 5555);
 
        	if(!socketChannel.connect(inetSocketAddress)) {
            	while (!socketChannel.finishConnect()) {
                	System.out.println("客户端正在连接中，请耐心等待");
            	}
        	}
 
        	ByteBuffer byteBuffer = ByteBuffer.wrap("mikechen的互联网架构".getBytes());
        	socketChannel.write(byteBuffer);
        	socketChannel.close();
	}
}
```


## AIO
**异步非阻塞**，服务器实现模式为一个有效请求一个线程

  Java AIO 采用订阅-通知模式：即应用程序向操作系统注册IO监听，然后继续做自己的事情。当操作系统发生IO事件，并且准备好数据后，在主动通知应用程序，触发相应的函数。

客户端的I/O请求都是由OS先完成了再通知服务器应用去启动线程进行处理。

  AIO方式使用于连接数目多且连接比较长（重操作）的架构，比如相册服务器，充分调用OS参与并发操作。

***IO模型是由操作系统提供支持的***

  windows系统提供了一种异步IO技术：`IOCP`（I/O CompletionPort，I/O完成端口）
  Linux下由于没有这种异步IO技术，所以使用 `epoll` 对异步IO进行模拟。

代码层面如下图所示结构。
<a href="https://smms.app/image/r5iY9NuJVtgOBQx" target="_blank"><img src="https://s2.loli.net/2023/12/15/r5iY9NuJVtgOBQx.jpg" ></a>


## 三者比较

<a href="https://smms.app/image/YHS1Ixk3isQnJWa" target="_blank"><img src="https://s2.loli.net/2023/12/15/YHS1Ixk3isQnJWa.jpg" ></a>


## 后记
  **马上期末了，这两天把java剩下的反射和容器写完，然后——再鸽一段时间 hhh**