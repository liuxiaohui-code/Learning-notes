# webservice
## 服务端
Java 中共有三种WebService 规范，分别是JAXM&SAAJ、JAX-WS（JAX-RPC）、JAX-RS。

JAXM&SAAJ ： 基于javax.messaging.*包 （需要手动下载这个包）
JAX-WS（JAX-RPC）：基于javax.xml.ws.*包（原生java支持）
JAX-RS ： 基于java.ws.rs.*包（restful风格）
### springboot 整合
```xml
<!-- webservice-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web-services</artifactId>
</dependency>
<dependency>
    <groupId>org.apache.cxf</groupId>
    <artifactId>cxf-rt-frontend-jaxws</artifactId>
    <version>3.2.6</version>
</dependency>
<dependency>
    <groupId>org.apache.cxf</groupId>
    <artifactId>cxf-rt-transports-http</artifactId>
    <version>3.2.6</version>
</dependency>
<dependency>
    <groupId>org.apache.cxf</groupId>
    <artifactId>cxf-spring-boot-starter-jaxws</artifactId>
    <version>3.2.6</version>
</dependency>
<dependency>
    <groupId>org.hibernate</groupId>
    <artifactId>hibernate-validator</artifactId>
    <version>5.4.1.Final</version>
</dependency>
```
```java
package org.example.webservice;

import javax.jws.WebService;

@WebService(name = "MyWebService", // 暴露服务名称
        targetNamespace = "http://service.example.org"// 命名空间,一般是接口的包名倒序
)
public interface MyWebService {
    String sayHello();
}
package org.example.webservice;

import javax.jws.WebService;

@WebService(serviceName = "MyWebService", // 与接口中指定的name一致
        targetNamespace = "http://service.example.org", // 与接口中的命名空间一致,一般是接口的包名倒
        endpointInterface = "org.example.webservice.MyWebService"// 接口地址
)
public class MyWebServiceImpl implements MyWebService {
    @Override
    public String sayHello() {
        return "hello webservice";
    }
}
package org.example.webservice;

import org.apache.cxf.Bus;
import org.apache.cxf.bus.spring.SpringBus;
import org.apache.cxf.jaxws.EndpointImpl;
import org.apache.cxf.transport.servlet.CXFServlet;
import org.springframework.boot.web.servlet.ServletRegistrationBean;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.xml.ws.Endpoint;

@Configuration
public class MyWebServiceConfig {
    // @Bean
    // public ServletRegistrationBean dispatcherServlet() {
    //     return new ServletRegistrationBean(new CXFServlet(),"/demo/*");
    // }
    @Bean(name = "cxfServlet")  // 注入servlet bean name不能dispatcherServlet ,否则会覆盖dispatcherServlet
    public ServletRegistrationBean<CXFServlet> cxfServlet() {
        return new ServletRegistrationBean<>(new CXFServlet(), "/demo/*");
    }

    @Bean(name = Bus.DEFAULT_BUS_ID)
    public SpringBus springBus() {
        return new SpringBus();
    }

    @Bean
    public MyWebService demoService() {
        return new MyWebServiceImpl();
    }

    @Bean
    public Endpoint endpoint() {
        EndpointImpl endpoint = new EndpointImpl(springBus(), demoService());
        endpoint.publish("/api");
        return endpoint;
    }
}
package org.example.webservice;

import org.apache.cxf.binding.soap.SoapMessage;
import org.apache.cxf.headers.Header;
import org.apache.cxf.helpers.DOMUtils;
import org.apache.cxf.interceptor.Fault;
import org.apache.cxf.phase.AbstractPhaseInterceptor;
import org.apache.cxf.phase.Phase;
import org.w3c.dom.Document;
import org.w3c.dom.Element;

import javax.xml.namespace.QName;
import java.util.List;

public class ClientLoginInterceptor extends AbstractPhaseInterceptor<SoapMessage> {


    public ClientLoginInterceptor(String UserName, String UserPass) {
        super(Phase.PREPARE_SEND);
        this.UserName = UserName;
        this.UserPass = UserPass;
    }


    private String UserName;
    private String UserPass;


    public String getUserName() {
        return UserName;
    }


    public void setUserName(String userName) {
        UserName = userName;
    }


    public String getUserPass() {
        return UserPass;
    }


    public void setUserPass(String userPass) {
        UserPass = userPass;
    }

    @Override
    public void handleMessage(SoapMessage soap) throws Fault {
        List<Header> headers = soap.getHeaders();
        Document doc = DOMUtils.createDocument();
        Element auth = doc.createElementNS("http://tempuri.org/", "SecurityHeader");
        Element UserName = doc.createElement("UserName");
        Element UserPass = doc.createElement("UserPass");

        UserName.setTextContent(this.UserName);
        UserPass.setTextContent(this.UserPass);

        auth.appendChild(UserName);
        auth.appendChild(UserPass);

        headers.add(0, new Header(new QName("SecurityHeader"), auth));
    }


}

```
## 客户端
WebService不适用场景：
考虑性能时不建议使用WebService；
重构程序下不建议使用WebService；

