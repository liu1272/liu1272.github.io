title: Java网络编程
author: liuGuobin
tags:
  - 日报
categories:
  - 日报
date: 2023-12-14 19:28:00
---
## 前言
  **这篇文章我觉得我对于网络基础部分就不用再说更多了，直接一张图就够。剩下的用代码和注释来说明，看懂就是学会。**
<a href="https://smms.app/image/Yv5mfec4P3sdFHt" target="_blank"><img src="https://s2.loli.net/2023/12/14/Yv5mfec4P3sdFHt.png" ></a>

## 单播通信
### TCP通信
#### 服务端
```Java

public class Sever {
    public static void main(String[] args) {
        ServerSocket serverSocket = null;   //初始化定义为空保证安全
        Socket socket = null;
        InputStream is = null;
        ByteArrayOutputStream baos = null;

        try {
            //创建ServerSocket对象，监听9999端口
            serverSocket = new ServerSocket(9999);

            //等待客户端的连接请求，返回一个Socket对象，程序阻塞在此处直到有客户端连接
            socket = serverSocket.accept();

            //获取输入流，用于读取客户端发送的数据
            is = socket.getInputStream();

            //创建字节数组输出流，用于存储读取到的数据
            baos = new ByteArrayOutputStream();

            //预定义缓冲区数组大小，方便存储数据
            byte[] buffer = new byte[1024];
            int len;

            //循环读取客户端发送的数据，将数据存储到字节数组输出流中
            while((len = is.read(buffer))!=-1){
            	//要写入的字节数组，从第一个元素开始写入，写入的字节数量
                baos.write(buffer,0,len);  
            }

            //将字节数组输出流中的数据转换为字符串并输出到控制台
            System.out.println(baos.toString());

            //关闭字节数组输出流和输入流
            baos.close();
            is.close();

            //关闭Socket对象和ServerSocket对象
            socket.close();
            serverSocket.close();
        } catch (IOException e){
            e.printStackTrace(); //打印异常信息
        }
    }
}
```

#### 客户端
```Java

public class Cilent {
    public static void main(String[] args) {
        Socket socket = null;   // 用于建立客户端与服务器之间的套接字连接
        OutputStream os = null; // 用于向服务器发送数据的输出流

        try {
        	
            // 通过IP地址获取服务器的InetAddress实例
            InetAddress serverIP = InetAddress.getByName("127.0.0.1"); 
            int port = 9999; // 连接服务器的端口号

            // 创建套接字连接至服务器
            socket = new Socket(serverIP, port);

            // 通过套接字获取输出流，用于向服务器发送数据
            os = socket.getOutputStream();

            // 向服务器发送数据，需要
            os.write("Hello, world!".getBytes());  
            //getBytes()方法编码为 byte 序列，并将结果存储到一个新的 byte 数组中

        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            if (socket != null) {
                try {
                    socket.close(); // 关闭套接字连接
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```


### UDP通信
#### 服务端
```Java

public class Sever {
    public static void main(String[] args) throws Exception {
    
        //创建DatagramSocket对象，监听6666端口
        DatagramSocket socket = new DatagramSocket(6666);
        
        //一直循环接收信息
        while (true) {
            byte[] container = new byte[1024];
            
            //创建DatagramPacket对象，用于接收客户端发送的数据（合成一个包）
            DatagramPacket packet = new DatagramPacket(container,0,container.length);
            //要发送或接收的数据的字节数组，从头开始读取或写入，读取或写入的字节数
            
            //接收客户端发送的数据
            socket.receive(packet);
            byte[] data = packet.getData();
            //获取接收到的数据，返回一个字节数组
            
            //将接收到的字节数组（包）转换为字符串
            String receiveDatas = new String(data,0,packet.getLength());
            System.out.println(receiveDatas);
            
            //判断是否输入了“bye”，如果是则退出循环
            if (receiveDatas.trim().equals("bye")) {
                break;
            }
        }
        
        //关闭DatagramSocket对象
        socket.close();
    }
}
```

