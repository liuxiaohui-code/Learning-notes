# maven

Maven 就是是专门为 Java 项目打造的管理和构建工具，它的主要功能有：

1. 提供了一套标准化的项目结构；
2. 提供了一套标准化的构建流程（编译，测试，打包，发布……）；
3. 提供了一套依赖管理机制。

## 项目目录

Maven 提倡使用一个共同的标准目录结构，Maven 使用约定优于配置的原则，大家尽可能的遵守这样的目录结构。如下所示：

```cmd
${basedir}   										#根目录
	|--- pom.xml
	|--- src
			|--- main
					|--- java  						#项目的java源代码
					|--- resources  			#项目的资源
			|--- test
					|--- java  						#项目的测试类
					|--- resources  			#测试用的资源
	|--- target  									#打包输出目录
			|--- classes  						#编译输出目录
			|--- test-classes  				#测试编译输出目录
```

## 安装

官网下载地址：http://maven.apache.org/download.cgi

`download -- Files —— Binary zip archive : xx-bin.zip`

解压，设置环境变量

#系统变量
`MAVEN_HOME=解压缩的那个文件路径`
#path 增加
`%MAVEN_HOME%\bin`

配置本地仓库：

```xml
在解压后的文件当中，新建一个文件夹repositorys，与bin目录同级。
修改配置。打开conf/settings.xml，配置localRepository，值就是所对应的路径：
<!--配置本地仓库-->
<localRepository>F:\Spark\Maven\apache-maven-3.6.3\repositorys</localRepository>

<!--配置镜像仓库-->
  <mirrors>
    <mirror>
      <id>mirrorId</id>
      <mirrorOf>repositoryId</mirrorOf>
      <name>aliyun maven</name>
	  <url>http://maven.aliyun.com/nexus/content/groups/public/</url>
    </mirror>
  </mirrors>

```

`mvn help:effective-settings` 检查配置文件格式问题

打开 cmd，先切盘到安装[maven]路径的 bin 目录下，输入：`mvn help:system` 然后系统就会下载需要的东西到 repositorys 这个文件夹中

完成之后，在 cmd 当中输入`mvn- v`查看版本，有版本输出表示全局环境变量配置成功

# pom 配置文件

## 内置属性

${basedir} 表示项目根目录，即包含pom.xml文件的目录
${version} 等同于 ${project.version} 或者 ${pom.version} 表示项目版本

所有 pom 中的元素都可以用 project.
例如${project.artifactId}对应了`<project>``<artifactId>`元素的值
常用的POM属性包括
${project.build.sourceDirectory}:项目的主源码目录，默认为 src/main/java/.
${project.build.testSourceDirectory}:项目的测试源码目录，默认为/src/test/java/.
${project.build.directory}:项目构建输出目录，默认为 target/.
${project.build.outputDirectory}:项目主代码编译输出目录，默认为target/classes/.
${project.build.testOutputDirectory}:项目测试代码编译输出目录，默认为 target/testclasses/.
${project.groupId}:项目的groupId.
${project.artifactId}:项目的 artifactId.
${project.version}:项目的version,等同于${version}
${project.build.finalName}:项目打包输出文件的名称，默认为${project.artifactId}${project.version}.

在 pom 中`<properties>`元素下自定义的 Maven 属性
所有用的的 settings.xml 中的设定都可以通过 settings. 前缀进行引用

与 POM 属性同理。如${settings.localRepository}指向用户本地仓库的地址
所有Java系统属性都可以使用Maven属性引用，例如${user.home}指向了用户目录。
可以通过命令行 mvn help:system 查看所有的 Java 系统属性

所有环境变量都可以使用以 env.开头的 Maven 属性引用。
例如${env.JAVA_HOME}指代了 JAVA_HOME 环境变量的值。
也可以通过命令行 mvn help:system 查看所有环境变量。

## 项目基本信息

在创建 POM 之前，首先要确定工程组（groupId），及其名称（artifactId）和版本，在仓库中这些属性是项目的唯一标识。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <!-- groupId 必填,项目组 ID，定义当前 Maven 项目隶属的组织或公司，通常是唯一的。它的取值一般是项目所属公司或组织的网址或 URL 的反写  -->
    <groupId>net.biancheng.www</groupId>
    <!-- artifactId 必填 项目 ID，通常是项目的名称。 -->
    <artifactId>maven</artifactId>
    <!-- version 必填 项目版本 -->
    <version>0.0.1-SNAPSHOT</version>
    <!-- 项目的打包方式，默认值为 jar -->
    <packaging>jar</packaging>
</project>
```

`Super POM` 所有的 POM 均继承自一个父 POM，这个父 POM 被称为 _Super POM_,它包含了一些可以被继承的默认设置,`mvn help:effective-pom` 可以查看默认配置内容.

## 依赖

Maven 坐标是依赖的前提，所有 Maven 项目必须明确定义自己的坐标，只有这样，它们才可能成为其他项目的依赖。当一个项目的构件成为其他项目的依赖时，该项目的坐标才能体现出它的价值。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
...
    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>servlet-api</artifactId>
            <!-- 注：只有以SNAPSHOT-开头的版本号会被Maven视为开发版本，开发版本每次都会重复下载，这种SNAPSHOT版本只能用于内部私有的Maven repo，公开发布的版本不允许出现SNAPSHOT。 -->
            <version>2.5</version>
            <!-- 依赖的范围 -->
            <scope>compile</scope>
            <!-- 依赖范围为  system 时,加载本地jar包-->
            <systemPath></systemPath>
            <!-- 标记依赖是否可选 -->
            <optional></optional>
            <!-- 用来排除传递性依赖 -->
            <exclusions>

            </exclusions>
        </dependency>
    </dependencies>
</project>
```

