# scoket

在开发网络应用程序的时候，我们又会遇到 Socket 这个概念。Socket 是一个抽象概念，一个应用程序通过一个 Socket 来建立一个远程连接，而 Socket 内部通过 TCP/IP 协议把数据传输到网络：

```java
┌───────────┐                                   ┌───────────┐
│Application│                                   │Application│
├───────────┤                                   ├───────────┤
│  Socket   │                                   │  Socket   │
├───────────┤                                   ├───────────┤
│    TCP    │                                   │    TCP    │
├───────────┤      ┌──────┐       ┌──────┐      ├───────────┤
│    IP     │<────>│Router│<─────>│Router│<────>│    IP     │
└───────────┘      └──────┘       └──────┘      └───────────┘
```

Socket、TCP 和部分 IP 的功能都是由操作系统提供的，不同的编程语言只是提供了对操作系统调用的简单的封装。

一个 Socket 就是由 IP 地址和端口号（范围是 0 ～ 65535）组成，可以把 Socket 简单理解为 IP 地址加端口号。端口号总是由操作系统分配，它是一个 0 ～ 65535 之间的数字，其中，小于 1024 的端口属于特权端口，需要管理员权限，大于 1024 的端口可以由任意用户的应用程序打开。

Port: 表示计算机上的一个程序的进程

1. 不同的进程不同的端口!有来区分软件
2. 范围:0-65535 ;
3. 单个协议下,端口号不能冲突
4. 共有端口: 0-1023
   1. http 80
   2. https 443
   3. ftp 21
   4. telnet 23
5. 程序注册端口:1024-65535
   1. tomcat 8080
   2. mysql 3306
   3. oracle 1521
   4. idea
   5. 动态/私有 49152-65535
6. 命令:
   1. 查看所有端口 netstat -ano
   2. 查看指定端口 netstat -ano|findstr "5900"
   3. 查看指定端口的进程 tasklist|findstr "8696"
7. `InetSocketAddress`

# TCP 编程

使用 Socket 进行网络编程时，本质上就是两个进程之间的网络通信。其中一个进程必须充当服务器端，它会主动监听某个指定的端口，另一个进程必须充当客户端，它必须主动连接服务器的 IP 地址和指定端口，如果连接成功，服务器端和客户端就成功地建立了一个 TCP 连接，双方后续就可以随时发送和接收数据。

因此，当 Socket 连接成功地在服务器端和客户端之间建立后：

对服务器端来说，它的 Socket 是指定的 IP 地址和指定的端口号；
对客户端来说，它的 Socket 是它所在计算机的 IP 地址和一个由操作系统分配的随机端口号。

## 服务端

Java 标准库提供了 `ServerSocket` 来实现对指定 `IP` 和指定端口的监听

1. 创建 ServerSocket 对象,监听端口 `new ServerSocket(6666);`
2. 每一个客户端连接后,获取一个 Socket 实例用于和客户端通信, `Socket sock = ss.accept();`,没有连接时,程序会阻塞
3. 通过 Socket 实例的输入流,接收客户端消息
4. 通过写入 Socket 的输出流,发送信息给客户端

```java
public class Server {
    public static void main(String[] args) throws IOException {
        // 监听指定端口 , 没有指定IP地址，表示在计算机的所有网络接口上进行监听
        ServerSocket ss = new ServerSocket(6666);
        System.out.println("server is running...");
        for (;;) {
            Socket sock = ss.accept();
            System.out.println("connected from " + sock.getRemoteSocketAddress());
            Thread t = new Handler(sock);
            t.start();
        }
    }
}
class Handler extends Thread {
    Socket sock;
    public Handler(Socket sock) {
        this.sock = sock;
    }
    @Override
    public void run() {
        try (InputStream input = this.sock.getInputStream()) {
            try (OutputStream output = this.sock.getOutputStream()) {
                handle(input, output);
            }
        } catch (Exception e) {
            try {
                this.sock.close();
            } catch (IOException ioe) {
            }
            System.out.println("client disconnected.");
        }
    }
    private void handle(InputStream input, OutputStream output) throws IOException {
        var writer = new BufferedWriter(new OutputStreamWriter(output, StandardCharsets.UTF_8));
        var reader = new BufferedReader(new InputStreamReader(input, StandardCharsets.UTF_8));
        writer.write("hello\n");
        // 强制刷新
        writer.flush();
        for (;;) {
            String s = reader.readLine();
            if (s.equals("bye")) {
                writer.write("bye\n");
                writer.flush();
                break;
            }
            writer.write("ok: " + s + "\n");
            writer.flush();
        }
    }
}
```

## 客户端

