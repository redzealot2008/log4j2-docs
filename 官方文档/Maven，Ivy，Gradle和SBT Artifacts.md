Log4j 2被分解到一个API和一个实现（核心）中，API提供了应用程序应该编码的接口。严格地说，Log4j核心只在运行时需要，而不是在编译时。

但是，下面我们将Log4j核心列为编译时依赖项，以提高[自定义插件][1]的启动时间，因为它提供了一个注解处理器，用于生成缓存插件信息的元数据文件以及编译时创建自定义插件所需的代码。

## 在Apache Maven构建中使用Log4j
要使用[Apache Maven][2]构建，请将下面列出的依赖添加到您的`pom.xml`文件中。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.9.1</version>
  </dependency>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-core</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```

## 在Apache Ivy构建中使用Log4j
要使用[Apache Ivy][3]构建，请将以下列出的依赖项添加到`ivy.xml`文件中。

`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-api" rev="2.9.1" />
  <dependency org="org.apache.logging.log4j" name="log4j-core" rev="2.9.1" />
</dependencies>
```

## 在Gradle构建中使用Log4j
要使用[Gradle][4]构建，请将下面列出的依赖项添加到`build.gradle`文件中。

`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-api', version: '2.9.1'
  compile group: 'org.apache.logging.log4j', name: 'log4j-core', version: '2.9.1'
}
```

## 在SBT构建中使用Log4j
要使用[SBT][5]进行构建，请将下面列出的依赖项添加到`build.sbt`文件中。

`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-api" % "2.9.1"
libraryDependencies += "org.apache.logging.log4j" % "log4j-core" % "2.9.1"
```

## 物料清单
为了让您的Log4j模块版本保持同步，提供了BOM pom.xml文件，以方便您使用。为了在[Maven][2]中使用它，将下面列出的依赖关系添加到你的`pom.xml`文件中。在本节中指定版本标识符时，不必在`<dependencies />`部分中指定版本。

`pom.xml`
```
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.apache.logging.log4j</groupId>
      <artifactId>log4j-bom</artifactId>
      <version>2.9.1</version>
      <scope>import</scope>
      <type>pom</type>
    </dependency>
  </dependencies>
</dependencyManagement>
```
要在Gradle中使用它，需要额外的[Gradle插件][6]来实现依赖关系管理功能。

`build.gradle`
```
plugins {
  id 'io.spring.dependency-management' version '1.0.1.RELEASE'
}
 
dependencyManagement {
  imports {
    mavenBom 'org.apache.logging.log4j:log4j-bom:2.9.1'
  }
}
 
dependencies {
  compile 'org.apache.logging.log4j:log4j-api'
  compile 'org.apache.logging.log4j:log4j-core'
  // etc.
}
```

## 可选组件
Log4j 2.x包含了可包含在应用程序中的几个可选组件。

### Log4j 1.x API桥
如果现有组件使用Log4j 1.x并且您希望将此日志记录路由到Log4j 2，请删除所有log4j 1.x依赖项并添加以下内容。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-1.2-api</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-1.2-api" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-1.2-api', version: '2.9.1'
}
```
`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-1.2-api" % "2.9.1"
```

### Apache Commons Logging桥
如果现有组件使用Apache Commons Logging 1.x，并且希望将此日志记录路由到Log4j 2，请添加以下内容，但不要删除任何Commons Logging 1.x依赖项。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-jcl</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-jcl" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-jcl', version: '2.9.1'
}
```
`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-jcl" % "2.9.1"
```

### SLF4J桥
如果现有组件使用SLF4J，并且希望将此日志记录路由到Log4j 2，请添加以下内容，但不要删除任何SLF4J依赖项。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-slf4j-impl</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-slf4j-impl" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-slf4j-impl', version: '2.9.1'
}
```
`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-slf4j-impl" % "2.9.1"
```

### JUL适配器
如果现有组件使用Java Util Logging，并且希望将此日志记录路由到Log4j 2，请添加以下内容。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-jul</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-jul" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-jul', version: '2.9.1'
}
```
`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-jul" % "2.9.1"
```

### Web Servlet支持
为了正确支持和处理Web应用程序的ClassLoader环境和容器生命周期，需要额外的模块。该模块仅在运行时才需要。另外，如果您在OSGi环境中使用servlet，请确保您的首选版本的servlet API已经可用（例如，如果您想使用3.0，但也已加载2.5，请确保两者都已加载）。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-web</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-web" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-web', version: '2.9.1'
}
```
`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-web" % "2.9.1"
```