**scope**：依赖的范围：
`compile`编译依赖范围，scope 元素的缺省值。使用此依赖范围的 Maven 依赖，对于三种 classpath 均有效，即该 Maven 依赖在上述三种 classpath 均会被引入。例如，log4j 在编译、测试、运行过程都是必须的。
`test`测试依赖范围。使用此依赖范围的 Maven 依赖，只对测试 classpath 有效。例如，Junit 依赖只有在测试阶段才需要。
`provided`已提供依赖范围。使用此依赖范围的 Maven 依赖，只对编译 classpath 和测试 classpath 有效。例如，servlet-api 依赖对于编译、测试阶段而言是需要的，但是运行阶段，由于外部容器已经提供，故不需要 Maven 重复引入该依赖。
`runtime`运行时依赖范围。使用此依赖范围的 Maven 依赖，只对测试 classpath、运行 classpath 有效。例如，JDBC 驱动实现依赖，其在编译时只需 JDK 提供的 JDBC 接口即可，只有测试、运行阶段才需要实现了 JDBC 接口的驱动。
`system`系统依赖范围，其效果与 provided 的依赖范围一致。其用于添加非 Maven 仓库的本地依赖，通过依赖元素 dependency 中的 systemPath 元素指定本地依赖的路径。鉴于使用其会导致项目的可移植性降低，一般不推荐使用。
`import`导入依赖范围，该依赖范围只能与 dependencyManagement 元素配合使用，其功能是将目标 pom.xml 文件中 dependencyManagement 的配置导入合并到当前 pom.xml 的 dependencyManagement 中。

**获取依赖** https://mvnrepository.com/

## 仓库

### 本地仓库

Maven 本地仓库实际上就是本地计算机上的一个目录（文件夹），它会在第一次执行 Maven 命令时被创建。

Maven 本地仓库可以储存本地所有项目所需的构件。当 Maven 项目第一次进行构建时，会自动从远程仓库搜索依赖项，并将其下载到本地仓库中。当项目再进行构建时，会直接从本地仓库搜索依赖项并引用，而不会再次向远程仓库获取。

Maven 本地仓库默认地址为 `C:\%USER_HOME%\.m2\repository` ，但出于某些原因（例如 C 盘空间不够），我们通常会重新自定义本地仓库的位置。这时需要修改 `%MAVEN_HOME%\conf` 目录下的 `settings.xml` 文件，通过 `localRepository` 元素定义另一个本地仓库地址，

```xml
<settings xmlns="http://maven.apache.org/SETTINGS/1.0.0"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://maven.apache.org/SETTINGS/1.0.0
   http://maven.apache.org/xsd/settings-1.0.0.xsd">
    <localRepository>D:/myRepository/repository</localRepository>
</settings>
```

### 中央仓库

中央仓库是由 Maven 社区提供的一种特殊的远程仓库，它包含了绝大多数流行的开源构件。在默认情况下，当本地仓库没有 Maven 所需的构件时，会首先尝试从中央仓库下载。

中央仓库具有如下特点：

- 这个仓库由 Maven 社区管理
- 不需要配置
- 需要通过网络才能访问

我们可以通过 Maven 社区提供的 URL：http://search.maven.org/#browse，浏览其中的构件。中央仓库包含了绝大多数流行的开源 Java 构件及其源码、作者信息和许可证信息等。一般来说，Maven 项目所依赖的构件都可以从中央仓库下载到。

### 远程仓库

如果 Maven 在本地仓库和中央仓库中都找不到依赖的库文件，它就会停止构建过程并输出错误信息到控制台。为避免这种情况的发生，Maven 还提供了远程仓库的概念，它是一种由开发人员自己定制的仓库，其中包含了供其他项目使用的代码库或者构件。

```xml
<project>
	......
	<repositories>
    <repository>
        <id>bianchengbang.lib1</id>
        <url>http://download.bianchengbang.org/maven2/lib1</url>
    </repository>
    <repository>
        <id>bianchengbang.lib2</id>
        <url>http://download.bianchengbang.org/maven2/lib2</url>
    </repository>
  </repositories>
</project>
```

### 顺序

当通过 Maven 构建项目时，Maven 按照如下顺序查找依赖的构件。

1. 从本地仓库查找构件，如果没有找到，跳到第 2 步，否则继续执行其他处理。
2. 从中央仓库查找构件，如果没有找到，并且已经设置其他远程仓库，然后移动到第 4 步；如果找到，那么将构件下载到本地仓库中使用。
3. 如果没有设置其他远程仓库，Maven 则会停止处理并抛出错误。
4. 在远程仓库查找构件，如果找到，则会下载到本地仓库并使用，否则 Maven 停止处理并抛出错误。

## 插件

Maven 实际上是一个依赖插件执行的框架，它执行的每个任务实际上都由插件完成的。Maven 的核心发布包中并不包含任何 Maven 插件，它们以独立构件的形式存在， 只有在 Maven 需要使用某个插件时，才会去仓库中下载。
插件类型 描述
**Build plugins** 在项目构建过程中执行，在 pom.xml 中的 **build** 元素中配置
**Reporting plugins** 在网站生成过程中执行，在 pom.xml 中的 **reporting** 元素中配置
对于 Maven 插件而言，为了提高代码的复用性，通常一个 Maven 插件能够实现多个功能，每一个功能都是一个插件目标，即 Maven 插件是插件目标的集合。我们可以把插件理解为一个类，而插件目标是类中的方法，调用插件目标就能实现对应的功能。

插件目标的通用写法如下。
`[插件名]:[插件目标名]`
使用 Maven 命令执行插件的目标，语法如下。
`mvn [插件名]:[目标名]`

