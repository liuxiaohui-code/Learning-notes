# jdk--HttpURLConnection

Java 标准库提供了基于 HTTP 的包，但是要注意，早期的 JDK 版本是通过 HttpURLConnection 访问 HTTP，典型代码如下：

```java
URL url = new URL("http://www.example.com/path/to/target?a=1&b=2");
HttpURLConnection conn = (HttpURLConnection) url.openConnection();
conn.setRequestMethod("GET");
conn.setUseCaches(false);
conn.setConnectTimeout(5000); // 请求超时5秒
// 设置HTTP头:
conn.setRequestProperty("Accept", "*/*");
conn.setRequestProperty("User-Agent", "Mozilla/5.0 (compatible; MSIE 11; Windows NT 5.1)");
// 连接并发送HTTP请求:
conn.connect();
// 判断HTTP响应是否200:
if (conn.getResponseCode() != 200) {
    throw new RuntimeException("bad response");
}
// 获取所有响应Header:
Map<String, List<String>> map = conn.getHeaderFields();
for (String key : map.keySet()) {
    System.out.println(key + ": " + map.get(key));
}
// 获取响应内容:
InputStream input = conn.getInputStream();
...

```

# jdk11--HttpClient

从 Java 11 开始，引入了新的 HttpClient，它使用链式调用的 API，能大大简化 HTTP 的处理。

```java
static HttpClient httpClient = HttpClient.newBuilder().build();
public void static main(Stringp[] args){
  String url = "https://www.sina.com.cn/";
  HttpRequest request = HttpRequest.newBuilder(new URI(url));
      .header("User-Agent","java httpclient").header("Accept","*/*")
      .timeout(Duration.ofSeconds(5))
      .version(Version.HTTP_2)
      .build();
  HttpResponse<String> response = httpClient.send(request,
      HttpResponse.BodyHandlers.ofString());  // HttpResponse.BodyHandlers.ofByteArray(),获取图片这样的二进制内容
      // 如果响应的内容很大，不希望一次性全部加载到内存，可以使用HttpResponse.BodyHandlers.ofInputStream()获取一个InputStream流。
  Map<String,List<String>> headers = response.headers().map();
  for(String header : headers.keySet()){
    System.out.println(header + ": "+headers.get(header).get(0));
  }
  System.out.println(response.body().substring(0,1024)+"...");
}

// POST 请求
String url = "http://www.example.com/login";
String body = "username=bob&password=123456";
HttpRequest request = HttpRequest.newBuilder(new URI(url))
    // 设置Header:
    .header("Accept", "*/*")
    .header("Content-Type", "application/x-www-form-urlencoded")
    // 设置超时:
    .timeout(Duration.ofSeconds(5))
    // 设置版本:
    .version(Version.HTTP_2)
    // 使用POST并设置Body:
    .POST(BodyPublishers.ofString(body, StandardCharsets.UTF_8)).build();
HttpResponse<String> response = httpClient.send(request, HttpResponse.BodyHandlers.ofString());
String s = response.body();
```

# Apache HttpClient 4

HttpComponents 也就是以前的 httpclient 项目，可以用来提供高效的、最新的、功能丰富的支持 HTTP 协议的客户端/服务器编程工具包，并且它支持 HTTP 协议最新的版本和建议。

HttpComponents 项目下的 HttpClient 是为扩展而设计的，同时提供了对基本 HTTP 协议的强大支持，对于构建 HTTP 感知的客户端应用程序（例如 Web 浏览器，Web 服务客户端或利用或扩展 HTTP 协议进行分布式通信的系统）

