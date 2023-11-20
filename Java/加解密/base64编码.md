# Base64 编码

URL 编码是对字符进行编码，表示成%xx 的形式，而 Base64 编码是对二进制数据进行编码，表示成文本格式。

Base64 编码可以**把任意长度的二进制数据变为纯文本**，且只包含`A~Za~z0~9+/=`这些字符。

它的原理是**把 3 字节的二进制数据按 6bit 一组，用 4 个 int 整数表示，然后查表，把 int 整数用索引对应到字符，得到编码后的字符串**。字符 A~Z 对应索引 0~25，字符 a~z 对应索引 26~51，字符 0~9 对应索引 52~61，最后两个索引 62、63 分别用字符+和/表示。

如果输入的**byte[]数组长度不是 3 的整数倍**肿么办？这种情况下，需要**对输入的末尾补一个或两个 0x00，编码后，在结尾加一个=表示补充了 1 个 0x00，加两个=表示补充了 2 个 0x00，解码的时候，去掉末尾补充的一个或两个 0x00 即可**。

在 Java 中，二进制数据就是`byte[]`数组。Java 标准库提供了`Base64`来对 byte[]数组进行编解码;

1. `Base64.getEncoder().encodeToString(input)` 标准 base64 编码
2. `Base64.getDecoder().decode("5Lit")` 标准 base64 解码
3. `Base64.getUrlEncoder()` `getUrlDecoder()` 因为标准的 Base64 编码会出现`+、/和=`，所以不适合把 Base64 编码后的字符串放到 URL 中,一种针对 URL 的 Base64 编码可以在 URL 中使用的 Base64 编码，它仅仅是把`+`变成`-`，`/`变成`_`
4. Base64 编码的缺点是传输效率会降低，因为它把原始数据的长度增加了 1/3。
5. 如果把 Base64 的 64 个字符编码表换成 32 个、48 个或者 58 个，就可以使用 Base32 编码，Base48 编码和 Base58 编码。字符越少，编码的效率就会越低。

```java

import java.util.Base64;

@Test
public void encode() {
    byte[] input = new byte[]{(byte) 0xe4, (byte) 0xb8, (byte) 0xad};
    System.out.println(Arrays.toString(input)); // [-28, -72, -83]
    String base64str = Base64.getEncoder().encodeToString(input);
    System.out.println(base64str); // 5Lit
}

@Test
public void decode() {
    byte[] output = Base64.getDecoder().decode("5Lit");
    System.out.println(Arrays.toString(output)); // [-28, -72, -83]
}
```