为了完成某个具体的构建任务，Maven 生命周期的阶段需要和 Maven 插件的目标相互绑定。例如，代码编译任务对应了 default 生命周期的 compile 阶段，而 maven-compiler-plugin 插件的 compile 目标能够完成这个任务，因此将它们进行绑定就能达到代码编译的目的。

**内置绑定**
Maven 默认为一些核心的生命周期阶段绑定了插件目标，当用户调用这些阶段时，对应的插件目标就会自动执行相应的任务。

clean

1. pre-clean
2. clean maven-clean-plugin:clean 清理 Maven 的输出目录
3. post-clean

site

1. pre-site
2. site maven-site-plugin:site 生成项目站点
3. post-site
4. site-deploy maven-site-plugin:deploy 部署项目站点

default

1. process-resources maven-resources-plugin:resources 复制资源文件到输出目录
2. compile maven-compiler-plugin:compile 编译代码到输出目录
3. process-test-resources maven-resources-plugin:testResources 复制测试资源文件到测试输出目录
4. test-compile maven-compiler-plugin:testCompile 编译测试代码到测试输出目录
5. test maven-surefire-plugin:test 执行测试用例
6. package maven-jar-plugin:jar/maven-jar-plugin:war 创建项目 jar/war 包
7. install maven-install-plugin:install 将项目输出的包文件安装到本地仓库
8. deploy maven-deploy-plugin:deploy 将项目输出的包文件部署到到远程仓库

### 自定义绑定

除了内置绑定之外，用户也可以自己选择将某个插件目标绑定到 Maven 生命周期的某个阶段上，这种绑定方式就是自定义绑定。自定义绑定能够让 Maven 在构建过程中执行更多更丰富的任务。
例如，我们想要在 clean 生命周期的 clean 阶段中显示自定义文本信息，则只需要在项目的 POM 中 ，通过 build 元素的子元素 plugins，将 maven-antrun-plugin:run 目标绑定到 clean 阶段上，并使用该插件输出自定义文本信息即可。

```xml
<project>
    <build>
        <plugins>
            <!-- 绑定插件 maven-antrun-plugin -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-antrun-plugin</artifactId>
                <version>1.8</version>
                <!-- 执行 -->
                <executions>
                    <execution>
                        <!--自定义 id -->
                        <id>www.biancheng.net clean</id>
                        <!--插件目标绑定的构建阶段 -->
                        <phase>clean</phase>
                        <!--插件目标 -->
                        <goals>
                            <goal>run</goal>
                        </goals>
                        <!--配置 -->
                        <configuration>
                            <!-- 执行的任务 -->
                            <tasks>
                                <!--自定义文本信息 -->
                                <echo>清理阶段，编程帮 欢迎您的到来，网址：www.biancheng.net</echo>
                            </tasks>
                            <transformers>
                                <!-- 指定Java程序的入口,Maven自带的标准插件例如compiler是无需声明的，只有引入其它的插件才需要声明 -->
                                <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                                    <mainClass>com.itranswarp.learnjava.Main</mainClass>
                                </transformer>
                            </transformers>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
    </build>
</project>
```

下面列举了一些常用的插件：

`maven-shade-plugin`：打包所有依赖包并生成可执行 jar；
`cobertura-maven-plugin`：生成单元测试覆盖率报告；
`findbugs-maven-plugin`：对 Java 源码进行静态分析以找出潜在问题。

### 打包时注入本地依赖

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-dependency-plugin</artifactId>
    <version>3.0.1</version>
    <executions>
        <execution>
            <id>copy-dependencies</id>
            <phase>package</phase>
            <goals>
                <goal>copy-dependencies</goal>
            </goals>
            <configuration>
                <outputDirectory>${project.build.directory}/lib</outputDirectory>
                <overWriteReleases>false</overWriteReleases>
                <overWriteSnapshots>false</overWriteSnapshots>
                <overWriteIfNewer>true</overWriteIfNewer>
            </configuration>
        </execution>
    </executions>
</plugin>
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-assembly-plugin</artifactId>
    <version>3.0.0</version>
    <configuration>
        <archive>
            <manifest>
                <mainClass></mainClass>
            </manifest>
        </archive>
        <descriptorRefs>
            <descriptorRef>jar-with-dependencies</descriptorRef>
        </descriptorRefs>
    </configuration>
    <executions>
        <execution>
            <id>make-assembly</id> <!-- this is used for inheritance merges -->
            <phase>package</phase> <!-- 指定在打包节点执行jar包合并操作 -->
            <goals>
                <goal>single</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

### 打包源代码以获取注释

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-source-plugin</artifactId>
    <version>3.2.1</version>
    <executions>
        <execution>
            <id>attach-sources</id>
            <goals>
                <goal>jar</goal>
            </goals>
        </execution>
    </executions>
</plugin>