基于标准的纯 Java，HTTP 版本 1.0 和 1.1 的实现
在可扩展的 OO 框架中完全实现所有 HTTP 方法（GET，POST，PUT，DELETE，HEAD，OPTIONS 和 TRACE）。
支持使用 HTTPS（基于 SSL 的 HTTP）协议进行加密。
通过 HTTP 代理的透明连接。
通过 CONNECT 方法通过 HTTP 代理建立的隧道 HTTPS 连接。
Basic, Digest，NTLMv1，NTLMv2，NTLM2 会话，SNPNEGO，Kerberos 身份验证方案。
自定义身份验证方案的插件机制。
可插拔的安全套接字工厂，使使用第三方解决方案更加容易
支持在多线程应用程序中使用的连接管理。支持设置最大总连接数以及每个主机的最大连接数。检测并关闭陈旧的连接。
自动 Cookie 处理
自定义 Cookie 策略的插件机制。
请求输出流，以避免通过直接流到服务器的套接字来缓冲任何内容主体。
响应输入流通过直接从套接字流传输到服务器来有效地读取响应主体。
在 HTTP / 1.0 中使用 KeepAlive 的持久连接以及在 HTTP / 1.1 中的持久性
直接访问服务器发送的响应代码和 header。
设置连接超时的能力。
支持 HTTP / 1.1 响应缓存。

http://hc.apache.org/httpcomponents-client-ga/

## 依赖

```xml
<dependency>
    <groupId>org.apache.httpcomponents</groupId>
    <artifactId>httpclient</artifactId>
</dependency>
```

## 配置类

```java
@Configuration
@ConfigurationProperties(prefix = "httpclient")
@Data
public class HttpClientConfig {

    private Integer maxTotal;

    private Integer defaultMaxPerRoute;
    private Integer connectTimeout;

    private Integer connectionRequestTimeout;

    private Integer socketTimeout;

    /**
     * HttpClient连接池
     * @return
     */
    @Bean
    public HttpClientConnectionManager httpClientConnectionManager() {
        PoolingHttpClientConnectionManager connectionManager = new PoolingHttpClientConnectionManager();
        connectionManager.setMaxTotal(maxTotal);
        connectionManager.setDefaultMaxPerRoute(defaultMaxPerRoute);
        return connectionManager;
    }

    /**
     * 创建RequestConfig
     * @return
     */
    @Bean
    public RequestConfig requestConfig() {
        return RequestConfig.custom().setConnectTimeout(connectTimeout)
            .setConnectionRequestTimeout(connectionRequestTimeout).setSocketTimeout(socketTimeout)
            .build();
    }

    /**
     * 创建HttpClient
     * @param manager
     * @param config
     * @return
     */
    @Bean
    public CloseableHttpClient httpClient(HttpClientConnectionManager manager, RequestConfig config) {
        return HttpClientBuilder.create().setConnectionManager(manager).setDefaultRequestConfig(config)
            .build();
    }
}
```

## 工具类