客户接口说明：
客户给到对应webservice的url地址，后缀为wsdl；

里面有两个url：
　　一个链接是wsdl后缀；
　　一个url是singleWsdl后缀；
这两个的区别：
　　wsdl：（嵌入式WSDL）不能使用它来生成WSDL2java包和不能使用JAX-WS创建连接；
　　singleWsdl：（单个WSDL）它可以使用CXF 3.0的WSDL2java生成Java包，并可以使用JAX-WS创建连接；

其中wsdl文件：
```xml
<wsdl:operation name="InfInterfaceForJson">
	<soap:operation soapAction="http://XXXXX/InfInterfaceForJson" style="document"/>
	<wsdl:input>
		<soap:body use="literal"/>
	</wsdl:input>
	<wsdl:output>
		<soap:body use="literal"/>
	</wsdl:output>
</wsdl:operation>
```
这个文件定义了一些方法的说明，上面是我们要调用的方法，
可以看出有输入和输出；
这个文件要关注的点：
1：wsdl:operation：需要调用的方法名
　　　表示客户的接口，一般都会有多少个接口可以跟客户端交流；
2：soap:operation soapAction：
　　　　 如果是.net生成的服务，soapAction是有值的
3：input：表示调用这个方法需要传入的参数
4：output：表示调用这个方法返回的结果。

其中singleWsdl文件：

```xml
<xs:element name="InfInterfaceForJson">
	<xs:complexType>
		<xs:sequence>
			<xs:element minOccurs="0" name="function" nillable="true" type="xs:string"/>
			<xs:element minOccurs="0" name="json" nillable="true" type="xs:string"/>
		</xs:sequence>
	</xs:complexType>
</xs:element>
<xs:element name="InfInterfaceForJsonResponse">
	<xs:complexType>
		<xs:sequence>
			<xs:element minOccurs="0" name="InfInterfaceForJsonResult" nillable="true" type="xs:string"/>
		</xs:sequence>
	</xs:complexType>
</xs:element>
```

说明：
这个文件需要关注的点：
1：targetNamespace=“http://XXXXX”
2：InfInterfaceForJson这个方法是客户提供的通用的json调用方法，
因此我们调用客户的webservice中这个方法，这里面有两个入惨【function】【json】
function：就是我们最终要调用的目标方法名字（客户会给到）
json：目标方法的传参（我们跟客户对接是用的json格式传参的）
3：InfInterfaceForJsonResponse这个是通用的返回；

### AXIS调用远程的webservice

### SOAP调用远程的webservice

### wsdl2java把WSDL文件转成本地类，然后像本地类一样使用

### URL Connection方式

### springboot整合
当然http方式也有很多，原生httpconnectin，httpclient，okhttp等都可以，
springboot也有支持的，封装了上面几个的，具体用哪种方式都可以；