```

## 依赖传递

Maven 的依赖传递机制是指：不管 Maven 项目存在多少间接依赖，POM 中都只需要定义其直接依赖，不必定义任何间接依赖，Maven 会动读取当前项目各个直接依赖的 POM，将那些必要的间接依赖以传递性依赖的形式引入到当前项目中。
通过这种依赖传递关系，可以使依赖关系树迅速增长到一个很大的量级，很有可能会出现依赖重复，依赖冲突等情况，Maven 针对这些情况提供了如下功能进行处理。

### 依赖范围（Dependency scope）

依赖范围 描述
`compile` 编译依赖范围，scope 元素的缺省值。使用此依赖范围的 Maven 依赖，对于三种 classpath 均有效，即该 Maven 依赖在上述三种 classpath 均会被引入。例如，log4j 在编译、测试、运行过程都是必须的。
`test` 测试依赖范围。使用此依赖范围的 Maven 依赖，只对测试 classpath 有效。例如，Junit 依赖只有在测试阶段才需要。
`provided` 已提供依赖范围。使用此依赖范围的 Maven 依赖，只对编译 classpath 和测试 classpath 有效。例如，servlet-api 依赖对于编译、测试阶段而言是需要的，但是运行阶段，由于外部容器已经提供，故不需要 Maven 重复引入该依赖。
`runtime` 运行时依赖范围。使用此依赖范围的 Maven 依赖，只对测试 classpath、运行 classpath 有效。例如，JDBC 驱动实现依赖，其在编译时只需 JDK 提供的 JDBC 接口即可，只有测试、运行阶段才需要实现了 JDBC 接口的驱动。
`system` 系统依赖范围，其效果与 provided 的依赖范围一致。其用于添加非 Maven 仓库的本地依赖，通过依赖元素 dependency 中的 systemPath 元素指定本地依赖的路径。鉴于使用其会导致项目的可移植性降低，一般不推荐使用。
`import` 导入依赖范围，该依赖范围只能与 dependencyManagement 元素配合使用，其功能是将目标 pom.xml 文件中 dependencyManagement 的配置导入合并到当前 pom.xml 的 dependencyManagement 中。

当第二直接依赖的范围是 compile 时，传递性依赖的范围与第一直接依赖的范围一致；
当第二直接依赖的范围是 test 时，传递性依赖不会被传递；
当第二直接依赖的范围是 provided 时，只传递第一直接依赖的范围也为 provided 的依赖，且传递性依赖的范围也为 provided；
当第二直接依赖的范围是 runtime 时，传递性依赖的范围与第一直接依赖的范围一致，但 compile 例外，此时传递性依赖的范围为 runtime。

### 依赖调解（Dependency mediation）

Maven 的依赖传递机制可以简化依赖的声明，用户只需要关心项目的直接依赖，而不必关心这些直接依赖会引入哪些间接依赖。但当一个间接依赖存在多条引入路径时，为了避免出现依赖重复的问题，Maven 通过依赖调节来确定间接依赖的引入路径。

依赖调节遵循以下两条原则：

1. 引入路径短者优先
2. 先声明者优先

以上两条原则，优先使用第一条原则解决，第一条原则无法解决，再使用第二条原则解决。

### 可选依赖（Optional dependencies）

与排除依赖的应用场景相同，也是 A 希望排除间接依赖 X，除了在 A 中设置排除依赖外，我们还可以在 B 中将 X 设置为可选依赖。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>net.biancheng.www</groupId>
    <artifactId>B</artifactId>
    <packaging>jar</packaging>
    <version>1.0-SNAPSHOT</version>
    <dependencies>
        <dependency>
            <groupId>net.biancheng.www</groupId>
            <artifactId>X</artifactId>
            <version>1.0-SNAPSHOT</version>
            <!--设置可选依赖  -->
            <optional>true</optional>
        </dependency>
    </dependencies>
</project>
```

关于 optional 元素及可选依赖说明如下：

可选依赖用来控制当前依赖是否向下传递成为间接依赖；
optional 默认值为 false，表示可以向下传递称为间接依赖；
若 optional 元素取值为 true，则表示当前依赖不能向下传递成为间接依赖。

### 排除依赖（Excluded dependencies）

假设存在这样的依赖关系，A 依赖于 B，B 依赖于 X，B 又依赖于 Y。B 实现了两个特性，其中一个特性依赖于 X，另一个特性依赖于 Y，且两个特性是互斥的关系，用户无法同时使用两个特性，所以 A 需要排除 X，此时就可以在 A 中将间接依赖 X 排除。

排除依赖是通过在 A 中使用 `exclusions` 元素实现的，该元素下可以包含若干个 exclusion 子元素，用于排除若干个间接依赖，示例代码如下。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>net.biancheng.www</groupId>
    <artifactId>A</artifactId>
    <version>1.0-SNAPSHOT</version>
    <dependencies>
        <dependency>
            <groupId>net.biancheng.www</groupId>
            <artifactId>B</artifactId>
            <version>1.0-SNAPSHOT</version>
            <exclusions>
                <!-- 设置排除 -->
                <!-- 排除依赖必须基于直接依赖中的间接依赖设置为可以依赖为 false -->
                <!-- 设置当前依赖中是否使用间接依赖 -->
                <exclusion>
                    <!--设置具体排除-->
                    <groupId>net.biancheng.www</groupId>
                    <artifactId>X</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
    </dependencies>
</project>
```

关于 exclusions 元素及排除依赖说明如下：
排除依赖是控制当前项目是否使用其直接依赖传递下来的接间依赖；
exclusions 元素下可以包含若干个 exclusion 子元素，用于排除若干个间接依赖；
exclusion 元素用来设置具体排除的间接依赖，该元素包含两个子元素：groupId 和 artifactId，用来确定需要排除的间接依赖的坐标信息；
exclusion 元素中只需要设置 groupId 和 artifactId 就可以确定需要排除的依赖，无需指定版本 version。

> 排除依赖和可选依赖都能在项目中将间接依赖排除在外，但两者实现机制却完全不一样。
> 排除依赖是控制当前项目是否使用其直接依赖传递下来的接间依赖；
> 可选依赖是控制当前项目的依赖是否向下传递；
> 可选依赖的优先级高于排除依赖；
> 若对于同一个间接依赖同时使用排除依赖和可选依赖进行设置，那么可选依赖的取值必须为 false，否则排除依赖无法生效。

### 依赖管理（Dependency management）

Maven 可以通过 dependencyManagement 元素对依赖进行管理，它具有以下 2 大特性：
在该元素下声明的依赖不会实际引入到模块中，只有在 dependencies 元素下同样声明了该依赖，才会引入到模块中。
该元素能够约束 dependencies 下依赖的使用，即 dependencies 声明的依赖若未指定版本，则使用 dependencyManagement 中指定的版本，否则将覆盖 dependencyManagement 中的版本。

```xml
<!--dependencyManagement 标签用于控制子模块的依赖版本等信息 -->
    <!-- 该标签只用来控制版本，不能将依赖引入 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>1.2.17</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>4.9</version>
                <!-- <scope>test</scope> -->
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>5.1.18</version>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>c3p0</groupId>
                <artifactId>c3p0</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>0.9.1</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