```java
@Component
@Slf4j
public class HttpClientUtil {

    @Autowired
    private CloseableHttpClient httpClient;

    public  String doGet(String url, Map<String, String> param,Map<String,String> headers) {

        String resultString = "";
        CloseableHttpResponse response = null;
        try {
            // 创建uri
            URIBuilder builder = new URIBuilder(url);
            if (param != null) {
                for (String key : param.keySet()) {
                    builder.addParameter(key, param.get(key));
                }
            }
            builder.setCharset(Charset.forName("utf-8"));
            URI uri = builder.build();

            // 创建http GET请求
            HttpGet httpGet = new HttpGet(uri);
            if(headers!= null && !headers.isEmpty()){
                headers.forEach((name,value)->httpGet.setHeader(name,value));
            }

            // 执行请求
            response = httpClient.execute(httpGet);
           return EntityUtils.toString(response.getEntity(), "UTF-8");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            try {
                close(response);
                httpClient.close();
            } catch (IOException e) {
               log.error("doGet url:"+url+" fail,cause :"+e.getMessage(),e);
            }
        }
        return resultString;
    }

    public  String doGet(String url) {
        return doGet(url, null,null);
    }

    public  String doPost(String url, Map<String, String> param,Map<String,String> headers) {
        CloseableHttpResponse response = null;
        String resultString = "";
        try {
            // 创建Http Post请求
            HttpPost httpPost = new HttpPost(url);
            // 创建参数列表
            if (param != null) {
                List<NameValuePair> paramList = new ArrayList<>();
                for (String key : param.keySet()) {
                    paramList.add(new BasicNameValuePair(key, param.get(key)));
                }
                // 模拟表单
                UrlEncodedFormEntity entity = new UrlEncodedFormEntity(paramList,Charset.forName("UTF-8"));
                httpPost.setEntity(entity);
            }

            if(headers!= null && !headers.isEmpty()){
                headers.forEach((name,value)->httpPost.setHeader(name,value));
            }

            // 执行http请求
            response = httpClient.execute(httpPost);
            resultString = EntityUtils.toString(response.getEntity(), "utf-8");
        } catch (Exception e) {
            log.error("doPost url:"+url+" fail,cause :"+e.getMessage(),e);
        } finally {
            close(response);
        }

        return resultString;
    }

    private void close(CloseableHttpResponse response) {
        try {
            if(response != null){
                response.close();
            }
        } catch (IOException e) {
         log.error(e.getMessage(),e);
        }
    }

    public  String doPost(String url) {
        return doPost(url, null,null);
    }

    public  String doPostJson(String url, String json) {
        CloseableHttpResponse response = null;
        String resultString = "";
        try {
            // 创建Http Post请求
            HttpPost httpPost = new HttpPost(url);
            // 创建请求内容
            StringEntity entity = new StringEntity(json, ContentType.APPLICATION_JSON);
            httpPost.setEntity(entity);
            // 执行http请求
            response = httpClient.execute(httpPost);
            resultString = EntityUtils.toString(response.getEntity(), "utf-8");
        } catch (Exception e) {
            log.error("doJson url:"+url+" fail,cause :"+e.getMessage(),e);
        } finally {
            close(response);
        }

        return resultString;
    }
}
```

# Apache HttpClient 5

## 依赖

```xml
<!-- https://mvnrepository.com/artifact/org.apache.httpcomponents.client5/httpclient5 -->
<dependency>
    <groupId>org.apache.httpcomponents.client5</groupId>
    <artifactId>httpclient5</artifactId>
    <version>5.1.3</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.apache.httpcomponents.client5/httpclient5-fluent -->
<!-- 使用 Apache HttpClient 5 提供的 Fluent API 可以更便捷的发起请求，但是可操作的地方较少。 -->
<dependency>
    <groupId>org.apache.httpcomponents.client5</groupId>
    <artifactId>httpclient5-fluent</artifactId>
    <version>5.1.3</version>
</dependency>
```

## 基本 get 和 post 请求

```java
//  get("http://httpbin.org/get");
public static String get(String url) {
  String resultContent = null;
  HttpGet httpGet = new HttpGet(url);
  // GET 请求参数
  nvps.add(new BasicNameValuePair("username", "wdbyte.com"));
  nvps.add(new BasicNameValuePair("password", "secret"));
  // 增加到请求 URL 中
  try {
      URI uri = new URIBuilder(new URI(url))
          .addParameters(nvps)
          .build();
      httpGet.setUri(uri);
  } catch (URISyntaxException e) {
      throw new RuntimeException(e);
  }
  // 返回
  try (CloseableHttpClient httpclient = HttpClients.createDefault()) {
      try (CloseableHttpResponse response = httpclient.execute(httpGet)) {
          // 获取状态码
          System.out.println(response.getVersion()); // HTTP/1.1
          System.out.println(response.getCode()); // 200
          System.out.println(response.getReasonPhrase()); // OK
          HttpEntity entity = response.getEntity();
          // 获取响应信息
          resultContent = EntityUtils.toString(entity);
      }
  } catch (IOException | ParseException e) {
      e.printStackTrace();
  }
  return resultContent;
}
// fluent get
public static String get(String url) {
  String result = null;
  try {
      Response response = Request.get(url).execute();
      result = response.returnContent().asString();
  } catch (IOException e) {
      e.printStackTrace();
  }
  return result;
}
// post
public static String post(String url) {
  String result = null;
  HttpPost httpPost = new HttpPost(url);
  // httpPost.setEntity(new StringEntity(jsonBody, ContentType.APPLICATION_JSON)); // json参数
  // 表单参数
  List<NameValuePair> nvps = new ArrayList<>();
  // POST 请求参数
  nvps.add(new BasicNameValuePair("username", "wdbyte.com"));
  nvps.add(new BasicNameValuePair("password", "secret"));
  httpPost.setEntity(new UrlEncodedFormEntity(nvps));
  try (CloseableHttpClient httpclient = HttpClients.createDefault()) {
      try (CloseableHttpResponse response = httpclient.execute(httpPost)) {
          System.out.println(response.getVersion()); // HTTP/1.1
          System.out.println(response.getCode()); // 200
          System.out.println(response.getReasonPhrase()); // OK

          HttpEntity entity = response.getEntity();
          // 获取响应信息
          result = EntityUtils.toString(entity);
          // 确保流被完全消费
          EntityUtils.consume(entity);
      }
  } catch (IOException | ParseException e) {
      e.printStackTrace();
  }
  return result;
}
// fluent post
public static String post(String url) {
  String result = null;
  Request request = Request.post(url);
  // POST 请求参数
  request.bodyForm(
      new BasicNameValuePair("username", "wdbyte.com"),
      new BasicNameValuePair("password", "secret"));
  try {
      result = request.execute().returnContent().asString();
  } catch (IOException e) {
      e.printStackTrace();
  }
  return result;
}
```

