# poi

[仓库地址](https://gitee.com/apache/poi?utm_source=alading&utm_campaign=repo)

```xml
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi</artifactId>
    <version>5.2.2</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-scratchpad</artifactId>
    <version>5.2.2</version>
</dependency>
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
    <version>5.2.2</version>
</dependency>
```

# word

`.docx` : `XWPFDocument`

## 去除作者信息

```Java
XWPFDocument xwpfDocument = new XWPFDocument(wordin);
POIXMLProperties.CoreProperties coreProperties = xwpfDocument.getProperties().getCoreProperties();
coreProperties.setTitle("");
coreProperties.setCreated("");
coreProperties.setCreated(new Date());
coreProperties.setSubjectProperty("");
coreProperties.setCreator("");
coreProperties.setModified("");
```
