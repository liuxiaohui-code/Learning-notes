# websocket

websocket: http 长连接

Java 实现 Websocket 通常有两种方式：1、创建 WebSocketServer 类，里面包含 open、close、message、error 等方法；2、利用 Springboot 提供的 webSocketHandler 类，创建其子类并重写方法。我们项目虽然使用 Springboot 框架，不过仍采用了第一种方法实现。

创建 WebSocket 的简单实例操作流程

## 1）引入 Websocket 依赖

```xml
<!--websocket支持包-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-websocket</artifactId>
</dependency>
```

## 2）创建配置类 WebSocketConfig

ServerEndpointExporter 是由 Spring 官方提供的标准实现，用于扫描 ServerEndpointConfig 配置类和@ServerEndpoint 注解实例。
     如果使用内置的 tomcat 容器，那么必须用@Bean 注入 ServerEndpointExporter ；
     如果使用外置容器部署 war 包，那么不需要提供 ServerEndpointExporter ，扫描服务器的行为将交由外置容器处理，需要将注入 ServerEndpointExporter 的@Bean 代码注解掉。

```Java
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.server.standard.ServerEndpointExporter;

@Configuration
public class WebSocketConfig {
    /**
     * 如果使用Springboot默认内置的tomcat容器，则必须注入ServerEndpoint的bean；
     * 如果使用外置的web容器，则不需要提供ServerEndpointExporter，下面的注入可以注解掉
     */
    @Bean
    public ServerEndpointExporter serverEndpointExporter(){
        return new ServerEndpointExporter();
    }
}
```

## 3）创建 WebSocketServer

在 websocket 协议下，后端服务器相当于 ws 里面的客户端，需要用@ServerEndpoint 指定访问路径，并使用@Component 注入容器

@ServerEndpoint：当 ServerEndpointExporter 类通过 Spring 配置进行声明并被使用，它将会去扫描带有@ServerEndpoint 注解的类。被注解的类将被注册成为一个 WebSocket 端点。所有的配置项都在这个注解的属性中 ( 如:@ServerEndpoint("/ws") )

下面的栗子中@ServerEndpoint 指定访问路径中包含 sid，这个是用于区分每个页面

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

import javax.websocket.OnClose;
import javax.websocket.OnError;
import javax.websocket.OnMessage;
import javax.websocket.OnOpen;
import javax.websocket.Session;
import javax.websocket.server.PathParam;
import javax.websocket.server.ServerEndpoint;

import com.alibaba.fastjson.JSON;
import com.alibaba.fastjson.JSONArray;
import com.alibaba.fastjson.JSONObject;
import org.apache.commons.lang.StringUtils;
import org.springframework.stereotype.Component;

import java.io.IOException;
import java.util.Collection;
import java.util.concurrent.ConcurrentHashMap;

@ServerEndpoint(value = "/ws/{sid}")
@Component
public class WebSocketServer {

    private final static Logger log = LoggerFactory.getLogger(WebSocketServer.class);
    //静态变量，用来记录当前在线连接数。应该把它设计成线程安全的。
    private static int onlineCount = 0;
    //与某个客户端的连接会话，需要通过它来给客户端发送数据
    private Session session;
    //旧：concurrent包的线程安全Set，用来存放每个客户端对应的MyWebSocket对象。由于遍历set费时，改用map优化
    //private static CopyOnWriteArraySet<WebSocketServer> webSocketSet = new CopyOnWriteArraySet<WebSocketServer>();
    //新：使用map对象优化，便于根据sid来获取对应的WebSocket
    private static ConcurrentHashMap<String,WebSocketServer> websocketMap = new ConcurrentHashMap<>();
    //接收用户的sid，指定需要推送的用户
    private String sid;

    /**
     * 连接成功后调用的方法
     */
    @OnOpen
    public void onOpen(Session session,@PathParam("sid") String sid) {
        this.session = session;
        //webSocketSet.add(this);     //加入set中
        websocketMap.put(sid,this); //加入map中
        addOnlineCount();           //在线数加1
        log.info("有新窗口开始监听:"+sid+",当前在线人数为" + getOnlineCount());
        this.sid=sid;
        try {
            sendMessage("连接成功");
        } catch (IOException e) {
            log.error("websocket IO异常");
        }
    }

    /**
     * 连接关闭调用的方法
     */
    @OnClose
    public void onClose() {
        if(websocketMap.get(this.sid)!=null){
            //webSocketSet.remove(this);  //从set中删除
            websocketMap.remove(this.sid);  //从map中删除
            subOnlineCount();           //在线数减1
            log.info("有一连接关闭！当前在线人数为" + getOnlineCount());
        }
    }

    /**
     * 收到客户端消息后调用的方法，根据业务要求进行处理，这里就简单地将收到的消息直接群发推送出去
     * @param message 客户端发送过来的消息
     */
    @OnMessage
    public void onMessage(String message, Session session) {
        log.info("收到来自窗口"+sid+"的信息:"+message);
        if(StringUtils.isNotBlank(message)){
            for(WebSocketServer server:websocketMap.values()) {
                try {
                    server.sendMessage(message);
                } catch (IOException e) {
                    e.printStackTrace();
                    continue;
                }
            }
        }
    }

    /**
     * 发生错误时的回调函数
     * @param session
     * @param error
     */
    @OnError
    public void onError(Session session, Throwable error) {
        log.error("发生错误");
        error.printStackTrace();
    }