## 设置超时

```java
package com.wdbyte.httpclient;

import java.io.IOException;

import org.apache.hc.client5.http.classic.methods.HttpGet;
import org.apache.hc.client5.http.config.RequestConfig;
import org.apache.hc.client5.http.impl.classic.CloseableHttpClient;
import org.apache.hc.client5.http.impl.classic.CloseableHttpResponse;
import org.apache.hc.client5.http.impl.classic.HttpClients;
import org.apache.hc.core5.http.HttpEntity;
import org.apache.hc.core5.http.ParseException;
import org.apache.hc.core5.http.io.entity.EntityUtils;
import org.apache.hc.core5.util.Timeout;

/**
* @author https://www.wdbyte.com
 */
public class HttpClient5GetWithTimeout {

    public static void main(String[] args) {
        String result = get("http://httpbin.org/get");
        System.out.println(result);
    }

    public static String get(String url) {
        String resultContent = null;
        // 设置超时时间
        RequestConfig config = RequestConfig.custom()
            .setConnectTimeout(Timeout.ofMilliseconds(5000L))
            .setConnectionRequestTimeout(Timeout.ofMilliseconds(5000L))
            .setResponseTimeout(Timeout.ofMilliseconds(5000L))
            .build();
        // 请求级别的超时
        HttpGet httpGet = new HttpGet(url);
        //httpGet.setConfig(config);
        //try (CloseableHttpClient httpclient = HttpClients.createDefault()) {
        // 客户端级别的超时
        try (CloseableHttpClient httpclient = HttpClients.custom().setDefaultRequestConfig(config).build()) {
            try (CloseableHttpResponse response = httpclient.execute(httpGet)) {
                // 获取状态码
                System.out.println(response.getVersion()); // HTTP/1.1
                System.out.println(response.getCode()); // 200
                System.out.println(response.getReasonPhrase()); // OK
                HttpEntity entity = response.getEntity();
                // 获取响应信息
                resultContent = EntityUtils.toString(entity);
            }
        } catch (IOException | ParseException e) {
            e.printStackTrace();
        }
        return resultContent;
    }

}

```

## 异步请求