#### 客户端
```Java

public class Cilent {
    public static void main(String[] args) throws Exception {
        try {
        
            //创建DatagramSocket对象，监听8888端口
            DatagramSocket socket = new DatagramSocket(8888);
            
            //创建BufferedReader对象，用于从控制台读取数据
            BufferedReader reader = new BufferedReader(new InputStreamReader(System.in));
                    
        	//一直循环接收信息
            while (true) {
            
                //从控制台读取数据
                String data = reader.readLine();
                
                //将读取到的字符串转换为字节数组
                byte[] datas = data.getBytes();
                
                //创建DatagramPacket对象，打包好数据，指定发送的目标地址和端口号
                DatagramPacket packet = new DatagramPacket(datas,0,datas.length,new InetSocketAddress("127.0.0.1",6666));
                
                //发送数据
                socket.send(packet);
                
                //判断是否输入了“bye”，如果是则退出循环
                if (data.equals("bye")) {
                    break;
                }
            }
            
            //关闭DatagramSocket对象
            socket.close();
        } catch (SocketException e) {
            e.printStackTrace();
        }
    }
}
```


### 多线程通信
#### 服务端
```Java

//继承Runnable接口
public class Sever implements Runnable {   
    DatagramSocket socket = new DatagramSocket(6666);
    private int port;
    private String msgfrom;
    public Sever(int port,String msgfrom) throws IOException {
        this.port = port;
        this.msgfrom = msgfrom;
        socket = new DatagramSocket(6666);
    }
    
    //重写run()方法
    public void run() {   
        try {
            while (true) {
                byte[] container = new byte[1024];

                //创建DatagramPacket对象，用于接收客户端发送的数据
                DatagramPacket packet = new DatagramPacket(container,0,container.length);

                //接收客户端发送的数据
                socket.receive(packet);
                byte[] data = packet.getData();

                //将接收到的字节数组转换为字符串
                String receiveDatas = new String(data,0,data.length);
                System.out.println(msgfrom + ":" + receiveDatas);

                //判断是否输入了“bye”，如果是则退出循环
                //trim()去除首尾空格，equals()比较对象，被比较的放前面
                if (receiveDatas.trim().equals("bye")) {
                    break;
                }
            }

            //关闭DatagramSocket对象
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

#### 客户端
```Java