1. 通过 IP 和 Port 连接服务端 `new Socket("localhost", 6666);`,连接成功则创建一个 Socket 实例用于和服务端通信
2. 通过 Socket 实例的输入流,接收服务端消息
3. 通过写入 Socket 的输出流,发送信息给服务端
4. 断开连接 `sock.close();`

```java
public class Client {
    public static void main(String[] args) throws IOException {
        Socket sock = new Socket("localhost", 6666); // 连接指定服务器和端口
        try (InputStream input = sock.getInputStream()) {
            try (OutputStream output = sock.getOutputStream()) {
                handle(input, output);
            }
        }
        sock.close();
        System.out.println("disconnected.");
    }
    private static void handle(InputStream input, OutputStream output) throws IOException {
        var writer = new BufferedWriter(new OutputStreamWriter(output, StandardCharsets.UTF_8));
        var reader = new BufferedReader(new InputStreamReader(input, StandardCharsets.UTF_8));
        Scanner scanner = new Scanner(System.in);
        System.out.println("[server] " + reader.readLine());
        for (;;) {
            System.out.print(">>> "); // 打印提示
            String s = scanner.nextLine(); // 读取一行输入
            writer.write(s);
            writer.newLine();
            writer.flush();
            String resp = reader.readLine();
            System.out.println("<<< " + resp);
            if (resp.equals("bye")) {
                break;
            }
        }
    }
}
```

# UDP 编程

和 TCP 编程相比，UDP 编程就简单得多，因为 UDP 没有创建连接，数据包也是一次收发一个，所以没有流的概念。

在 Java 中使用 UDP 编程，仍然需要使用 Socket，因为应用程序在使用 UDP 时必须指定网络接口（IP）和端口号。注意：UDP 端口和 TCP 端口虽然都使用 0~65535，但他们是两套独立的端口，即一个应用程序用 TCP 占用了端口 1234，不影响另一个应用程序用 UDP 占用端口 1234。

## 服务端

在服务器端，使用 UDP 也需要监听指定的端口。Java 提供了 `DatagramSocket` 来实现这个功能

```java
DatagramSocket ds = new DatagramSocket(6666); // 监听指定端口
for (;;) { // 无限循环
    // 数据缓冲区:
    byte[] buffer = new byte[1024];
    DatagramPacket packet = new DatagramPacket(buffer, buffer.length);
    // DatagramSocket没有IO流接口，数据被直接写入byte[]缓冲区。
    ds.receive(packet); // 收取一个UDP数据包
    // 收取到的数据存储在buffer中，由packet.getOffset(), packet.getLength()指定起始位置和长度
    // 将其按UTF-8编码转换为String:
    String s = new String(packet.getData(), packet.getOffset(), packet.getLength(), StandardCharsets.UTF_8);
    // 发送数据:
    byte[] data = "ACK".getBytes(StandardCharsets.UTF_8);
    packet.setData(data);
    ds.send(packet);
}
```

## 客户端

```Java
DatagramSocket ds = new DatagramSocket();
// 后续接收UDP包时，等待时间最多不会超过1秒，否则在没有收到UDP包时，客户端会无限等待下去
ds.setSoTimeout(1000);
// 这个connect()方法不是真连接，它是为了在客户端的DatagramSocket实例中保存服务器端的IP和端口号，确保这个DatagramSocket实例只能往指定的地址和端口发送UDP包，不能往其他地址和端口发送。这么做不是UDP的限制，而是Java内置了安全检查。
ds.connect(InetAddress.getByName("localhost"), 6666); // 连接指定服务器和端口
// 发送:
byte[] data = "Hello".getBytes();
DatagramPacket packet = new DatagramPacket(data, data.length);
ds.send(packet);
// 接收:
byte[] buffer = new byte[1024];
packet = new DatagramPacket(buffer, buffer.length);
ds.receive(packet);
String resp = new String(packet.getData(), packet.getOffset(), packet.getLength());
// 注意到disconnect()也不是真正地断开连接，它只是清除了客户端DatagramSocket实例记录的远程服务器地址和端口号，这样，DatagramSocket实例就可以连接另一个服务器端。
ds.disconnect();
```

# email

电子邮件是从用户电脑的邮件软件，例如 Outlook，发送到邮件服务器上，可能经过若干个邮件服务器的中转，最终到达对方邮件服务器上，收件方就可以用软件接收邮件.

我们把类似 Outlook 这样的邮件软件称为 `MUA`：Mail User Agent，意思是给用户服务的邮件代理；
邮件服务器则称为 `MTA`：Mail Transfer Agent，意思是邮件中转的代理；
最终到达的邮件服务器称为 `MDA`：Mail Delivery Agent，意思是邮件到达的代理。