```java
try (CloseableHttpAsyncClient httpclient = HttpAsyncClients.createDefault()) {
  // 开始 http clinet
  httpclient.start();
  // 执行请求
  SimpleHttpRequest request1 = SimpleHttpRequests.get(url);
  //1. 等待直到返回完毕
  Future<SimpleHttpResponse> future = httpclient.execute(request1, null);
  SimpleHttpResponse response1 = future.get();

  //2. 根据请求响应情况进行回调操作
  CountDownLatch latch = new CountDownLatch(1);
  SimpleHttpRequest request = SimpleHttpRequests.get(url);
  httpclient.execute(request, new FutureCallback<SimpleHttpResponse>() {
      @Override
      public void completed(SimpleHttpResponse response2) {
          latch.countDown();
          System.out.println("getAsync2:" + request.getRequestUri() + "->" + response2.getCode());
      }

      @Override
      public void failed(Exception ex) {
          latch.countDown();
          System.out.println("getAsync2:" + request.getRequestUri() + "->" + ex);
      }

      @Override
      public void cancelled() {
          latch.countDown();
          System.out.println("getAsync2:" + request.getRequestUri() + " cancelled");
      }

  });
  latch.await();
  // 3.根据请求响应情况进行回调操作
  CountDownLatch latch = new CountDownLatch(1);
  AsyncRequestProducer producer = AsyncRequestBuilder.get("http://httpbin.org/get").build();
  AbstractCharResponseConsumer<HttpResponse> consumer3 = new AbstractCharResponseConsumer<HttpResponse>() {
      HttpResponse response;
      @Override
      protected void start(HttpResponse response, ContentType contentType) throws HttpException, IOException {
          System.out.println("getAsync3: 开始响应....");
          this.response = response;
      }
      @Override
      protected int capacityIncrement() {
          return Integer.MAX_VALUE;
      }
      @Override
      protected void data(CharBuffer data, boolean endOfStream) throws IOException {
          System.out.println("getAsync3: 收到数据....");
          // Do something useful
      }
      @Override
      protected HttpResponse buildResult() throws IOException {
          System.out.println("getAsync3: 接收完毕...");
          return response;
      }
      @Override
      public void releaseResources() {
      }

  };
  httpclient.execute(producer, consumer3, new FutureCallback<HttpResponse>() {
      @Override
      public void completed(HttpResponse response) {
          latch.countDown();
          System.out.println("getAsync3: "+request.getRequestUri() + "->" + response.getCode());
      }
      @Override
      public void failed(Exception ex) {
          latch.countDown();
          System.out.println("getAsync3: "+request.getRequestUri() + "->" + ex);
      }
      @Override
      public void cancelled() {
          latch.countDown();
          System.out.println("getAsync3: "+request.getRequestUri() + " cancelled");
      }

  });
  latch.await();
}
```

## Cookie

```java
try (final CloseableHttpClient httpclient = HttpClients.createDefault()) {
  // 创建一个本地的 Cookie 存储
  final CookieStore cookieStore = new BasicCookieStore();
  // BasicClientCookie clientCookie = new BasicClientCookie("name", "www.wdbyte.com");
  // clientCookie.setDomain("http://httpbin.org/cookies");
  // 过期时间
  // clientCookie.setExpiryDate(new Date());
  // 添加到本地 Cookie
  // cookieStore.addCookie(clientCookie);

  // 创建本地 HTTP 请求上下文 HttpClientContext
  final HttpClientContext localContext = HttpClientContext.create();
  // 绑定 cookieStore 到 localContext
  localContext.setCookieStore(cookieStore);

  final HttpGet httpget = new HttpGet("http://httpbin.org/cookies/set/cookieName/www.wdbyte.com");
  System.out.println("执行请求 " + httpget.getMethod() + " " + httpget.getUri());

  // 获取 Coolie 信息
  try (final CloseableHttpResponse response = httpclient.execute(httpget, localContext)) {
      System.out.println("----------------------------------------");
      System.out.println(response.getCode() + " " + response.getReasonPhrase());
      final List<Cookie> cookies = cookieStore.getCookies();
      for (int i = 0; i < cookies.size(); i++) {
          System.out.println("Local cookie: " + cookies.get(i));
      }
      EntityUtils.consume(response.getEntity());
  }
}
```

## 读文件

