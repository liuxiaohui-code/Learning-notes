# Jackson

```xml
<dependency>
    <groupId>com.fasterxml.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
    <version>2.10.0</version>
</dependency>
```

```java
InputStream input = Main.class.getResourceAsStream("/book.json");
ObjectMapper mapper = new ObjectMapper();
// 反序列化时忽略不存在的JavaBean属性:
mapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false);
Book book = mapper.readValue(input, Book.class);

// 序列化
String json = mapper.writeValueAsString(book);
```

# Gson

# Fastjson

# xml

1. dom4j 性能最佳
2. JDOM 和 DOM 性能不佳，大文档易内存溢出，但可移植。小文档可考虑使用 DOM 和 JDOM
3. XML 文档较大且不考虑移植性问题可用 dom4j
4. 无需解析全部 XML 文档，可用 SAX

# DOM(Document Object Model)

优点：可获取和操作 xml 任意部分的结构和数据
缺点：需加载整个 XML 文档，消耗资源大

Java 提供了 DOM API 来解析 XML，它使用下面的对象来表示 XML 的内容：

Document：代表整个 XML 文档；
Element：代表一个 XML 元素；
Attribute：代表一个元素的某个属性。

```java
InputStream input = Main.class.getResourceAsStream("/book.xml");
DocumentBuilderFactory dbf = DocumentBuilderFactory.newInstance();
DocumentBuilder db = dbf.newDocumentBuilder();
Document doc = db.parse(input);

// 递归打印 dom 数
void printNode(Node n, int indent) {
    for (int i = 0; i < indent; i++) {
        System.out.print(' ');
    }
    switch (n.getNodeType()) {
    case Node.DOCUMENT_NODE: // Document节点
        System.out.println("Document: " + n.getNodeName());
        break;
    case Node.ELEMENT_NODE: // 元素节点
        System.out.println("Element: " + n.getNodeName());
        break;
    case Node.TEXT_NODE: // 文本
        System.out.println("Text: " + n.getNodeName() + " = " + n.getNodeValue());
        break;
    case Node.ATTRIBUTE_NODE: // 属性
        System.out.println("Attr: " + n.getNodeName() + " = " + n.getNodeValue());
        break;
    default: // 其他
        System.out.println("NodeType: " + n.getNodeType() + ", NodeName: " + n.getNodeName());
    }
    for (Node child = n.getFirstChild(); child != null; child = child.getNextSibling()) {
        printNode(child, indent + 1);
    }
}
```

# SAX(Simple API for XML)

使用 DOM 解析 XML 的优点是用起来省事，但它的主要缺点是内存占用太大。

另一种解析 XML 的方式是 SAX。SAX 是 Simple API for XML 的缩写，它是一种基于流的解析方式，边读取 XML 边解析，并以事件回调的方式让调用者获取数据。因为是一边读一边解析，所以无论 XML 有多大，占用的内存都很小。

SAX 解析会触发一系列事件：

startDocument：开始读取 XML 文档；
startElement：读取到了一个元素，例如<book>；
characters：读取到了字符；
endElement：读取到了一个结束的元素，例如</book>；
endDocument：读取 XML 文档结束。
如果我们用 SAX API 解析 XML，Java 代码如下：

```Java
InputStream input = Main.class.getResourceAsStream("/book.xml");
SAXParserFactory spf = SAXParserFactory.newInstance();
SAXParser saxParser = spf.newSAXParser();
// 除了需要传入一个InputStream外，还需要传入一个回调对象，这个对象要继承自DefaultHandler
saxParser.parse(input, new MyHandler());

class MyHandler extends DefaultHandler {
  // 文档根节点
  public void startDocument() throws SAXException {
      print("start document");
  }
  public void endDocument() throws SAXException {
      print("end document");
  }
  // 子元素的开始标签
  public void startElement(String uri, String localName, String qName, Attributes attributes) throws SAXException {
      print("start element:", localName, qName);
  }
  // 子元素的结束标签
  public void endElement(String uri, String localName, String qName) throws SAXException {
      print("end element:", localName, qName);
  }
  // 元素文本内容
  public void characters(char[] ch, int start, int length) throws SAXException {
      print("characters:", new String(ch, start, length));
  }
  // 解析异常
  public void error(SAXParseException e) throws SAXException {
      print("error:", e);
  }
  void print(Object... objs) {
      for (Object obj : objs) {
          System.out.print(obj);
          System.out.print(" ");
      }
      System.out.println();
  }
}
```

如果要读取`<name>`节点的文本，我们就必须在解析过程中根据 `startElement()`和 `endElement()`定位当前正在读取的节点，可以使用栈结构保存，每遇到一个 `startElement()`入栈，每遇到一个 `endElement()`出栈，这样，读到 `characters()`时我们才知道当前读取的文本是哪个节点的。

# JDOM(Java-based Document Object Model)

JDOM 自身不包含解析器，使用 SAX2 解析器来解析和验证输入 XML 文档
包含一些转换器以将 JDOM 表示输出成 SAX2 事件流、DOM 模型、XML 文本文档

优点：API 简单，方便开发
缺点：灵活性较差；性能较差

```xml
<dependency>
            <groupId>jdom</groupId>
            <artifactId>jdom</artifactId>
            <version>1.1</version>
        </dependency>
```