MTA 和 MDA 这样的服务器软件通常是现成的，我们不关心这些服务器内部是如何运行的。要发送邮件，我们关心的是如何编写一个 MUA 的软件，把邮件发送到 MTA 上。

MUA 到 MTA 发送邮件的协议就是 `SMTP` 协议，它是 Simple Mail Transport Protocol 的缩写，使用**标准端口 25**，也可以使用**加密端口 465 或 587**。

SMTP 协议是一个建立在 TCP 之上的协议，任何程序发送邮件都必须遵守 SMTP 协议。使用 Java 程序发送邮件时，我们无需关心 SMTP 协议的底层原理，只需要使用 `JavaMail` 这个标准 API 就可以直接发送邮件。

## 发送邮件

假设我们准备使用自己的邮件地址me@example.com给小明发送邮件，已知小明的邮件地址是xiaoming@somewhere.com，发送邮件前，我们首先要确定作为 MTA 的邮件服务器地址和端口号。邮件服务器地址通常是 smtp.example.com，端口号由邮件服务商确定使用 25、465 还是 587。以下是一些常用邮件服务商的 SMTP 信息：

QQ 邮箱：SMTP 服务器是 `smtp.qq.com`，端口是 `465/587`；
163 邮箱：SMTP 服务器是 `smtp.163.com`，端口是 `465`；
Gmail 邮箱：SMTP 服务器是 `smtp.gmail.com`，端口是 `465/587`。
有了 SMTP 服务器的域名和端口号，我们还需要 SMTP 服务器的登录信息，通常是使用自己的邮件地址作为用户名，登录口令是用户口令或者一个独立设置的 SMTP 口令。

Java mail 依赖:

```xml
<dependencies>
    <dependency>
        <groupId>javax.mail</groupId>
        <artifactId>javax.mail-api</artifactId>
        <version>1.6.2</version>
    </dependency>
    <dependency>
        <groupId>com.sun.mail</groupId>
        <artifactId>javax.mail</artifactId>
        <version>1.6.2</version>
    </dependency>
    ...
```

发送邮件步骤:

1. 连接自己邮箱的邮件服务器
2.

```java
// 服务器地址:
String smtp = "smtp.office365.com";
// 登录用户名:
String username = "jxsmtp101@outlook.com";
// 登录口令:
String password = "********";
// 连接到SMTP服务器587端口:
Properties props = new Properties();
props.put("mail.smtp.host", smtp); // SMTP主机名
props.put("mail.smtp.port", "587"); // 主机端口号
props.put("mail.smtp.auth", "true"); // 是否需要用户认证
props.put("mail.smtp.starttls.enable", "true"); // 启用TLS加密
// 获取Session实例:
Session session = Session.getInstance(props, new Authenticator() {
    protected PasswordAuthentication getPasswordAuthentication() {
        return new PasswordAuthentication(username, password);
    }
});
// 设置debug模式便于调试:
session.setDebug(true);

MimeMessage message = new MimeMessage(session);
// 设置发送方地址: 绝大多数邮件服务器要求发送方地址和登录用户名必须一致，否则发送将失败
message.setFrom(new InternetAddress("me@example.com"));
// 设置接收方地址:
message.setRecipient(Message.RecipientType.TO, new InternetAddress("xiaoming@somewhere.com"));
// 设置邮件主题:
message.setSubject("Hello", "UTF-8");
// 设置邮件正文:
message.setText("Hi Xiaoming...", "UTF-8"); // 字符串
message.setText(body, "UTF-8", "html"); // body 为 html 内容
// 发送:
Transport.send(message);


// 发送附件
// 一个Multipart对象可以添加若干个BodyPart，其中第一个BodyPart是文本，即邮件正文，后面的BodyPart是附件。
Multipart multipart = new MimeMultipart();
// 添加text:
BodyPart textpart = new MimeBodyPart();
textpart.setContent(body, "text/html;charset=utf-8");
textpart.setContent("<h1>Hello</h1><p><img src=\"cid:img01\"></p>", "text/html;charset=utf-8"); // 内嵌图片
multipart.addBodyPart(textpart);
// 添加image:
BodyPart imagepart = new MimeBodyPart();
imagepart.setFileName(fileName);
imagepart.setDataHandler(new DataHandler(new ByteArrayDataSource(input, "application/octet-stream")));
multipart.addBodyPart(imagepart);
// 添加image 用于内嵌图片
BodyPart imagepart = new MimeBodyPart();
imagepart.setFileName(fileName);
imagepart.setDataHandler(new DataHandler(new ByteArrayDataSource(input, "image/jpeg")));
// 与HTML的<img src="cid:img01">关联:
imagepart.setHeader("Content-ID", "<img01>");
multipart.addBodyPart(imagepart);
// 设置邮件内容为multipart:
message.setContent(multipart);
```