### 继承

Maven 在设计时，借鉴了 Java 面向对象中的继承思想，提出了 POM 继承思想。当一个项目包含多个模块时，可以在该项目中再创建一个父模块，并在其 POM 中声明依赖，其他模块的 POM 可通过继承父模块的 POM 来获得对相关依赖的声明。对于父模块而言，其目的是为了消除子模块 POM 中的重复配置，其中不包含有任何实际代码，因此**父模块 POM 的打包类型（packaging）必须是 pom**。

```
single-project
├── parent
│   └── pom.xml  # 父模块,将子模块公共依赖抽出来
├── module-a
│   ├── pom.xml
│   └── src
├── module-b
│   ├── pom.xml
│   └── src
└── module-c
    ├── pom.xml
    └── src
```

Maven 支持模块化管理，可以把一个大项目拆成几个模块可以通过继承在 parent 的 pom.xml 统一定义重复配置可以通过`<modules>`编译多个模块

### 聚合

使用 Maven 聚合功能对项目进行构建时，需要在该项目中额外创建一个的聚合模块，然后通过这个模块构建整个项目的所有模块。聚合模块仅仅是帮助聚合其他模块的工具，其本身并无任何实质内容，因此聚合模块中只有一个 POM 文件

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>net.biancheng.www</groupId>
    <artifactId>Root</artifactId>
    <version>1.0</version>
    <!--定义的父类pom.xml 打包类型使pom -->
    <packaging>pom</packaging>
    <properties>
        <!-- 定义一些属性 -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <log4j.version>1.2.17</log4j.version>
        <junit.version>4.9</junit.version>
        <system.version>1.0</system.version>
        <mysql.connector.version>5.1.18</mysql.connector.version>
        <c3p0.version>0.9.1</c3p0.version>
    </properties>
    <!--dependencyManagement 标签用于控制子模块的依赖版本等信息 -->
    <!-- 该标签只用来控制版本，不能将依赖引入 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>${log4j.version}</version>
            </dependency>
            <dependency>
                <groupId>junit</groupId>
                <artifactId>junit</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>${junit.version}</version>
                <!-- <scope>test</scope> -->
            </dependency>
            <dependency>
                <groupId>mysql</groupId>
                <artifactId>mysql-connector-java</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>${mysql.connector.version}</version>
                <scope>runtime</scope>
            </dependency>
            <dependency>
                <groupId>c3p0</groupId>
                <artifactId>c3p0</artifactId>
                <!--引用的properties标签中定义的属性 -->
                <version>${c3p0.version}</version>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <!--添加需要聚合的模块-->
    <modules>
        <module>../App-Core-lib</module>
        <module>../App-Data-lib</module>
        <module>../App-UI-WAR</module>
    </modules>
