# netty

[入门博客](https://zhuanlan.zhihu.com/p/181239748)

Netty 是 一个异步事件驱动的网络应用程序框架，用于快速开发可维护的高性能协议服务器和客户端。

NIO 的主要问题是:

1. NIO 的类库和 API 繁杂，学习成本高，你需要熟练掌握 Selector、ServerSocketChannel、SocketChannel、ByteBuffer 等。
2. 需要熟悉 Java 多线程编程。这是因为 NIO 编程涉及到 Reactor 模式，你必须对多线程和网络编程非常熟悉，才能写出高质量的 NIO 程序。
3. 臭名昭著的 epoll bug。它会导致 Selector 空轮询，最终导致 CPU 100%。直到 JDK1.7 版本依然没得到根本性的解决。

netty 优点:

1. API 使用简单，学习成本低。
2. 功能强大，内置了多种解码编码器，支持多种协议。
3. 性能高，对比其他主流的 NIO 框架，Netty 的性能最优。
4. 社区活跃，发现 BUG 会及时修复，迭代版本周期短，不断加入新的功能。
5. Dubbo、Elasticsearch 都采用了 Netty，质量得到验证。

Netty 的功能、协议、传输方式都比较全，比较强大:

- Core 核心模块: 包括零拷贝、API 库、可扩展的事件模型。
- Protocol Support 协议支持，包括 Http 协议、webSocket、SSL(安全套接字协议)、谷歌 Protobuf 协议、zlib/gzip 压缩与解压缩、Large File Transfer 大文件传输等等。
- Transport Services 传输服务，包括 Socket、Datagram、Http Tunnel 等等。

# hello world

## 依赖

```xml
<dependency>
  <groupId>io.netty</groupId>
  <artifactId>netty-all</artifactId>
  <version>4.1.20.Final</version>
</dependency>
```

## 服务端

### 启动类

```Java
public class MyServer {
    public static void main(String[] args) throws Exception {
        //创建两个线程组,默认的线程数是cpu核数的两倍
        // 连接后,将线程放入workerGroup
        EventLoopGroup bossGroup = new NioEventLoopGroup();
        // 控制读写
        EventLoopGroup workerGroup = new NioEventLoopGroup();
        try {
            //创建服务端的启动对象，设置参数
            ServerBootstrap bootstrap = new ServerBootstrap();
            //设置两个线程组boosGroup和workerGroup
            bootstrap.group(bossGroup, workerGroup)
                //设置服务端通道实现类型
                .channel(NioServerSocketChannel.class)
                //设置线程队列得到连接个数
                .option(ChannelOption.SO_BACKLOG, 128)
                //设置保持活动连接状态
                .childOption(ChannelOption.SO_KEEPALIVE, true)
                //使用匿名内部类的形式初始化通道对象
                .childHandler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel socketChannel) throws Exception {
                            //给pipeline管道设置处理器
                            socketChannel.pipeline().addLast(new MyServerHandler());
                        }
                    });//给workerGroup的EventLoop对应的管道设置处理器
            System.out.println("java技术爱好者的服务端已经准备就绪...");
            //绑定端口号，启动服务端
            ChannelFuture channelFuture = bootstrap.bind(6666).sync();
            //对关闭通道进行监听
            channelFuture.channel().closeFuture().sync();
        } finally {
            bossGroup.shutdownGracefully();
            workerGroup.shutdownGracefully();
        }
    }
}
```

### 处理器

```Java
/**
 * 自定义的Handler需要继承Netty规定好的HandlerAdapter
 * 才能被Netty框架所关联，有点类似SpringMVC的适配器模式
 **/
public class MyServerHandler extends ChannelInboundHandlerAdapter {
    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        //获取客户端发送过来的消息
        ByteBuf byteBuf = (ByteBuf) msg;
        System.out.println("收到客户端" + ctx.channel().remoteAddress() + "发送的消息：" + byteBuf.toString(CharsetUtil.UTF_8));
    }
    @Override
    public void channelReadComplete(ChannelHandlerContext ctx) throws Exception {
        //发送消息给客户端
        ctx.writeAndFlush(Unpooled.copiedBuffer("服务端已收到消息，并给你发送一个问号?", CharsetUtil.UTF_8));
    }
    @Override
    public void exceptionCaught(ChannelHandlerContext ctx, Throwable cause) throws Exception {
        //发生异常，关闭通道
        ctx.close();
    }
}
```

## 客户端

### 启动类

```Java
public class MyClient {
    public static void main(String[] args) throws Exception {
        NioEventLoopGroup eventExecutors = new NioEventLoopGroup();
        try {
            //创建bootstrap对象，配置参数
            Bootstrap bootstrap = new Bootstrap();
            //设置线程组
            bootstrap.group(eventExecutors)
                //设置客户端的通道实现类型
                .channel(NioSocketChannel.class)
                //使用匿名内部类初始化通道
                .handler(new ChannelInitializer<SocketChannel>() {
                        @Override
                        protected void initChannel(SocketChannel ch) throws Exception {
                            //添加客户端通道的处理器
                            ch.pipeline().addLast(new MyClientHandler());
                        }
                    });
            System.out.println("客户端准备就绪，随时可以起飞~");
            //连接服务端
            ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 6666).sync();
            //对通道关闭进行监听
            channelFuture.channel().closeFuture().sync();
        } finally {
            //关闭线程组
            eventExecutors.shutdownGracefully();
        }
    }
}
```

### 处理器

```Java
public class MyClientHandler extends ChannelInboundHandlerAdapter {

    @Override
    public void channelActive(ChannelHandlerContext ctx) throws Exception {
        //发送消息到服务端
        ctx.writeAndFlush(Unpooled.copiedBuffer("歪比巴卜~茉莉~Are you good~马来西亚~", CharsetUtil.UTF_8));
    }

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        //接收服务端发送过来的消息
        ByteBuf byteBuf = (ByteBuf) msg;
        System.out.println("收到服务端" + ctx.channel().remoteAddress() + "的消息：" + byteBuf.toString(CharsetUtil.UTF_8));
    }
}
```

# 特性与组件

## taskQueue 任务队列

如果 Handler 处理器有一些长时间的业务处理，可以交给 taskQueue 异步处理。

```Java
public class MyServerHandler extends ChannelInboundHandlerAdapter {

    @Override
    public void channelRead(ChannelHandlerContext ctx, Object msg) throws Exception {
        //获取到线程池eventLoop，添加线程，执行
        ctx.channel().eventLoop().execute(new Runnable() {
            @Override
            public void run() {
                try {
                    //长时间操作，不至于长时间的业务操作导致Handler阻塞
                    Thread.sleep(1000);
                    System.out.println("长时间的业务处理");
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
    }
}
```

## scheduleTaskQueue 延时任务队列

延时任务队列和上面介绍的任务队列非常相似，只是多了一个可延迟一定时间再执行的设置，请看代码演示：

```Java
ctx.channel().eventLoop().schedule(new Runnable() {
    @Override
    public void run() {
        try {
            //长时间操作，不至于长时间的业务操作导致Handler阻塞
            Thread.sleep(1000);
            System.out.println("长时间的业务处理");
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
},5, TimeUnit.SECONDS);//5秒后执行
```

## Future 异步机制

ChannelFuture 提供操作完成时一种异步通知的方式。一般在 Socket 编程中，等待响应结果都是同步阻塞的，而 Netty 则不会造成阻塞，因为 ChannelFuture 是采取类似观察者模式的形式进行获取结果。

```Java
ChannelFuture channelFuture = bootstrap.connect("127.0.0.1", 6666);
...
//添加监听器
channelFuture.addListener(new ChannelFutureListener() {
    //使用匿名内部类，ChannelFutureListener接口
    //重写operationComplete方法
    @Override
    public void operationComplete(ChannelFuture future) throws Exception {
        //判断是否操作成功
        if (future.isSuccess()) {
            System.out.println("连接成功");
        } else {
            System.out.println("连接失败");
        }
    }
});
```

## Bootstrap 与 ServerBootstrap

Bootstrap 和 ServerBootstrap 是 Netty 提供的一个创建客户端和服务端启动器的工厂类，使用这个工厂类非常便利地创建启动类,都是继承于 AbstractBootStrap 抽象类.

一般使用步骤

1. 设置 EventLoopGroup 线程组,`group`
2. 设置 channel 通道类型
3. 设置 option 参数
4. 设置 handler 流水线
5. 进行端口绑定
6. 启动
7. 等待通道关闭
8. 优雅关闭`EventLoopGroup`

```Java
//创建服务端的启动对象，设置参数
ServerBootstrap bootstrap = new ServerBootstrap();
//设置两个线程组boosGroup和workerGroup
//bossGroup 用于监听客户端连接，专门负责与客户端创建连接，并把连接注册到workerGroup的Selector中。
//workerGroup用于处理每一个连接发生的读写事件。
//默认的线程数是cpu核数的两倍
bootstrap.group(bossGroup, workerGroup)
    //设置服务端通道实现类型
    /**
    通道类型有以下：
      NioSocketChannel： 异步非阻塞的客户端 TCP Socket 连接。
      NioServerSocketChannel： 异步非阻塞的服务器端 TCP Socket 连接。
    常用的就是这两个通道类型，因为是异步非阻塞的。所以是首选。
      OioSocketChannel： 同步阻塞的客户端 TCP Socket 连接。
      OioServerSocketChannel： 同步阻塞的服务器端 TCP Socket 连接。
      NioSctpChannel： 异步的客户端 Sctp（Stream Control Transmission Protocol，流控制传输协议）连接。
      NioSctpServerChannel： 异步的 Sctp 服务器端连接。
     */
    .channel(NioServerSocketChannel.class)
    //设置线程队列得到连接个数,置的是服务端用于接收进来的连接，也就是boosGroup线程。
    // SO_BACKLOG Socket参数，服务端接受连接的队列长度，如果队列已满，客户端连接将被拒绝。默认值，Windows为200，其他为128。
    .option(ChannelOption.SO_BACKLOG, 128)
    //设置保持活动连接状态,是提供给父管道接收到的连接，也就是workerGroup线程。
    /**
    SO_RCVBUF Socket参数，TCP数据接收缓冲区大小。
    TCP_NODELAY TCP参数，立即发送数据，默认值为Ture。
    SO_KEEPALIVE Socket参数，连接保活，默认值为False。启用该功能时，TCP会主动探测空闲连接的有效性。
     */
    .childOption(ChannelOption.SO_KEEPALIVE, true)
    //使用匿名内部类的形式初始化通道对象
    .childHandler(new ChannelInitializer<SocketChannel>() {
            @Override
            protected void initChannel(SocketChannel socketChannel) throws Exception {
                //给pipeline管道设置处理器
                socketChannel.pipeline().addLast(new MyServerHandler());
            }
        });//给workerGroup的EventLoop对应的管道设置处理器


//server端代码，跟上面几乎一样，只需改三个地方
//这个地方使用的是OioEventLoopGroup
EventLoopGroup bossGroup = new OioEventLoopGroup();
ServerBootstrap bootstrap = new ServerBootstrap();
bootstrap.group(bossGroup)//只需要设置一个线程组boosGroup
        .channel(OioServerSocketChannel.class)//设置服务端通道实现类型

//client端代码，只需改两个地方
//使用的是OioEventLoopGroup
EventLoopGroup eventExecutors = new OioEventLoopGroup();
//通道类型设置为OioSocketChannel
bootstrap.group(eventExecutors)//设置线程组
        .channel(OioSocketChannel.class)//设置客户端的通道实现类型
```

## 处理器

处理器 Handler 主要分为两种：ChannelInboundHandlerAdapter(入站处理器)、ChannelOutboundHandler(出站处理器);

入站指的是数据从底层 java NIO Channel 到 Netty 的 Channel;出站指的是通过 Netty 的 Channel 来操作底层的 java NIO Channel。

`ChannelInboundHandlerAdapter` 处理器常用的事件有：

注册事件 fireChannelRegistered。
连接建立事件 fireChannelActive。
读事件和读完成事件 fireChannelRead、fireChannelReadComplete。
异常通知事件 fireExceptionCaught。
用户自定义事件 fireUserEventTriggered。
Channel 可写状态变化事件 fireChannelWritabilityChanged。
连接关闭事件 fireChannelInactive。

`ChannelOutboundHandler` 处理器常用的事件有：
端口绑定 bind。
连接服务端 connect。
写事件 write。
刷新时间 flush。
读事件 read。
主动断开连接 disconnect。
关闭 channel 事件 close。
还有一个类似的 handler()，主要用于装配 parent 通道，也就是 bossGroup 线程。一般情况下，都用不上这个方法。

## PiPeline 与 ChannelPipeline

pipeline 相当于处理器的容器。初始化 channel 时，把 channelHandler 按顺序装在 pipeline 中，就可以实现按序执行 channelHandler 了。

在一个 Channel 中，只有一个 ChannelPipeline。该 pipeline 在 Channel 被创建的时候创建。ChannelPipeline 包含了一个 ChannelHander 形成的列表，且所有 ChannelHandler 都会注册到 ChannelPipeline 中。

## ChannelHandlerContext

Netty 设计了这个 ChannelHandlerContext 上下文对象，就可以拿到 channel、pipeline 等对象，就可以进行读写等操作。

ChannelHandlerContext 是一个接口，下面有三个实现类。

```Java
实际上ChannelHandlerContext在pipeline中是一个链表的形式。看一段源码就明白了：

//ChannelPipeline实现类DefaultChannelPipeline的构造器方法
protected DefaultChannelPipeline(Channel channel) {
    this.channel = ObjectUtil.checkNotNull(channel, "channel");
    succeededFuture = new SucceededChannelFuture(channel, null);
    voidPromise =  new VoidChannelPromise(channel, true);
    //设置头结点head，尾结点tail
    tail = new TailContext(this);
    head = new HeadContext(this);

    head.next = tail;
    tail.prev = head;
}
```

## EventLoopGroup

EventLoopGroup 是一个接口,实现类有 NioEventLoopGroup(异步非阻塞),OioEventLoopGroup(同步则色),DefaultEventLoopGroup

从 Netty 的架构图中，可以知道服务器是需要两个线程组进行配合工作的，而这个线程组的接口就是 EventLoopGroup。

每个 EventLoopGroup 里包括一个或多个 EventLoop，每个 EventLoop 中维护一个 Selector 实例。

### 轮询机制的实现原理

```Java
我们不妨看一段DefaultEventExecutorChooserFactory的源码：

private final AtomicInteger idx = new AtomicInteger();
private final EventExecutor[] executors;

@Override
public EventExecutor next() {
    //idx.getAndIncrement()相当于idx++，然后对任务长度取模
    return executors[idx.getAndIncrement() & executors.length - 1];
}

//它这里还有一个判断，如果线程数不是2的N次方，则采用取模算法实现。
@Override
public EventExecutor next() {
    return executors[Math.abs(idx.getAndIncrement() % executors.length)];
}
```