常见报错

1. 用户名或口令错误，会导致 535 登录失败
2. 登录用户和发件人不一致，会导致 554 拒绝发送错误
3. 有些时候，如果邮件主题和正文过于简单，会导致 554 被识别为垃圾邮件的错误

## 接收邮件

接收邮件是收件人用自己的客户端把邮件从 MDA 服务器上抓取到本地的过程。

接收邮件使用最广泛的协议是 POP3：Post Office Protocol version 3，它也是一个建立在 TCP 连接之上的协议。POP3 服务器的标准端口是 110，如果整个会话需要加密，那么使用加密端口 995。

另一种接收邮件的协议是 IMAP：Internet Mail Access Protocol，它使用标准端口 143 和加密端口 993。

IMAP 和 POP3 的主要区别是，IMAP 协议在本地的所有操作都会自动同步到服务器上，并且，IMAP 可以允许用户在邮件服务器的收件箱中创建文件夹。

JavaMail 也提供了 IMAP 协议的支持。因为 POP3 和 IMAP 的使用方式非常类似，因此我们只介绍 POP3 的用法。

```java
/**
使用POP3收取Email时，我们无需关心POP3协议底层，因为JavaMail提供了高层接口。首先需要连接到Store对象：
 */
// 准备登录信息:
String host = "pop3.example.com";
int port = 995;
String username = "bob@example.com";
String password = "password";
Properties props = new Properties();
props.setProperty("mail.store.protocol", "pop3"); // 协议名称
props.setProperty("mail.pop3.host", host);// POP3主机名
props.setProperty("mail.pop3.port", String.valueOf(port)); // 端口号
// 启动SSL:
props.put("mail.smtp.socketFactory.class", "javax.net.ssl.SSLSocketFactory");
props.put("mail.smtp.socketFactory.port", String.valueOf(port));
// 连接到Store:
URLName url = new URLName("pop3", host, post, "", username, password);
Session session = Session.getInstance(props, null);
session.setDebug(true); // 显示调试信息
Store store = new POP3SSLStore(session, url);
store.connect();
/**
一个Store对象表示整个邮箱的存储，要收取邮件，我们需要通过Store访问指定的Folder（文件夹），通常是INBOX表示收件箱：
 */
// 获取收件箱:
Folder folder = store.getFolder("INBOX");
// 以读写方式打开:
folder.open(Folder.READ_WRITE);
// 打印邮件总数/新邮件数量/未读数量/已删除数量:
System.out.println("Total messages: " + folder.getMessageCount());
System.out.println("New messages: " + folder.getNewMessageCount());
System.out.println("Unread messages: " + folder.getUnreadMessageCount());
System.out.println("Deleted messages: " + folder.getDeletedMessageCount());
// 获取每一封邮件:
Message[] messages = folder.getMessages();
for (Message message : messages) {
    // 打印每一封邮件:
    printMessage((MimeMessage) message);
}
/**
当我们获取到一个Message对象时，可以强制转型为MimeMessage，然后打印出邮件主题、发件人、收件人等信息：
 */
void printMessage(MimeMessage msg) throws IOException, MessagingException {
    // 邮件主题:
    System.out.println("Subject: " + MimeUtility.decodeText(msg.getSubject()));
    // 发件人:
    Address[] froms = msg.getFrom();
    InternetAddress address = (InternetAddress) froms[0];
    String personal = address.getPersonal();
    String from = personal == null ? address.getAddress() : (MimeUtility.decodeText(personal) + " <" + address.getAddress() + ">");
    System.out.println("From: " + from);
    // 继续打印收件人:
    ...
}
/**
比较麻烦的是获取邮件的正文。一个MimeMessage对象也是一个Part对象，它可能只包含一个文本，也可能是一个Multipart对象，即由几个Part构成，因此，需要递归地解析出完整的正文：
 */
String getBody(Part part) throws MessagingException, IOException {
    if (part.isMimeType("text/*")) {
        // Part是文本:
        return part.getContent().toString();
    }
    if (part.isMimeType("multipart/*")) {
        // Part是一个Multipart对象:
        Multipart multipart = (Multipart) part.getContent();
        // 循环解析每个子Part:
        for (int i = 0; i < multipart.getCount(); i++) {
            BodyPart bodyPart = multipart.getBodyPart(i);
            String body = getBody(bodyPart);
            if (!body.isEmpty()) {
                return body;
            }
        }
    }
    return "";
}

folder.close(true); // 传入true表示删除操作会同步到服务器上（即删除服务器收件箱的邮件）
store.close();
```