```xml
<!-- 下面三个依赖，是解决缺少 jdk目录中的 tools.jar 包问题 start -->
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
    <version>2.2.1</version>
</dependency>
<dependency>
    <groupId>javax.xml</groupId>
    <artifactId>jaxb-impl</artifactId>
    <version>2.1</version>
</dependency>
<dependency>
    <groupId>com.sun.xml.bind</groupId>
    <artifactId>jaxb-xjc</artifactId>
    <version>2.2.1.1</version>
</dependency>
<!-- 上面三个依赖，是解决缺少 jdk目录中的 tools.jar 包问题 end -->
```
```java

package org.example.webservice;

import com.epoint.shenhua.service.SRMDMDROOT;
import org.apache.cxf.endpoint.Client;
import org.apache.cxf.jaxws.endpoint.dynamic.JaxWsDynamicClientFactory;
import org.apache.cxf.transport.http.HTTPConduit;
import org.apache.cxf.transports.http.configuration.HTTPClientPolicy;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@RequestMapping("/webservice")
public class MyWebServiceController {
    @GetMapping("/send")
    public String doWebService(){
        //创建动态客户端
        JaxWsDynamicClientFactory factory = JaxWsDynamicClientFactory.newInstance();
        Client client = factory.createClient("http://localhost:13131/demo/api?wsdl");
        // 需要密码的情况需要加上用户名和密码
        // client.getOutInterceptors().add(new ClientLoginInterceptor("srm","lX6Ybm1Q"));
        HTTPConduit conduit = (HTTPConduit) client.getConduit();
        HTTPClientPolicy httpClientPolicy = new HTTPClientPolicy();
        httpClientPolicy.setConnectionTimeout(2000);  //连接超时
        httpClientPolicy.setAllowChunking(false);    //取消块编码
        httpClientPolicy.setReceiveTimeout(120000);     //响应超时
        conduit.setClient(httpClientPolicy);
        //client.getOutInterceptors().addAll(interceptors);//设置拦截器
        try{
            Object[] objects = new Object[0];
            // invoke("方法名",参数1,参数2,参数3....);
            objects = client.invoke("sayHello", "sujin");
            System.out.println("返回数据:" + objects[0]);
            return objects[0].toString();
        }catch (Exception e){
            e.printStackTrace();
            return "发送webservice异常";
        }
    }
    @GetMapping("/sendSrm")
    public String doWebServiceSrm(){
        //创建动态客户端
        JaxWsDynamicClientFactory factory = JaxWsDynamicClientFactory.newInstance();
        Client client = factory.createClient("http://10.126.17.198:8080/bidcsshwebservice/services/Srm2ShgcDmdJHService?wsdl");
        // 需要密码的情况需要加上用户名和密码
        // client.getOutInterceptors().add(new ClientLoginInterceptor("srm","lX6Ybm1Q"));
        HTTPConduit conduit = (HTTPConduit) client.getConduit();
        HTTPClientPolicy httpClientPolicy = new HTTPClientPolicy();
        httpClientPolicy.setConnectionTimeout(2000);  //连接超时
        httpClientPolicy.setAllowChunking(false);    //取消块编码
        httpClientPolicy.setReceiveTimeout(120000);     //响应超时
        conduit.setClient(httpClientPolicy);
        //client.getOutInterceptors().addAll(interceptors);//设置拦截器
        try{
            Object[] objects = new Object[0];
            // invoke("方法名",参数1,参数2,参数3....);
            String x = "<?xml version=\"1.0\" encoding=\"utf-8\"?><ROOT>\n" +
                    "<TID>C3FCA890D9D6477780A25D6ED4A2FD25</TID>\n" +
                    "<TSTAMP>20231117150741741</TSTAMP>\n" +
                    "<TINF>S2IJGQR</TINF>\n" +
                    "<PACKAGE>\n" +
                    "<PACK_BH>CEZB230000545001</PACK_BH>\n" +
                    "<PACK_JGLX>0</PACK_JGLX>\n" +
                    "<TEXT/>\n" +
                    "<PACK_PBQRH>http://10.126.22.23:8000/sap/ebp/zsrm_cfloderebp?srmDown&amp;amp;sap-client=120&amp;amp;docid=FA163ED44CA11EEEA08FE3814CECBB79&amp;amp;sap-language=ZH</PACK_PBQRH>\n" +
                    "<VENDORS/>\n" +
                    "</PACKAGE>\n" +
                    "</ROOT>";
            objects = client.invoke("dmdToShgcOperate", "C3FCA890D9D6477780A25D6ED4A2FD25",
                    "S2IJGQR","test",x);
            System.out.println(objects.length+"\t"+objects+" 返回数据:" + objects[0]);
            return ((SRMDMDROOT)objects[0]).toString();
        }catch (Exception e){
            e.printStackTrace();
            return "发送webservice异常";
        }
    }
}

```