```java
String path = Thread.currentThread().getContextClassLoader().getResource("demo.xml").getPath();

SAXBuilder jdomsaxBuilder = new SAXBuilder(false);
Document doc = jdomsaxBuilder.build(path);
Element rootElement = doc.getRootElement();

List<StudentGridlb> studentGridlbList = new ArrayList<>();
StudentGridlb studentGridlb;
for (Object classGridlb : rootElement.getChildren("classGridlb")) {
    Element classGridlbEle = (Element) classGridlb;

    for (Object studentGrid : classGridlbEle.getChild("studentGrid").getChildren("studentGridlb")) {
        Element studentGridEle = (Element) studentGrid;
        studentGridlb = new StudentGridlb();
        studentGridlb.setStu_id(studentGridEle.getChildTextTrim("stu_id"));
        studentGridlb.setStu_age(Integer.parseInt(studentGridEle.getChildTextTrim("stu_age")));
        studentGridlb.setStu_name(studentGridEle.getChildTextTrim("stu_name"));
        DateFormat format = new SimpleDateFormat("yyyy-MM-dd");
        studentGridlb.setStu_birthday(format.parse(studentGridEle.getChildTextTrim("stu_birthday")));
        studentGridlbList.add(studentGridlb);
    }
}
XMLOutputter outputter = new XMLOutputter();
outputter.output(doc, new FileOutputStream(path));
```

# dom4j(Document Object Model for Java)

dom4j 包含了超出 XML 文档表示的功能
支持 XML Schema
支持大文档或流化文档的基于事件的处理
可以选择按照 DOM 或 SAX 方式解析 XML 文档
优点：

API 丰富易用，较直观，方便开发
支持 XPath
性能较好
缺点：

接口较多，API 较为复杂

```xml
<!--MetaStuff dom4j-->
        <dependency>
            <groupId>dom4j</groupId>
            <artifactId>dom4j</artifactId>
            <version>1.6.1</version>
            <exclusions>
                <exclusion>
                    <groupId>xml-apis</groupId>
                    <artifactId>xml-apis</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
```

```java
// 剔除 xml-apis 的用意 JDK 中已经有对应的类，如不剔除在部署 weblogic 时会出现 Jar 冲突。
//  实例 demo (将 demo.xml studentGridlbb 节点的值解析出来，组成业务实体对象) 。
String path = Thread.currentThread().getContextClassLoader().getResource("demo.xml").getPath();

        SAXReader reader = new SAXReader();
        Document document = reader.read(new File(path));

        List<StudentGridlb> studentGridlbList = new ArrayList<>();
        StudentGridlb studentGridlbVo;
        for (Object classGridlb : document.getRootElement().elements("classGridlb")) {
            Element classGridlbEle = (Element) classGridlb;

            for (Object studentGridlb : classGridlbEle.element("studentGrid").elements("studentGridlb")) {
                Element studentGridlbEle = (Element) studentGridlb;

                studentGridlbVo = new StudentGridlb();
                studentGridlbVo.setStu_id(studentGridlbEle.elementTextTrim("stu_id"));
                studentGridlbVo.setStu_age(Integer.parseInt(studentGridlbEle.elementTextTrim("stu_age")));
                studentGridlbVo.setStu_name(studentGridlbEle.elementTextTrim("stu_name"));
                DateFormat format = new SimpleDateFormat("yyyy-MM-dd");
                studentGridlbVo.setStu_birthday(format.parse(studentGridlbEle.elementTextTrim("stu_birthday")));
                studentGridlbList.add(studentGridlbVo);
            }
        }
```

# XStream

```java
class Book implements Serializable {
    public long id;
    public String name;
    public String author;
    public String isbn;
    public List<String> tags;
    public String pubDate;
}
@Test
public void exampleXStream() {
    XStream xstream = new XStream(new StaxDriver());
    // Object to XML Conversion
    // 根元素别名,默认为类的全限定名
    xstream.alias("book", Book.class);
    String xml = xstream.toXML(new Book(1l, "xxx", "asf", "gaewg", null, "fgaskk"));
    System.out.println(xml); //<?xml version='1.0' encoding='UTF-8'?><book><id>1</id><name>xxx</name><author>asf</author><isbn>gaewg</isbn><pubDate>fgaskk</pubDate></book>
    // 为类型层次结构添加安全权限。不加会报错 ForbiddenClassException
    xstream.allowTypeHierarchy(Book.class);
    Book book = (Book) xstream.fromXML(xml);
    System.out.println(book); //Book{id=1, name='xxx', author='asf', isbn='gaewg', tags=null, pubDate='fgaskk'}
}
```

```java
// 使用注解
xstream.processAnnotations(Book.class);

@XStreamAlias // 起别名,类和字段
@XStreamAliasType // 别名,类
@XStreamAsAttribute //定义一个字段应该序列化为根元素的一个属性。
@XStreamConverter // 声明一个转换器
@XStreamConverters // 多个转换器
@XStreamImplicit // 用于将字段标记为隐式集合或数组的注释。标记在集合字段上,list,会去除list跟标签,
@XStreamInclude //对类自动化处理
@XStreamOmitField //声明要省略的字段
```

# jackson

```xml
        <dependency>
            <groupId>com.fasterxml.jackson.dataformat</groupId>
            <artifactId>jackson-dataformat-xml</artifactId>
            <version>2.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.fasterxml.woodstox</groupId>
            <artifactId>woodstox-core</artifactId>
            <version>6.2.5</version>
        </dependency>
```

```java
InputStream input = Main.class.getResourceAsStream("/book.xml");
JacksonXmlModule module = new JacksonXmlModule();
XmlMapper mapper = new XmlMapper(module);
Book book = mapper.readValue(input, Book.class);
System.out.println(book.id);
System.out.println(book.name);
System.out.println(book.author);
System.out.println(book.isbn);
System.out.println(book.tags);
System.out.println(book.pubDate);
```