```java
String params = "/Users/darcy/params.json";

try (final CloseableHttpClient httpclient = HttpClients.createDefault()) {
  final HttpPost httppost = new HttpPost("http://httpbin.org/post");

  final InputStreamEntity reqEntity = new InputStreamEntity(new FileInputStream(params), -1,
      ContentType.APPLICATION_JSON);
  // 也可以使用 FileEntity 的形式
  // FileEntity reqEntity = new FileEntity(new File(params), ContentType.APPLICATION_JSON);

  httppost.setEntity(reqEntity);

  System.out.println("执行请求 " + httppost.getMethod() + " " + httppost.getUri());
  try (final CloseableHttpResponse response = httpclient.execute(httppost)) {
      System.out.println("----------------------------------------");
      System.out.println(response.getCode() + " " + response.getReasonPhrase());
      System.out.println(EntityUtils.toString(response.getEntity()));
  }
}
```

## 拦截器

```java
/**
 * 展示如何在请求和响应时进行拦截进行自定义处理。
 */
public class HttpClient5Interceptors {

    public static void main(final String[] args) throws Exception {
        try (final CloseableHttpClient httpclient = HttpClients.custom()
            // 添加一个请求 id 到请求 header
            .addRequestInterceptorFirst(new HttpRequestInterceptor() {
                private final AtomicLong count = new AtomicLong(0);
                @Override
                public void process(
                    final HttpRequest request,
                    final EntityDetails entity,
                    final HttpContext context) throws HttpException, IOException {
                    request.setHeader("request-id", Long.toString(count.incrementAndGet()));
                }
            })
            .addExecInterceptorAfter(ChainElement.PROTOCOL.name(), "custom", new ExecChainHandler() {
                // 请求 id 为 2 的，模拟 404 响应，并自定义响应的内容。
                @Override
                public ClassicHttpResponse execute(
                    final ClassicHttpRequest request,
                    final Scope scope,
                    final ExecChain chain) throws IOException, HttpException {

                    final Header idHeader = request.getFirstHeader("request-id");
                    if (idHeader != null && "2".equalsIgnoreCase(idHeader.getValue())) {
                        final ClassicHttpResponse response = new BasicClassicHttpResponse(HttpStatus.SC_NOT_FOUND,
                            "Oppsie");
                        response.setEntity(new StringEntity("bad luck", ContentType.TEXT_PLAIN));
                        return response;
                    } else {
                        return chain.proceed(request, scope);
                    }
                }
            })
            .build()) {

            for (int i = 0; i < 3; i++) {
                final HttpGet httpget = new HttpGet("http://httpbin.org/get");

                try (final CloseableHttpResponse response = httpclient.execute(httpget)) {
                    System.out.println("----------------------------------------");
                    System.out.println("执行请求 " + httpget.getMethod() + " " + httpget.getUri());
                    System.out.println(response.getCode() + " " + response.getReasonPhrase());
                    System.out.println(EntityUtils.toString(response.getEntity()));
                }
            }
        }
    }

}

```

# restTemplate

spring 框架提供的 RestTemplate 类可用于在应用中调用 rest 服务，它简化了与 http 服务的通信方式，统一了 RESTful 的标准，封装了 http 链接,大大提高客户端的编写效率。相较于之前常用的 HttpClient，RestTemplate 是一种更优雅的调用 RESTful 服务的方式。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```
```java
@Bean
RestTemplate getRestTemplate(){
    return new RestTemplate();
}
```
1. `RestTemplate` 默认依赖 JDK 提供的 HttpURLConnection，如果有需要的话也可以通过 setRequestFactory 方法替换为例如 Apache HttpComponents、Netty 或 OkHttp 等其它 HTTP library。
2. get : `restTemplate.getForEntity()`
3. post: `restTemplate.postForEntity()`
4. 其他: `exchange.exchange()`

# webclient

webClient 是一个响应式客户端，它提供了 restTemplate 的替代方法。它公开了一个功能齐全、流畅的 api，并依赖于非阻塞 I / O，使其能够比 restTemplate 更高效地支持高并发性。webclient 非常适合流式的传输方案，并且依赖于较低级别的 HTTP 客户端库来执行请求，是可插拔的。

webclient 特点

1. 非阻塞，Reactive 的，并支持更高的并发性和更少的硬件资源。
2. 提供利用 Java 8 lambdas 的函数 API。
3. 支持同步和异步方案。
4. 支持从服务器向上或向下流式传输。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
```