### HttpURLConnection 方式
依照上面的客户webservice接口说明：
参数说明：
String urlWsdl：webservice服务地址
String namespace,：
String SOAPAction：
String interfaceName：调用webservice的哪个方法
String function：最终调用的方法名
String json：方法传参
```java
import com.alibaba.fastjson.JSONObject;
import lombok.extern.slf4j.Slf4j;
import org.dom4j.Document;
import org.dom4j.io.SAXReader;

import java.io.*;
import java.net.HttpURLConnection;
import java.net.URL;
import java.util.HashMap;
import java.util.Map;

/**
 * webservice 请求工具类
 */
@Slf4j
public class CallWebServiceUtils {

    private static String soapStr = "<?xml version=\"1.0\" encoding=\"UTF-8\"?>" +
            "<soapenv:Envelope xmlns:soapenv=\"http://schemas.xmlsoap.org/soap/envelope/\" xmlns:web=\"namespaceData\">" +
            "   <soapenv:Header/>" +
            "   <soapenv:Body>" +
            "       <web:interfaceNameData>" +
            "           <web:function>functionData</web:function>" +
            "           <web:json>jsonData</web:json>" +
            "       </web:interfaceNameData>" +
            "   </soapenv:Body>" +
            "</soapenv:Envelope>";


    public static String callWebServiceByHttp(String urlWsdl,String namespace, String SOAPAction,String interfaceName,String function, String json){
        log.info("callWebServiceByHttp begin json:{}",json);

        /** 关键参数替换 */
        String soapXML = soapStr.replaceAll("namespaceData",namespace)
                .replaceAll("interfaceNameData",interfaceName)
                .replaceAll("functionData",function)
                .replaceAll("jsonData", json);

        HttpURLConnection connection = null;
        try {
            URL url = new URL(urlWsdl);
            connection = (HttpURLConnection)url.openConnection();
            connection.setDoOutput(true);
            connection.setDoInput(true);
            connection.setRequestProperty("Content-Type", "text/xml;charset=UTF-8");
            connection.setRequestProperty("SOAPAction", SOAPAction);
            connection.connect();

            setBytesToOutputStream(connection.getOutputStream(), soapXML.getBytes());

            if (connection.getResponseCode() == HttpURLConnection.HTTP_OK) {
                byte[] b = getBytesFromInputStream(connection.getInputStream());
                String back = new String(b);

                log.info("callWebServiceByHttp end json:{} back:{}", json,JSONObject.toJSONString(back));

                String returnData = parseResult(back);

                log.info("callWebServiceByHttp end returnData:{}",returnData);
                return returnData;
            } else {
                log.error("callWebServiceByHttp json:{} httpURLConnection返回状态码:{}",json,connection.getResponseCode());
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (connection != null){
                connection.disconnect();
            }
        }
        log.error("callWebServiceByHttp error json:{}",json);
        return null;
    }

    /**
     * 向输入流发送数据
     * @param out
     * @param bytes
     * @throws IOException
     */
    private static void setBytesToOutputStream(OutputStream out, byte[] bytes) throws IOException {
        ByteArrayInputStream bais = new ByteArrayInputStream(bytes);
        byte[] b = new byte[1024];
        int len;
        while ((len = bais.read(b)) != -1) {
            out.write(b, 0, len);
        }
        out.flush();
    }

    /**
     * 从输入流获取数据
     * @param in
     * @return
     * @throws IOException
     */
    private static byte[] getBytesFromInputStream(InputStream in) throws IOException {
        ByteArrayOutputStream baos = new ByteArrayOutputStream();
        byte[] b = new byte[1024];
        int len;
        while ((len = in.read(b)) != -1) {
            baos.write(b, 0, len);
        }
        byte[] bytes = baos.toByteArray();
        return bytes;
    }


    /**
     * 解析结果
     * @param s
     * @return
     */
    private static String parseResult(String s) {
        String result = "";
        try {
            Reader file = new StringReader(s);
            SAXReader reader = new SAXReader();

            Map<String, String> map = new HashMap<String, String>();
            // 这个替换为targetNamespace
            map.put("ns", "http://XXXXXX");
            reader.getDocumentFactory().setXPathNamespaceURIs(map);
            Document dc = reader.read(file);
            // 这个节点改成你们对应客户接口返回xml格式的数据节点
            result = dc.selectSingleNode("//ns:InfInterfaceForJsonResult").getText().trim();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return result;
    }
}
```
客户接口返回的是xml：
```xml
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
    <s:Body>
        <InfInterfaceForJsonResponse xmlns="http://XXXX">
            <InfInterfaceForJsonResult>{"code":"Error","errMsg":"数据不存在","data":""}</InfInterfaceForJsonResult>
        </InfInterfaceForJsonResponse>
    </s:Body>
</s:Envelope>
```
### postman 请求webservice

post请求:

url-客户提供的webservice地址: 
`http://XXXX?wsdl`

header:
`SOAPAction`: `http://XXXX/InfInterfaceForJson`
`Content-Type`: `text/xml;charset=utf-8`

请求body：选择raw，XML格式
```xml
<?xml version="1.0" encoding="UTF-8"?>
<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:web="XXXX（targetNamespace）">
<soapenv:Header/>
<soapenv:Body>
    <web:InfInterfaceForJson>
        <web:function>update_user_info</web:function>
        <web:json>{'userId':'123','userName':'李四'}</web:json>
    </web:InfInterfaceForJson>    
</soapenv:Body>
</soapenv:Envelope>
```
调用客户webservice服务中的InfInterfaceForJson方法，其中有两个入参：
function：最终调用的具体方法
json：最终的目标方法的参数

返回xml：
```xml
<s:Envelope xmlns:s="http://schemas.xmlsoap.org/soap/envelope/">
    <s:Body>
        <InfInterfaceForJsonResponse xmlns="http://XXXXX">
            <InfInterfaceForJsonResult>{"code":"200","msg":"更新成功","data":""}</InfInterfaceForJsonResult>
        </InfInterfaceForJsonResponse>
    </s:Body>
</s:Envelope>
```
参考文章：
https://www.cnblogs.com/siqi/p/3475222.html
http://www.what21.com/sys/view/java_webservice_1456896211789.html
https://blog.csdn.net/qq_24808729/article/details/80647429