    /**
     * 实现服务器主动推送消息
     */
    public void sendMessage(String message) throws IOException {
        this.session.getBasicRemote().sendText(message);
    }


    /**
     * 群发自定义消息（用set会方便些）
     * */
    public static void sendInfo(String message,@PathParam("sid") String sid) throws IOException {
        log.info("推送消息到窗口"+sid+"，推送内容:"+message);
        /*for (WebSocketServer item : webSocketSet) {
            try {
                //这里可以设定只推送给这个sid的，为null则全部推送
                if(sid==null) {
                    item.sendMessage(message);
                }else if(item.sid.equals(sid)){
                    item.sendMessage(message);
                }
            } catch (IOException e) {
                continue;
            }
        }*/
        if(StringUtils.isNotBlank(message)){
            for(WebSocketServer server:websocketMap.values()) {
                try {
                    // sid为null时群发，不为null则只发一个
                    if (sid == null) {
                        server.sendMessage(message);
                    } else if (server.sid.equals(sid)) {
                        server.sendMessage(message);
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                    continue;
                }
            }
        }
    }

    public static synchronized int getOnlineCount() {
        return onlineCount;
    }
    public static synchronized void addOnlineCount() {
        WebSocketServer.onlineCount++;
    }
    public static synchronized void subOnlineCount() {
        WebSocketServer.onlineCount--;
    }
}
```

## 4）websocket 调用

可以提供接口让前端调用，也可以由前端指定 ws 调用网址，直接使用 onopen 等方法。在业务代码中调用方法也是可以的。

### 4.1）提供接口进行消息推送

一个用户调用接口，主动将信息发给后端，后端接收后再主动推送给指定/全部用户

```Java
import com.javatest.websocket.WebSocketServer;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;
import org.springframework.web.servlet.ModelAndView;

import java.io.IOException;
import java.util.HashMap;
import java.util.Map;

@Controller
@RequestMapping("/websocket")
public class WebSocketController {

    //页面请求
    @GetMapping("/socket/{cid}")
    public ModelAndView socket(@PathVariable String cid) {
        ModelAndView mav=new ModelAndView("/socket");
        mav.addObject("cid", cid);
        return mav;
    }
    //推送数据接口
    @ResponseBody
    @RequestMapping("/socket/push/{cid}")
    public Map<String,Object> pushToWeb(@PathVariable String cid, String message) {
        Map<String,Object> result = new HashMap<>();
        try {
            WebSocketServer.sendInfo(message,cid);
            result.put("status","success");
        } catch (IOException e) {
            e.printStackTrace();
            result.put("status","fail");
            result.put("errMsg",e.getMessage());
        }
        return result;
    }
```

### 4.2）由前端指定 ws 调用网址直接使用

前端页面，注意是要使用 ws 协议

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>websocket通讯</title>
  </head>
  <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.js"></script>
  <script>
    var socket
    function openSocket() {
      if (typeof WebSocket == "undefined") {
        console.log("您的浏览器不支持WebSocket")
      } else {
        console.log("您的浏览器支持WebSocket")
        //实现化WebSocket对象，指定要连接的服务器地址与端口  建立连接
        //等同于socket = new WebSocket("ws://localhost:9010/javatest/ws/25");
        //var socketUrl="${request.contextPath}/ws/"+$("#userId").val();
        var socketUrl = "http://localhost:9010/javatest/ws/" + $("#userId").val()
        socketUrl = socketUrl.replace("https", "ws").replace("http", "ws")
        console.log(socketUrl)
        socket = new WebSocket(socketUrl)
        //打开事件
        socket.onopen = function () {
          console.log("websocket已打开")
          //socket.send("这是来自客户端的消息" + location.href + new Date());
        }
        //获得消息事件
        socket.onmessage = function (msg) {
          console.log(msg.data)
          //发现消息进入    开始处理前端触发逻辑
        }
        //关闭事件
        socket.onclose = function () {
          console.log("websocket已关闭")
        }
        //发生了错误事件
        socket.onerror = function () {
          console.log("websocket发生了错误")
        }
      }
    }
    function sendMessage() {
      if (typeof WebSocket == "undefined") {
        console.log("您的浏览器不支持WebSocket")
      } else {
        console.log("您的浏览器支持WebSocket")
        console.log('[{"toUserId":"' + $("#toUserId").val() + '","contentText":"' + $("#contentText").val() + '"}]')
        socket.send('[{"toUserId":"' + $("#toUserId").val() + '","contentText":"' + $("#contentText").val() + '"}]')
      }
    }
  </script>
  <body>
    <p>【userId】：</p>
    <div><input id="userId" name="userId" type="text" value="11" /></div>
    <p>【toUserId】：</p>
    <div><input id="toUserId" name="toUserId" type="text" value="22" /></div>
    <p>【toUserId内容】：</p>
    <div><input id="contentText" name="contentText" type="text" value="abc" /></div>
    <p>【操作】：</p>
    <div><input type="button" onclick="openSocket()" />开启socket</div>
    <p>【操作】：</p>
    <div><input type="button" onclick="sendMessage()" />发送消息</div>
  </body>
</html>
```

这样，启动服务器之后就可以接口或者 ws 调用网址的方式进行 websocket 的通信啦~