### 标签库
Log4j日志标签库创建了在不使用Java脚本的情况下在JSP中插入日志语句的功能。它使用标准的Log4j 2 API根据Log4j配置记录消息。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-taglib</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-taglib" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-taglib', version: '2.9.1'
}
```
`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-taglib" % "2.9.1"
```

### Apache Flume Appender
Flume Appender允许应用程序向Flume Agent发送事件。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-flume-ng</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-flume-ng" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-flume-ng', version: '2.9.1'
}
```
`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-flume-ng" % "2.9.1"
```

### Log4j到SLF4J适配器
Log4j 2到SLF4J适配器允许编码到Log4j 2 API的应用程序被路由到SLF4J。使用此适配器可能会导致性能损失，因为Log4j 2消息必须先格式化，然后才能传递给SLF4J。使用时，SLF4J桥不得在class路径上。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-to-slf4j</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-to-slf4j" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-to-slf4j', version: '2.9.1'
}
```
`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-to-slf4j" % "2.9.1" 
```

### NoSQL Appenders
如果您的配置使用NoSQL Appender之一，请添加以下内容。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-nosql</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-nosql" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-nosql', version: '2.9.1'
}
```
`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-nosql" % "2.9.1"
```

### IO流
Log4j IO Streams允许应用程序将写入到OutputStream或Writer的数据重定向到Logger，或让Logger窃听从InputStream或Reader读取的数据。要使用IO流，请添加以下内容。

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-iostreams</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-iostreams" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-iostreams', version: '2.9.1'
}
```
`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" % "log4j-iostreams" % "2.9.1"
```

### Scala API
Logger API的一个方便的Scala包装器。SBT用户可以将以下内容添加到他们的`build.sbt`：

`build.sbt`
```
libraryDependencies += "org.apache.logging.log4j" %% "log4j-api-scala" % "2.9.1"
```
Maven，Ivy和Gradle用户需要将Scala版本添加到工件名称。

Scala 2.12用户可以使用以下内容：

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api-scala_2.12</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-api-scala_2.12" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-api-scala_2.12', version: '2.9.1'
}
```
Scala 2.11用户可以使用以下内容：

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api-scala_2.11</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-api-scala_2.11" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-api-scala_2.11', version: '2.9.1'
}
```
Scala 2.10用户可以使用以下内容：

`pom.xml`
```
<dependencies>
  <dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api-scala_2.10</artifactId>
    <version>2.9.1</version>
  </dependency>
</dependencies>
```
`ivy.xml`
```
<dependencies>
  <dependency org="org.apache.logging.log4j" name="log4j-api-scala_2.10" rev="2.9.1" />
</dependencies>
```
`build.gradle`
```
dependencies {
  compile group: 'org.apache.logging.log4j', name: 'log4j-api-scala_2.10', version: '2.9.1'
}
```

## 快照构建
你可以通过使用Maven仓库`https://repository.apache.org/snapshots`和当前的SNAPSHOT版本来访问最新的开发快照。通常，主分支将使用下一个补丁版本作为其快照版本，而不管下一个实际版本的Log4j是什么。例如，如果最新版本是`2.0`，那么master将使用版本`2.0.1-SNAPSHOT`。请始终按照[源仓库页面][7]中所述，使用master分支中的`pom.xml`进行验证。

### Maven
Maven用户可以将以下内容添加到他们的`pom.xml`中以启用快照：
```
<repositories>
  <repository>
    <id>apache.snapshots</id>
    <name>Apache Snapshot Repository</name>
    <url>https://repository.apache.org/snapshots</url>
    <releases>
      <enabled>false</enabled>
    </releases>
  </repository>
</repositories>
```

### Gradle
Gradle用户可以将以下内容添加到他们的`build.gradle`中以启用快照：
```
repositories {
  mavenCentral()
  maven { url 'https://repository.apache.org/snapshots' }
}
```

### SBT
SBT用户可以将以下内容添加到其`build.sbt`以启用快照：
```
resolvers += "Apache Snapshot Repository" at "https://repository.apache.org/snapshots"
```

http://logging.apache.org/log4j/2.x/maven-artifacts.html

[1]:http://logging.apache.org/log4j/2.x/manual/plugins.html
[2]:https://maven.apache.org/
[3]:https://ant.apache.org/ivy/
[4]:https://gradle.org/
[5]:http://www.scala-sbt.org/
[6]:https://github.com/spring-gradle-plugins/dependency-management-plugin
[7]:http://logging.apache.org/log4j/2.x/source-repository.html