</project>
```

# mvn

在包含 pom.xml 目录下执行

## 创建 maven

`​mvn archetype:generate -DgroupId=net.biancheng.www -DartifactId=helloMaven -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false`

-DgroupId: 项目组 ID，通常为组织名或公司网址的反写。
-DartifactId: 项目名。
-DarchetypeArtifactId: 指定 ArchetypeId，maven-archetype-quickstart 用于快速创建一个简单的 Maven 项目。
-DinteractiveMode: 是否使用交互模式。

> 创建 web 应用
> `mvn archetype:generate -DgroupId=net.biancheng.www -DartifactId=mavenWeb -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false`
> 命令执行完成后，Maven 为我们创建了一个名为 mavenWeb 的 JavaWeb 应用
> `mvn clean package`
> 打包完成后，进入 D:\MavenWeb\mavenWeb\target 目录，可以看到该应用打包生成的 war 文件
> 将 mavenWeb 的 war 文件复制到 Tomcat 服务器的 webapp 目录中，然后重新启动 Tomcat 服务器。
> Tomcat 启动完成后，在浏览器地址栏输入“http://localhost:8080/mavenWeb/index.jsp”

## 生命周期

Maven 从大量项目和构建工具中学习和反思，最后总结了一套高度完美的，易扩展的生命周期。这个生命周期将项目的清理、初始化、编译、测试、打包、集成测试、验证、部署和站点生成等几乎所有构建过程进行了抽象和统一。
Maven 拥有三套标准的生命周期：

- clean：用于清理项目
- default：用于构建项目
- site：用于建立项目站点
  每套生命周期包含一系列的构建阶段（phase），这些阶段是有顺序的，且后面的阶段依赖于前面的阶段。用户与 Maven 最直接的交互方式就是调用这些生命周期阶段。以 clean 生命周期为例，它包含 pre-clean、clean 以及 post-clean 三个阶段，当用户调用 pre-clean 阶段时，则只有 pre-clean 阶段执行；当用户调用 clean 阶段时，pre-clean 和 clean 阶段都会执行。当用户调用 post-clean 阶段时，则 pre-clean、clean 以及 post-clean 三个阶段都会执行。

与构建阶段的前后依赖关系不同，三套生命周期本身时相互独立的，用户可以只调用 clean 生命周期的某个阶段，也可以只调用 default 生命周期的某个阶段，而不会对其他生命周期造成任何影响。

使用 Maven 构建项目就是执行 lifecycle，执行到指定的 phase 为止。每个 phase 会执行自己默认的一个或多个 goal。goal 是最小任务单元。

### clean

clean 生命周期包括以下 3 个阶段。

1. pre-clean（清理前）
2. clean（清理）
3. post-clean（清理后）

### default

default 生命周期定义了项目真正构建时所需要的所有步骤，它是所有生命周期中最核心，最重要的部分。

1. validate 验证项目是否正确以及所有必要信息是否可用。
2. initialize 初始化构建状态。
3. generate-sources 生成编译阶段需要的所有源码文件。
4. process-sources 处理源码文件，例如过滤某些值。
5. generate-resources 生成项目打包阶段需要的资源文件。
6. process-resources 处理资源文件，并复制到输出目录，为打包阶段做准备。
7. compile 编译源代码，并移动到输出目录。
8. process-classes 处理编译生成的字节码文件
9. generate-test-sources 生成编译阶段需要的测试源代码。
10. process-test-sources 处理测试资源，并复制到测试输出目录。
11. test-compile 编译测试源代码并移动到测试输出目录中。
12. test 使用适当的单元测试框架（例如 JUnit）运行测试。
13. prepare-package 在真正打包之前，执行一些必要的操作。
14. package 获取编译后的代码，并按照可发布的格式进行打包，例如 JAR、WAR 或者 EAR 文件。
15. pre-integration-test 在集成测试执行之前，执行所需的操作，例如设置环境变量。
16. integration-test 处理和部署所需的包到集成测试能够运行的环境中。
17. post-integration-test 在集成测试被执行后执行必要的操作，例如清理环境。
18. verify 对集成测试的结果进行检查，以保证质量达标。
19. install 安装打包的项目到本地仓库，以供其他项目使用。
20. deploy 拷贝最终的包文件到远程仓库中，以共享给其他开发人员和项目。

### site

sit 生命周期的目的是建立和部署项目站点，Maven 能够根据 POM 包含的信息，自动生成一个友好的站点，该站点包含一些与该项目相关的文档。

site 生命周期包含以下 4 个阶段：

1. pre-site
2. site
3. post-site
4. site-deploy

- clean 负责清理 target 目录
- validate 初始化
- compile 对项目进行编译
  - target/classes：源代码编译后存放在该目录中。再此目录下运行`java <groupId>` 可以运行程序
- test 测试
- package 负责将项目构建并打包输出为 jar 文件
  - target/test-classes：测试源代码编译后并存放在该目录中。
  - target/surefire-reports：Maven 运行测试用例生成的测试报告存放在该目录中。
  - target/helloMaven-1.0-SNAPSHOT.jar：Maven 对项目进行打包生成的 jar 文件。`java -Dspring.config.location=/baas/xchain/application.yml -jar xx.jar`
- verify
- install
- site
- deploy

### goal

我们以 compile 这个 phase 为例，如果执行 `mvn compile` Maven 将执行 compile 这个 phase，这个 phase 会调用 compiler 插件执行关联的 `compiler:compile` 这个 goal。

## 其他命令

`-Dmaven.test.skip=true` 不编译也不执行 test
`-DskipTests` 编译但不执行 test

`mvn dependency:tree` 当前依赖树查看
`mvn spring-boot:run` 运行 spring boot 程序

## Archetype(原型/模板)

Archetype 是 Maven 项目的模板工具包，它定义了 Maven 项目的基本架构。Archetype 为开发人员提供了数千种创建 Maven 项目的模板，Maven 通过这些模板可以帮助用户快速的生成项目的目录结构以及 POM 文件。

Maven Archetype 由下面 5 个模块组成：
maven-archetype-plugin：Archetype 插件。
archetype-packaging：用于描述 Archetype 的生命周期与构建项目软件包。
archetype-models：用于描述类与引用。
archetype-common：核心类。
archetype-testing：用于测试 Maven Archetype 的内部组件。

我们知道 Maven 的所有功能都是通过插件实现的，Archetype 也不例外，它是由一个名为 maven-archetype-plugin 的插件实现的，该插件提供了 ArcheType 的所有功能。
执行以下命令可以帮助用户快速的创建 Maven 项目.
`mvn archetype:generate`

## SNAPSHOT(快照)

SNAPSHOT（快照）是一种特殊的版本，它表示当前开发进度的副本。与常规版本不同，快照版本的构件在发布时，Maven 会自动为它打上一个时间戳，有了这个时间戳后，当依赖该构件的项目进行构建时，Maven 就能从仓库中找到最新的 SNAPSHOT 版本文件。
定义一个组件或模块为快照版本，只需要在其 pom.xml 中版本号（version 元素的值）后加上 `-SNAPSHOT` 即可

# Maven Profile

一个项目通常都会有多个不同的运行环境，例如开发环境，测试环境、生产环境等。而不同环境的构建过程很可能是不同的，例如数据源配置、插件、以及依赖的版本等。每次将项目部署到不同的环境时，都需要修改相应的配置，这样重复的工作，不仅浪费劳动力，还容易出错。为了解决这一问题，Maven 引入了 Profile 的概念，通过它可以为不同的环境定制不同的构建过程。

Profile 可以分为 3 个类型，它们的作用范围也各不相同。

类型 位置 有效范围
Per Project Maven 项目的 pom.xml 中 只对当前项目有效
Per User 用户主目录（％USER_HOME％）/.m2/settings.xml 中 对本机上该用户所有 Maven 项目有效
Global Maven 安装目录（%MAVEN_HOME%）/conf/settings.xml 中 对本机上所有 Maven 项目有效

## 声明 Profile

Maven 通过 profiles 元素来声明一组 Profile 配置，该元素下可以包含多个 profile 子元素，每个 profile 元素表示一个 Profile 配置。每个 profile 元素中通常都要包含一个 id 子元素，该元素是调用当前 Profile 的标识。

定义 Profile 的一般形式如下：

```xml
<profiles>
    <profile>
        <id>profile id</id>
        ....
    </profile>
    <profile>
        <id>profile id</id>
        ....
    </profile>
