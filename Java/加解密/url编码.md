# URL 编码

Java 标准库提供了一个 `URLEncoder` 类来对任意字符串进行 URL 编码.

和标准的 URL 编码稍有不同，URLEncoder 把空格字符编码成`+`，而现在的 URL 编码标准要求空格被编码为`%20`，不过，服务器都可以处理这两种情况。

如果服务器收到 URL 编码的字符串，就可以对其进行解码，还原成原始字符串。Java 标准库的 `URLDecoder` 就可以解码

```java
@Test
public void hello() throws UnsupportedEncodingException {
    String encode = URLEncoder.encode("你好啊sb  +,xxx!", StandardCharsets.UTF_8.name());
    System.out.println(encode);    // %E4%BD%A0%E5%A5%BD%E5%95%8Asb++%2B%2Cxxx%21
}
@Test
public void decode() throws UnsupportedEncodingException {
    String decode = URLDecoder.decode("%E4%BD%A0%E5%A5%BD%E5%95%8Asb++%2B%2Cxxx%21", StandardCharsets.UTF_8.name());
    System.out.println(decode);   // 你好啊sb  +,xxx!
}
```

要特别注意：URL 编码是编码算法，不是加密算法。URL 编码的目的是把任意文本数据编码为%前缀表示的文本，编码后的文本仅包含`A~Za~z0~9-_.*%`，便于浏览器和服务器处理。