public class Cilent implements Runnable {
    DatagramSocket socket = null;
    BufferedReader reader = null;
    private String toIP;
    private int fromport;
    private int toport;
    public Cilent(int fromIP,String toIP,int toport) throws SocketException {
        this.fromport = fromport;
        this.toport = toport;
        this.toIP = toIP;

        //创建DatagramSocket对象，监听指定端口
        socket = new DatagramSocket(fromport);

        //创建BufferedReader对象，用于从控制台读取数据
        reader = new BufferedReader(new InputStreamReader(System.in));
    }
    public void run() {
        try {
            while (true) {

                //从控制台读取数据
                String data = reader.readLine();

                //将读取到的字符串转换为字节数组
                byte[] datas = data.getBytes();

                //创建DatagramPacket对象，指定发送的目标地址和端口号
                DatagramPacket packet = new DatagramPacket(datas,0,datas.length,new InetSocketAddress(this.toIP,this.toport));

                //发送数据
                socket.send(packet);

                //判断是否输入了“bye”，如果是则退出循环
                if (data.equals("bye")) {
                    break;
                }
            }

            //关闭DatagramSocket对象
            socket.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```


## 广播通信
### 服务端
```Java

public class Sever {
    public static void main(String args[]) {
        int port = 5858;
        InetAddress group = null;
        MulticastSocket socket = null;
        try {

            //创建InetAddress对象，指定广播地址
            group = InetAddress.getByName("127.0.0.1");

            //创建MulticastSocket对象，监听指定端口
            socket = new MulticastSocket(port);

            //创建InetSocketAddress对象，指定广播地址和端口
            InetSocketAddress socketAddress = new InetSocketAddress(group,port);

            //获取指定网卡的NetworkInterface对象
            NetworkInterface networkInterface = NetworkInterface.getByInetAddress(group);

            //加入广播组
            socket.joinGroup(socketAddress,networkInterface);
        } catch (Exception e) {
            e.printStackTrace();
        }
        while (true) {
            byte data[] = new byte[8192];
            DatagramPacket packet = null;

            //创建DatagramPacket对象，用于接收广播内容
            packet = new DatagramPacket(data,data.length,group,port);
            try {
                socket.receive(packet);

                //将接收到的字节数组转换为字符串
                String message = new String(packet.getData(),0,packet.getLength());
                System.out.println("接收的内容：" + message);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
}
```

### 客户端
```Java

public class Cilent {
    // Broadcast
    String s = "abcd";
    int port = 8989;
    InetAddress group = null;
    MulticastSocket socket = null;
    Cilent() {
        try {

            //创建InetAddress对象，指定广播地址（路由器指定的）
            group = InetAddress.getByName("255.255.255.255");

            //创建MulticastSocket对象，监听指定端口
            //  (注意，从这里开始DatagramSocket已经变成了MulticastSocket)
            socket = new MulticastSocket(port);

            //设置TTL值为1，表示只在本地网络中广播，TTL值是该字段指定IP包被路由器丢弃之前允许通过的最大网段数量，1代表只能通过一个网段
            socket.setTimeToLive(1);

            //创建InetSocketAddress对象，指定广播地址和端口
            InetSocketAddress socketAddress = new InetSocketAddress(group,port);

            //获取指定网卡的NetworkInterface对象
            NetworkInterface networkInterface = NetworkInterface.getByInetAddress(group);

            //加入广播组
            socket.joinGroup(socketAddress,networkInterface);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public void play() {
        while (true) {
            try {
                DatagramPacket packet = null;
                byte data[] = s.getBytes();

                //创建DatagramPacket对象，指定发送的目标地址和端口号
                packet = new DatagramPacket(data,data.length,group,port);
                System.out.println(new String(data));

                //发送数据
                socket.send(packet);
                Thread.sleep(2000);
            } catch (Exception e) {
                e.printStackTrace();
            }
        }
    }
    public static void main(String args[]) {
        new Cilent().play();
    }
}
```


## 多播通信
### 服务器接收端程序
```Java

public class TestMain {
    private static MulticastSocket ds;   //多播套接字对象
    static String multicastHost="239.0.0.255";   //多播组地址
    static InetAddress receiveAddress;   //接收地址对象

    public static void main(String[] args) throws IOException {
    
        // 创建多播套接字，绑定到本地的8899端口
        ds = new MulticastSocket(8899);  
        
        // 获取多播组地址
        receiveAddress=InetAddress.getByName(multicastHost); 
        
        // 将套接字加入多播组
        ds.joinGroup(receiveAddress);
        
        // 开启一个新线程，并将多播套接字对象传递给udpRunnable对象
        new Thread(new udpRunnable(ds)).start();  
    }
}

class udpRunnable implements Runnable {
    MulticastSocket ds;

    public udpRunnable(MulticastSocket ds) {
        this.ds=ds;
    }

    public void run() {
    
        // 创建一个用于接收数据的缓冲区
        byte buf[] = new byte[1024];  
        DatagramPacket dp = new DatagramPacket(buf, 1024);  
        while (true) {  
            try {  
            
                // 从多播套接字中接收数据，并将数据存储到数据包对象中
                ds.receive(dp);  
                
                // 打印接收到的客户端消息
                System.out.println("receive client message : "+new String(buf, 0, dp.getLength()));  
            } catch (Exception e) {  
                e.printStackTrace();  
            }  
        }  
    }
}
```

### 客户发送端
```Java

public class SendUdp {
    public static void main(String[] args) throws IOException {
        MulticastSocket ms=null; 
        DatagramPacket dataPacket = null; 
        ms = new MulticastSocket();
        
        // 设置多播数据包的生存时间为32
        ms.setTimeToLive(32);  
        
        // 将字符串 "组播 测试" 转换为字节数组 data
        byte[] data = "组播 测试".getBytes();   
        
        // 获取多播组地址 address 对象
        InetAddress address = InetAddress.getByName("239.0.0.255");  
        
        // 创建数据包 dataPacket，指定数据、数据长度、目标地址和端口
        dataPacket = new DatagramPacket(data, data.length, address,8899); 
        
        // 发送数据包
        ms.send(dataPacket);  
        
        // 关闭套接字
        ms.close();   
    }
}
```


## IP地址分级
之前比赛写过这个还不是很了解，这里记一下

| 分类 | 范围   |
|------|--------|
| A | 0.0.0.0-127.255.255.255   |
| B | 128.0.0.0-191.255.255.255   |
| C | 192.0.0.0-223.255.255.255   |
| D | 224.0.0.0-239.255.255.255   |
| E | 240.0.0.0-255.255.255.255   |


## 后记
  发现一个网站，大家可以[进来玩玩](https://decomytree.com/home?hashedId=3L5dBvOjX-Kd)

<a href="https://smms.app/image/73ArcZKfJHBeW8o" target="_blank"><img src="https://s2.loli.net/2023/12/14/73ArcZKfJHBeW8o.jpg" width="550"></a>