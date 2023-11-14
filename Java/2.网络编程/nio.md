# nio

[nio](https://mp.weixin.qq.com/s/GfV9w2B0mbT7PmeBS45xLw)

1. `java.nio.*` 非阻塞式 io,面向缓冲区而不是 stream
2. 缓冲区 Buffer,ByteBuffer,CharBuffer,DoubleBuffer,FloatBuffer,IntBuffer,LongBuffer,MappedByteBuffer(直接字节缓冲区，其内容是文件的内存映射区域),ShortBuffer
3. 通道 channel
4. 选择器 selector

# 缓冲区

## 创建

一般我们常用的类型是 ByteBuffer，把数据转成字节进行处理。实质上是一个 byte[]数组。

```Java
//创建jvm堆内内存块HeapByteBuffer
ByteBuffer byteBuffer1 = ByteBuffer.allocate(1024);
String msg = "java技术爱好者";
//包装一个byte[]数组获得一个Buffer，实际类型是HeapByteBuffer
ByteBuffer byteBuffer2 = ByteBuffer.wrap(msg.getBytes());

//创建内存块DirectByteBuffer
/**
DirectByteBuffer的使用场景：
  1. java程序与本地磁盘、socket传输数据
  2. 大文件对象，可以使用。不会受到堆内存大小的限制。
  3. 不需要频繁创建，生命周期较长的情况，能重复使用的情况。
没有达到一定的量级，实际上使用DirectByteBuffer是体现不出优势的
 */
ByteBuffer byteBuffer3 = ByteBuffer.allocateDirect(1024);
```

## 基本使用

1. 创建
2. 写入数据`put()`
3. 切换读写模式`flip()`;缓存区是双向的，既可以往缓冲区写入数据，也可以从缓冲区读取数据。但是不能同时进行，需要切换。
4. 读取数据`hasRemaining() | get()`

```Java
public static void main(String[] args) throws Exception {
    String msg = "java技术爱好者，起飞！";
    //创建一个固定大小的buffer(返回的是HeapByteBuffer)
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
    byte[] bytes = msg.getBytes();
    //写入数据到Buffer中
    byteBuffer.put(bytes);
    //切换成读模式，关键一步
    byteBuffer.flip();
    //创建一个临时数组，用于存储获取到的数据
    byte[] tempByte = new byte[bytes.length];
    int i = 0;
    //如果还有数据，就循环。循环判断条件
    while (byteBuffer.hasRemaining()) {
        //获取byteBuffer中的数据
        byte b = byteBuffer.get();
        //放到临时数组中
        tempByte[i] = b;
        i++;
    }
    //打印结果
    System.out.println(new String(tempByte));//java技术爱好者，起飞！
}
```

## 参数

```Java
//位置，默认是从第一个开始
private int position = 0;
//限制，不能读取或者写入的位置索引
private int limit;
//容量，缓冲区所包含的元素的数量
private int capacity;
```

flip()方法的源码如下：

```Java
//通过控制position和limit的值来控制读写的数据。
public final Buffer flip() {
    limit = position;
    position = 0;
    mark = -1;
    return this;
}

byteBuffer.hasRemaining();
public final boolean hasRemaining() {
    //判断position的索引是否小于limit。
    //所以可以看出limit的作用就是记录写入数据的位置，那么当读取数据时，就知道读到哪个位置
    return position < limit;
}
```

# 通道

Channel 本身并不存储数据，只是负责数据的运输。必须要和 Buffer 一起使用。

常用的 Channel 有这四种：

FileChannel，读写文件中的数据。
SocketChannel，通过 TCP 读写网络中的数据。
ServerSockectChannel，监听新进来的 TCP 连接，像 Web 服务器那样。对每一个新进来的连接都会创建一个 SocketChannel。
DatagramChannel，通过 UDP 读写网络中的数据。

`FileChannel`:

1. 从输入流获取输入流通道`inputStream.getChannel()`
2. 从输出流获取输出流通道`outputStream.getChannel()`
3. 建立缓冲区
4. 把输入流通道的数据读取到缓冲区
5. 缓冲区切换成读模式
6. 把数据从缓冲区写入到输出流通道
7. 关闭输入流|输出流|输入流通道|输出流通道

```Java
public static void main(String[] args) throws Exception {
    //获取文件输入流
    File file = new File("1.txt");
    FileInputStream inputStream = new FileInputStream(file);
    //从文件输入流获取通道
    FileChannel inputStreamChannel = inputStream.getChannel();
    //获取文件输出流
    FileOutputStream outputStream = new FileOutputStream(new File("2.txt"));
    //从文件输出流获取通道
    FileChannel outputStreamChannel = outputStream.getChannel();
    //创建一个byteBuffer，小文件所以就直接一次读取，不分多次循环了
    ByteBuffer byteBuffer = ByteBuffer.allocate((int)file.length());
    //把输入流通道的数据读取到缓冲区
    inputStreamChannel.read(byteBuffer);
    //切换成读模式
    byteBuffer.flip();
    //把数据从缓冲区写入到输出流通道
    outputStreamChannel.write(byteBuffer);
    //关闭通道
    outputStream.close();
    inputStream.close();
    outputStreamChannel.close();
    inputStreamChannel.close();
}
```

`SocketChannel`:

```Java
public static void main(String[] args) throws Exception {
  //获取ServerSocketChannel
  ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
  InetSocketAddress address = new InetSocketAddress("127.0.0.1", 6666);
  //绑定地址，端口号
  serverSocketChannel.bind(address);
  //创建一个缓冲区
  ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
  while (true) {
      //获取SocketChannel
      SocketChannel socketChannel = serverSocketChannel.accept();
      while (socketChannel.read(byteBuffer) != -1){
          //打印结果
          System.out.println(new String(byteBuffer.array()));
          //清空缓冲区
          byteBuffer.clear();
      }
  }
}
```

## 通道间数据传输

```Java
// transferTo()：把源通道的数据传输到目的通道中。
public static void main(String[] args) throws Exception {
    //获取文件输入流
    File file = new File("1.txt");
    FileInputStream inputStream = new FileInputStream(file);
    //从文件输入流获取通道
    FileChannel inputStreamChannel = inputStream.getChannel();
    //获取文件输出流
    FileOutputStream outputStream = new FileOutputStream(new File("2.txt"));
    //从文件输出流获取通道
    FileChannel outputStreamChannel = outputStream.getChannel();
    //创建一个byteBuffer，小文件所以就直接一次读取，不分多次循环了
    ByteBuffer byteBuffer = ByteBuffer.allocate((int) file.length());
    //把输入流通道的数据读取到输出流的通道
    inputStreamChannel.transferTo(0, byteBuffer.limit(), outputStreamChannel);
    //关闭通道
    outputStream.close();
    inputStream.close();
    outputStreamChannel.close();
    inputStreamChannel.close();
}
//transferFrom()：把来自源通道的数据传输到目的通道。
public static void main(String[] args) throws Exception {
    //获取文件输入流
    File file = new File("1.txt");
    FileInputStream inputStream = new FileInputStream(file);
    //从文件输入流获取通道
    FileChannel inputStreamChannel = inputStream.getChannel();
    //获取文件输出流
    FileOutputStream outputStream = new FileOutputStream(new File("2.txt"));
    //从文件输出流获取通道
    FileChannel outputStreamChannel = outputStream.getChannel();
    //创建一个byteBuffer，小文件所以就直接一次读取，不分多次循环了
    ByteBuffer byteBuffer = ByteBuffer.allocate((int) file.length());
    //把输入流通道的数据读取到输出流的通道
    outputStreamChannel.transferFrom(inputStreamChannel,0,byteBuffer.limit());
    //关闭通道
    outputStream.close();
    inputStream.close();
    outputStreamChannel.close();
    inputStreamChannel.close();
}
```

## 分散读取和聚合写入

使用场景就是可以使用一个缓冲区数组，自动地根据需要去分配缓冲区的大小。可以减少内存消耗。

```Java
/**
从源码中可以看出实现了GatheringByteChannel, ScatteringByteChannel接口。也就是支持分散读取和聚合写入的操作。
*/
public abstract class FileChannel extends AbstractInterruptibleChannel
    implements SeekableByteChannel, GatheringByteChannel, ScatteringByteChannel {
}
```

1.txt 文件内容: `abcdefghijklmnopqrstuvwxyz//26个字母`

```Java
public static void main(String[] args) throws Exception {
  //获取文件输入流
  File file = new File("1.txt");
  FileInputStream inputStream = new FileInputStream(file);
  //从文件输入流获取通道
  FileChannel inputStreamChannel = inputStream.getChannel();
  //获取文件输出流
  FileOutputStream outputStream = new FileOutputStream(new File("2.txt"));
  //从文件输出流获取通道
  FileChannel outputStreamChannel = outputStream.getChannel();
  //创建三个缓冲区，分别都是5
  ByteBuffer byteBuffer1 = ByteBuffer.allocate(5);
  ByteBuffer byteBuffer2 = ByteBuffer.allocate(5);
  ByteBuffer byteBuffer3 = ByteBuffer.allocate(5);
  //创建一个缓冲区数组
  ByteBuffer[] buffers = new ByteBuffer[]{byteBuffer1, byteBuffer2, byteBuffer3};
  //循环写入到buffers缓冲区数组中，分散读取
  long read;
  long sumLength = 0;
  while ((read = inputStreamChannel.read(buffers)) != -1) {
      sumLength += read;
      Arrays.stream(buffers)
              .map(buffer -> "posstion=" + buffer.position() + ",limit=" + buffer.limit())
              .forEach(System.out::println);
      //切换模式
      Arrays.stream(buffers).forEach(Buffer::flip);
      //聚合写入到文件输出通道
      outputStreamChannel.write(buffers);
      //清空缓冲区
      Arrays.stream(buffers).forEach(Buffer::clear);
  }
  System.out.println("总长度:" + sumLength);
  //关闭通道
  outputStream.close();
  inputStream.close();
  outputStreamChannel.close();
  inputStreamChannel.close();
}
/**
posstion=5,limit=5
posstion=5,limit=5
posstion=5,limit=5

posstion=5,limit=5
posstion=5,limit=5
posstion=1,limit=5

总长度:26

可以看到循环了两次。第一次循环时，三个缓冲区都读取了5个字节，总共读取了15，也就是读满了。还剩下11个字节，于是第二次循环时，前两个缓冲区分配了5个字节，最后一个缓冲区给他分配了1个字节，刚好读完。总共就是26个字节。
*/
```

# 选择器

Selector 翻译成选择器,有些人也会翻译成多路复用器

只有网络 IO 才会使用选择器，文件 IO 是不需要使用的。

选择器可以说是 NIO 的核心组件，它可以监听通道的状态，来实现异步非阻塞的 IO。换句话说，也就是事件驱动。以此实现单线程管理多个 Channel 的目的。

`Selector.open()` 打开一个选择器。
`select()` 选择一组键，其相应的通道已为 I/O 操作准备就绪。
`selectedKeys()` 返回此选择器的已选择键集。

```Java
//在SelectionKey类中有四个常量表示四种事件，来看源码：
public abstract class SelectionKey {
    //读事件
    public static final int OP_READ = 1 << 0; //2^0=1
    //写事件
    public static final int OP_WRITE = 1 << 2; // 2^2=4
    //连接操作,Client端支持的一种操作
    public static final int OP_CONNECT = 1 << 3; // 2^3=8
    //连接可接受操作,仅ServerSocketChannel支持
    public static final int OP_ACCEPT = 1 << 4; // 2^4=16
}
//附加的对象(可选)，把通道注册到选择器中时可以附加一个对象。
public final SelectionKey register(Selector sel, int ops, Object att)
//从selectionKey中获取附件对象可以使用attachment()方法
public final Object attachment() {
    return attachment;
}
```

## 例子

```java
public class NIOServer {
    public static void main(String[] args) throws Exception {
        //打开一个ServerSocketChannel
        ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
        InetSocketAddress address = new InetSocketAddress("127.0.0.1", 6666);
        //绑定地址
        serverSocketChannel.bind(address);
        //设置为非阻塞
        serverSocketChannel.configureBlocking(false);
        //打开一个选择器
        Selector selector = Selector.open();
        //serverSocketChannel注册到选择器中,监听连接事件
        serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        //循环等待客户端的连接
        while (true) {
            //等待3秒，（返回0相当于没有事件）如果没有事件，则跳过
            if (selector.select(3000) == 0) {
                System.out.println("服务器等待3秒，没有连接");
                continue;
            }
            //如果有事件selector.select(3000)>0的情况,获取事件
            Set<SelectionKey> selectionKeys = selector.selectedKeys();
            //获取迭代器遍历
            Iterator<SelectionKey> it = selectionKeys.iterator();
            while (it.hasNext()) {
                //获取到事件
                SelectionKey selectionKey = it.next();
                //判断如果是连接事件
                if (selectionKey.isAcceptable()) {
                    //服务器与客户端建立连接，获取socketChannel
                    SocketChannel socketChannel = serverSocketChannel.accept();
                    //设置成非阻塞
                    socketChannel.configureBlocking(false);
                    //把socketChannel注册到selector中，监听读事件，并绑定一个缓冲区
                    socketChannel.register(selector, SelectionKey.OP_READ, ByteBuffer.allocate(1024));
                }
                //如果是读事件
                if (selectionKey.isReadable()) {
                    //获取通道
                    SocketChannel socketChannel = (SocketChannel) selectionKey.channel();
                    //获取关联的ByteBuffer
                    ByteBuffer buffer = (ByteBuffer) selectionKey.attachment();
                    //打印从客户端获取到的数据
                    socketChannel.read(buffer);
                    System.out.println("from 客户端：" + new String(buffer.array()));
                }
                //从事件集合中删除已处理的事件，防止重复处理
                it.remove();
            }
        }
    }
}
public class NIOClient {
    public static void main(String[] args) throws Exception {
        SocketChannel socketChannel = SocketChannel.open();
        InetSocketAddress address = new InetSocketAddress("127.0.0.1", 6666);
        socketChannel.configureBlocking(false);
        //连接服务器
        boolean connect = socketChannel.connect(address);
        //判断是否连接成功
        if(!connect){
            //等待连接的过程中
            while (!socketChannel.finishConnect()){
                System.out.println("连接服务器需要时间，期间可以做其他事情...");
            }
        }
        String msg = "hello java技术爱好者！";
        ByteBuffer byteBuffer = ByteBuffer.wrap(msg.getBytes());
        //把byteBuffer数据写入到通道中
        socketChannel.write(byteBuffer);
        //让程序卡在这个位置，不关闭连接
        System.in.read();
    }
}
```

# 应用: 多人聊天室

1. 启动服务端
2. 启动多个客户端

## 服务端

1. init
   1. 初始化一个选择器`Selector`,
   2. 打开一个非阻塞的服务器 socket 通道`ServerSocketChannel`,绑定端口,
   3. 把通道(OP_ACCEPT)注册到选择器中
2. 监听客户端消息
   1. 死循环
   2. 轮询`selector.select(2000)`
   3. 有事件就遍历
      1. 连接事件,进行连接获取 SocketChannel,并把通道(OP_READ)注册到选择器中
      2. 可读事件
      3. 事件处理完毕,删除

```Java
public class GroupChatServer {
    private Selector selector;
    private ServerSocketChannel serverSocketChannel;
    public static final int PORT = 6667;
    //构造器初始化成员变量
    public GroupChatServer() {
        try {
            //打开一个选择器
            this.selector = Selector.open();
            //打开serverSocketChannel
            this.serverSocketChannel = ServerSocketChannel.open();
            //绑定地址，端口号
            this.serverSocketChannel.bind(new InetSocketAddress("127.0.0.1", PORT));
            //设置为非阻塞
            serverSocketChannel.configureBlocking(false);
            //把通道注册到选择器中
            serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    /**
     * 监听，并且接受客户端消息，转发到其他客户端
     */
    public void listen() {
        try {
            while (true) {
                //获取监听的事件总数
                int count = selector.select(2000);
                if (count > 0) {
                    Set<SelectionKey> selectionKeys = selector.selectedKeys();
                    //获取SelectionKey集合
                    Iterator<SelectionKey> it = selectionKeys.iterator();
                    while (it.hasNext()) {
                        SelectionKey key = it.next();
                        //如果是获取连接事件
                        if (key.isAcceptable()) {
                            SocketChannel socketChannel = serverSocketChannel.accept();
                            //设置为非阻塞
                            socketChannel.configureBlocking(false);
                            //注册到选择器中
                            socketChannel.register(selector, SelectionKey.OP_READ);
                            System.out.println(socketChannel.getRemoteAddress() + "上线了~");
                        }
                        //如果是读就绪事件
                        if (key.isReadable()) {
                            //读取消息，并且转发到其他客户端
                            readData(key);
                        }
                        it.remove();
                    }
                } else {
                    System.out.println("等待...");
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //获取客户端发送过来的消息
    private void readData(SelectionKey selectionKey) {
        SocketChannel socketChannel = null;
        try {
            //从selectionKey中获取channel
            socketChannel = (SocketChannel) selectionKey.channel();
            //创建一个缓冲区
            ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
            //把通道的数据写入到缓冲区
            int count = socketChannel.read(byteBuffer);
            //判断返回的count是否大于0，大于0表示读取到了数据
            if (count > 0) {
                //把缓冲区的byte[]转成字符串
                String msg = new String(byteBuffer.array());
                //输出该消息到控制台
                System.out.println("from 客户端：" + msg);
                //转发到其他客户端
                notifyAllClient(msg, socketChannel);
            }
        } catch (Exception e) {
            try {
                //打印离线的通知
                System.out.println(socketChannel.getRemoteAddress() + "离线了...");
                //取消注册
                selectionKey.cancel();
                //关闭流
                socketChannel.close();
            } catch (IOException e1) {
                e1.printStackTrace();
            }
        }
    }

    /**
     * 转发消息到其他客户端
     * msg 消息
     * noNotifyChannel 不需要通知的Channel
     */
    private void notifyAllClient(String msg, SocketChannel noNotifyChannel) throws Exception {
        System.out.println("服务器转发消息~");
        for (SelectionKey selectionKey : selector.keys()) {
            Channel channel = selectionKey.channel();
            //channel的类型实际类型是SocketChannel，并且排除不需要通知的通道
            if (channel instanceof SocketChannel && channel != noNotifyChannel) {
                //强转成SocketChannel类型
                SocketChannel socketChannel = (SocketChannel) channel;
                //通过消息，包裹获取一个缓冲区
                ByteBuffer byteBuffer = ByteBuffer.wrap(msg.getBytes());
                socketChannel.write(byteBuffer);
            }
        }
    }

    public static void main(String[] args) throws Exception {
        GroupChatServer chatServer = new GroupChatServer();
        //启动服务器，监听
        chatServer.listen();
    }
}
```

## 客户端

```Java
public class GroupChatClinet {

    private Selector selector;

    private SocketChannel socketChannel;

    private String userName;

    public GroupChatClinet() {
        try {
            //打开选择器
            this.selector = Selector.open();
            //连接服务器
            socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", GroupChatServer.PORT));
            //设置为非阻塞
            socketChannel.configureBlocking(false);
            //注册到选择器中
            socketChannel.register(selector, SelectionKey.OP_READ);
            //获取用户名
            userName = socketChannel.getLocalAddress().toString().substring(1);
            System.out.println(userName + " is ok~");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //发送消息到服务端
    private void sendMsg(String msg) {
        msg = userName + "说：" + msg;
        try {
            socketChannel.write(ByteBuffer.wrap(msg.getBytes()));
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    //读取服务端发送过来的消息
    private void readMsg() {
        try {
            int count = selector.select();
            if (count > 0) {
                Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
                while (iterator.hasNext()) {
                    SelectionKey selectionKey = iterator.next();
                    //判断是读就绪事件
                    if (selectionKey.isReadable()) {
                        SocketChannel channel = (SocketChannel) selectionKey.channel();
                        //创建一个缓冲区
                        ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
                        //从服务器的通道中读取数据到缓冲区
                        channel.read(byteBuffer);
                        //缓冲区的数据，转成字符串，并打印
                        System.out.println(new String(byteBuffer.array()));
                    }
                    iterator.remove();
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) throws Exception {
        GroupChatClinet chatClinet = new GroupChatClinet();
        //启动线程，读取服务器转发过来的消息
        new Thread(() -> {
            while (true) {
                chatClinet.readMsg();
                try {
                    Thread.sleep(3000);
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
        //主线程发送消息到服务器
        Scanner scanner = new Scanner(System.in);
        while (scanner.hasNextLine()) {
            String msg = scanner.nextLine();
            chatClinet.sendMsg(msg);
        }
    }
}
```