</profiles>
```

除此之外，Profile 中还可以声明一些其他的 POM 元素，但不同位置的 Profile 所能声明的 POM 元素也是不同的。

1. 在 pom.xml 中声明的 Profile，由于其能够随着 pom.xml 一起存在，它被提交到代码仓库中，被 Maven 安装到本地仓库或远程仓库中，所以它能够修改或增加很多 POM 元素，其中常用的元素如下表。
2. 在 setting.xml 中声明的 Profile 是无法保证能够随着 pom.xml 一起被分发的，因此 Maven 不允许用户在该类型的 Profile 修改或增加依赖或插件等配置信息，它只能声明以下范围较为宽泛的元素。
   repositories：仓库配置。
   pluginRepositories：插件仓库配置。
   properties：键值对，该键值对可以在 pom.xml 中使用。

## 激活 Profile

Profile 能够在项目构建时，修改 POM 中配置或者添加一些额外的配置元素。用户可以通过多种方式激活 Profile，以实现不同环境使用不同的配置，执行不同的构建过程。

Profile 可以通过以下 6 种方式激活：

- 命令行激活
- settings.xml 文件显示激活
- 系统属性激活
- 操作系统环境激活
- 文件存在与否激活
- 默认激活

## 示例

1. 创建一个名为 maven 的项目，并在其 src/main/resources 目录下有 3 个环境 properties 配置文件。
   env.properties：默认配置文件
   env.test.properties：测试环境配置文件
   env.prod.properties：生产环境配置文件
2. 在 pom.xml 中定义三个不同的 Profile，将 maven-antrun-plugin:run 目标绑定到 default 生命周期的 test 阶段上，以实现在不同的 Profile 中进行不同的操作。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>net.biancheng.www</groupId>
    <artifactId>maven</artifactId>
    <version>0.0.1-SNAPSHOT</version>

    <dependencies>
        <!-- junit依赖 -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.9</version>
            <scope>compile</scope>
        </dependency>
        <!-- log4j依赖 -->
        <dependency>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
            <version>1.2.17</version>
        </dependency>
    </dependencies>

    <profiles>
        <!--test 环境配置  -->
        <profile>
            <id>test</id>
            <activation>
                <property>
                    <name>env</name>
                    <value>test</value>
                </property>
            </activation>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-antrun-plugin</artifactId>
                        <version>1.3</version>
                        <executions>
                            <execution>
                                <phase>test</phase>
                                <goals>
                                    <goal>run</goal>
                                </goals>
                                <configuration>
                                    <tasks>
                                        <!--输出  -->
                                        <echo>使用 env.test.properties,将其配置信息复制到 D:\eclipse workSpace
                                            3\maven\target\classes\user.properties 中
                                        </echo>
                                        <!-- 在 target\calsses 目录下生成user.properties -->
                                        <!--  env.test.properties 的内容复制到user.properties中-->
                                        <copy file="src/main/resources/env.test.properties"
                                              tofile="${project.build.outputDirectory}/user.properties"/>
                                    </tasks>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
        <!-- 默认环境配置 -->
        <profile>
            <id>normal</id>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-antrun-plugin</artifactId>
                        <version>1.3</version>
                        <executions>
                            <execution>
                                <phase>test</phase>
                                <goals>
                                    <goal>run</goal>
                                </goals>
                                <configuration>
                                    <tasks>
                                        <echo>使用 env.properties，将其配置信息复制到 D:\eclipse workSpace
                                            3\maven\target\classes\user.properties 中
                                        </echo>
                                        <!-- 在target\calsses 目录下生成user.properties -->
                                        <!--  env.properties 的内容复制到user.properties中-->
                                        <copy file="src/main/resources/env.properties"
                                              tofile="${project.build.outputDirectory}/user.properties"/>
                                    </tasks>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
        <!--生产环境配置  -->
        <profile>
            <id>prod</id>
            <build>
                <plugins>
                    <plugin>
                        <groupId>org.apache.maven.plugins</groupId>
                        <artifactId>maven-antrun-plugin</artifactId>
                        <version>1.3</version>
                        <executions>
                            <execution>
                                <phase>test</phase>
                                <goals>
                                    <goal>run</goal>
                                </goals>
                                <configuration>
                                    <tasks>
                                        <echo>使用 env.prod.properties，将其配置信息复制到 D:\eclipse workSpace
                                            3\maven\target\classes\user.properties 中
                                        </echo>
                                        <!-- 在target\calsses 目录下生成user.properties -->
                                        <!--  env.prod.properties 的内容复制到user.properties中-->
                                        <copy file="src/main/resources/env.prod.properties"
                                              tofile="${project.build.outputDirectory}/user.properties"/>
                                    </tasks>
                                </configuration>
                            </execution>
                        </executions>
                    </plugin>
                </plugins>
            </build>
        </profile>
    </profiles>
</project>
```

3. 我们可以在命令行中使用 mvn 命令行参数 -P 加上 Profile 的 id 来激活 Profile，多个 id 之间使用逗号隔开。例如，激活 test1 和 test2 两个 Profile, 命令如下：
   `mvn clean install -Ptest1,test2`

打开命令行窗口，跳转到 pom.xml 所在的目录，执行以下 mvn 命令，激活 id 为 test 的 Profile。
`mvn clean test -Ptest ` 4. 在本地仓库的 settings.xml 文件中添加如下配置，激活指定的 Profile。

```xml
<activeProfiles>
    <activeProfile>test</activeProfile>
</activeProfiles>
```

打开命令行窗口，跳转到 pom.xml 所在的目录下，执行以下 mvn 命令。
`mvn clean test`

5. 用户可以配置当某个系统属性存在时，激活指定的 Profile。例如，我们在 id 为 prod 的 profile 元素中添加以下配置，表示当系统属性 user 存在，且值等于 prod 时，自动激活该 Profile。

```xml
<activation>
      <property>
        <name>user</name>
        <value>prod</value>
    </property>
</activation>
```

打开命令行窗口，跳转到 pom.xml 所在的目录。执行命令，其中参数 -D 选项用来指定系统临时属性。
mvn clean test -Duser=prod

6. Maven 还可以根据操作系统环境自动激活指定的 Profile。例如，将以下配置（本地计算机操作系统环境信息）添加到 pom.xml 文件中的 id 为 normal 的 Profile 中。

```xml
<activation>
    <os>
        <name>Windows 10</name>
        <family>Windows</family>
        <arch>amd64</arch>
        <version>10.0</version>
    </os>
</activation>
```

打开命令行窗口，跳转到 pom.xml 所在的目录下，执行以下 mvn 命令。
mvn clean test

7. Maven 可以根据某些文件存在与否，来决定是否激活 Profile。例如，在 id 为 prod 的 Profile 中添加以下配置，该配置表示当 env.prod.properties 文件存在，且 env.test.properties 文件不存在时，激活该 Profile。

```xml
<activation>
        <file>
            <exists>./src/main/resources/env.prod.properties</exists>
            <missing>./src/main/resources/env.test.properties</missing>
        </file>
</activation>
```

将 env.test.properties 从项目中删除，打开命令行窗口，跳转到 pom.xml 所在目录，执行以下命令。
纯文本复制
mvn clean test

8. 我们可以在声明 Profile 时，指定其默认激活。例如，在 id 为 normal 的 Profile 中添加以下配置，指定该 Profile 默认激活。

```xml
<activation>
    <activeByDefault>true</activeByDefault>
</activation>
```

打开命令行窗口，跳转到 pom.xml 所在目录，执行以下命令。
纯文本复制
mvn clean test

# 镜像

使用镜像代替中央仓库
目前国内使用最多，最稳定的中央仓库镜像分别是由阿里云和华为云提供的，它们的地址配置如下。
阿里云镜像地址

```xml
<mirror>
    <id>aliyun</id>
    <mirrorOf>central</mirrorOf>
    <name>aliyun</name>
    <url>https://maven.aliyun.com/repository/public</url>
</mirror>
```

华为云镜像地址

```xml
<mirror>
    <id>huaweicloud</id>
    <name>mirror from maven huaweicloud</name>
    <mirrorOf>central</mirrorOf>
    <url>https://repo.huaweicloud.com/repository/maven/</url>
</mirror>
```

以上配置中，mirrorOf 的取值为 central，表示该配置为中央仓库的镜像，所有对于中央仓库的请求都会转到该镜像。当然，我们也可以使用以上方式配置其他仓库的镜像。另外三个元素 id、name 和 url 分别表示镜像的唯一标识、名称和地址。

镜像与 Maven 私服配合使用
镜像通常会和 Maven 私服配合使用，由于 Maven 私服可以代理所有外部的公共仓库（包括中央仓库），因此对于组织内部的用户来说，使用一个私服就相当于使用了所有需要的外部仓库，这样就可以将配置集中到私服中，简化 Maven 本身的配置。这种情况下，用户所有所需的构件都可以从私服中获取，此时私服就是所有仓库的镜像。

<srttings>
``` xml
    <mirrors>
        <mirror>
            <id>nexus</id>
            <mirrorOf>*</mirrorOf>
            <name>nexus</name>
            <url>http://localhost:8082/nexus/content/groups/bianchengbang_repository_group/</url>
        </mirror>
    </mirrors>
</settings>
```
以上配置中，mirrorOf 元素的取值为 * ，表示匹配所有远程仓库，所有对于远程仓库的请求都会被拦截，并跳转到 url 元素指定的地址。

为了满足一些较为复杂的需求，Maven 还支持一些更为高级的配置。
<mirrorOf>_</mirrorOf>：匹配所有远程仓库。
<mirrorOf>external:_</mirrorOf>：匹配所有远程仓库，使用 localhost 和 file:// 协议的除外。即，匹配所有不在本机上的远程仓库。
<mirrorOf>repo1,repo2</mirrorOf>：匹配仓库 repo1 和 repo2，使用逗号分隔多个远程仓库。
<mirrorOf>\*,!repo1</miiroOf>：匹配所有远程仓库，repo1 除外，使用感叹号将仓库从匹配中排除。
需要注意的是，由于镜像仓库完全屏蔽了被镜像仓库，当镜像仓库不稳定或者停止服务时，Maven 也无法访问被镜像仓库，因而将无法下载构件。

Maven 私服搭建
能够帮助我们建立私服的软件被称为 Maven 仓库管理器（Repository Manager），主要有以下 3 种：
Apache Archiva
JFrog Artifactory
Sonatype Nexus

其中，Sonatype Nexus 是当前最流行，使用最广泛的 Maven 仓库管理器。

# 问题

## 1 was cached in the local repository, resolution will not be reattempted until the update

- maven 命令后加-U 参数 `mvn clean package -U`
- 删除`~/.m2/repository/`对应目录或目录下的`*.lastUpdated`文件，然后再次运行 maven 命令
- 配置文件修改

```xml
<repositories>
  <repository>
    <id>io.spring.repo.maven.release</id>
    <url>http://repo.spring.io/release/</url>
    <releases>
      <enabled>true</enabled>
      <!-- 修改 -->
      <updatePolicy>always</updatePolicy>
    </releases>
    <snapshots><enabled>false</enabled></snapshots>
  </repository>
</repositories>
```
