## 使用Log4j 1.x桥
也许最简单的转换到使用Log4j 2的方法是用Log4j 2的`log4j-1.2-api.jar`替换log4j 1.x jar文件。但是，使用这个成功的应用程序必须满足以下要求：

1. 他们不能访问Log4j 1.x实现的内部方法和类，例如`Appender s`，`LoggerRepository`或`Category`的`callAppenders`方法。
2. 它们不能以编程方式配置Log4j。
3. 他们不能通过调用类`DOMConfigurator`或`PropertyConfigurator`进行配置。

## 转换为Log4j 2 API
在大多数情况下，从Log4j 1.x API转换为Log4j 2应该是相当简单的。许多日志语句不需要修改。但是，必要时必须进行以下更改。

1. 版本1中的主包是`org.apache.log4j`，在版本2中是 `org.apache.logging.log4j`。
2. 调用`org.apache.log4j.Logger.getLogger（）`必须修改为 `org.apache.logging.log4j.LogManager.getLogger（）`。
3. 调用`org.apache.log4j.Logger.getRootLogger（）`或 `org.apache.log4j.LogManager.getRootLogger（）`必须替换为 `org.apache.logging.log4j.LogManager.getRootLogger（）`。
4. 调用`org.apache.log4j.Logger.getLogger`接收一个`LoggerFactory`参数必须删除`org.apache.log4j.spi.LoggerFactory`和使用Log4j 2的其他扩展机制之一。
5. 调用`org.apache.log4j.Logger.getEffectiveLevel（）`替换为 `org.apache.logging.log4j.Logger.getLevel（） `。
6. 删除对`org.apache.log4j.LogManager.shutdown（）`的调用，因为它们在版本2中不需要，因为Log4j                 Core现在会在启动时自动添加JVM关闭钩子，以执行任何Core清理。
    1. 从Log4j 2.1开始，您可以指定一个自定义的 [`ShutdownCallbackRegistry`][1] 来覆盖默认的JVM shutdown hook策略。
    2. 从Log4j 2.6开始，现在可以使用`org.apache.logging.log4j.LogManager.shutdown（） `手动启动关闭。
7. 在API中不支持对`org.apache.log4j.Logger.setLevel（）`或类似方法的调用。应用程序应删除这些。Log4j 2实现类中提供了等效功能，请参阅`org.apache.logging.log4j.core.config.Configurator.setLevel（）`，但可能会使应用程序容易受到Log4j 2内部更改的影响。
8. 在适当的情况下，应用程序应转换为使用参数化消息而不是String连接。
9. [`org.apache.log4j.MDC`][2]和 [`org.apache.log4j.NDC`][3]已被[Thread Context][4]替换。

## 配置Log4j 2
虽然Log4j 2配置语法与Log4j 1.x不同，但大多数（如果不是全部）相同的功能可用。

请注意，通过`${foo}`语法的系统属性插值已被扩展，以允许来自许多不同来源的属性查找。有关详细信息，请参阅[Lookups][5]文档。例如，在Log4j 1.x中使用名为`catalina.base`的系统属性查找语法为`${catalina.base}`。在Log4j 2中，语法为 `${sys:catalina.base}`。

Log4j 1.x具有与Log4j 2中的XmlLayout不同的XMLLayout，log4j-1.2-api模块包含一个Log4j1XmlLayout，它以格式如Log4j 1.x生成输出。Log4j 1.x `SimpleLayout`可以模拟PatternLayout“%level - %m%n”。Log4j 1.x `TTCCLayout`可以模拟PatternLayout“%r [%t] %p %c %notEmpty{%ndc}- %m%n”。

Log4j 1.x的`PatternLayout`和`EnhancedPatternLayout` 在Log4j 2中可以替换为`PatternLayout`。 在log4j 1.x PatternLayout中 log4j-1.2-API 模块包含两个样式转换“%ndc”和“%properties”可用于模拟“%x”和“%X”（Log4j 2中的“%x”和“%X”格式略有不同）。

以下是Log4j 1.x的示例配置及其在Log4j 2中的配置。

**示例1 - 使用Console Appender进行简单配置**

Log4j 1.x XML配置
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE log4j:configuration PUBLIC "-//APACHE//DTD LOG4J 1.2//EN" "log4j.dtd">
<log4j:configuration xmlns:log4j='http://jakarta.apache.org/log4j/'>
  <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
    <layout class="org.apache.log4j.PatternLayout">
      <param name="ConversionPattern" value="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
    </layout>
  </appender>
  <category name="org.apache.log4j.xml">
    <priority value="info" />
  </category>
  <Root>
    <priority value ="debug" />
    <appender-ref ref="STDOUT" />
  </Root>
</log4j:configuration>
```
Log4j 2 XML配置
```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
  <Appenders>
    <Console name="STDOUT" target="SYSTEM_OUT">
      <PatternLayout pattern="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Logger name="org.apache.log4j.xml" level="info"/>
    <Root level="debug">
      <AppenderRef ref="STDOUT"/>
    </Root>
  </Loggers>
</Configuration>
```

**示例2 - 使用File Appender，XMLLayout和SimpleLayout进行简单配置**

Log4j 1.x XML配置
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE log4j:configuration PUBLIC "-//APACHE//DTD LOG4J 1.2//EN" "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
  <appender name="A1" class="org.apache.log4j.FileAppender">
    <param name="File"   value="A1.log" />
    <param name="Append" value="false" />
    <layout class="org.apache.log4j.xml.XMLLayout" />
  </appender>
  <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
    <layout class="org.apache.log4j.SimpleLayout" />
  </appender>
  <category name="org.apache.log4j.xml">
    <priority value="debug" />
    <appender-ref ref="A1" />
  </category>
  <root>
    <priority value ="debug" />
    <appender-ref ref="STDOUT" />
  </Root>
</log4j:configuration>
```
Log4j 2 XML配置
```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
  <Appenders>
    <File name="A1" fileName="A1.log" append="false">
      <Log4j1XmlLayout />
    </File>
    <Console name="STDOUT" target="SYSTEM_OUT">
      <PatternLayout pattern="%level - %m%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Logger name="org.apache.log4j.xml" level="debug">
      <AppenderRef ref="A1"/>
    </Logger>
    <Root level="debug">
      <AppenderRef ref="STDOUT"/>
    </Root>
  </Loggers>
</Configuration>
```

**示例3 - SocketAppender**

Log4j 1.x XML配置。Log4j 1.x的这个例子是误导的。SocketAppender实际上并不使用布局。配置一个没有任何效果。
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE log4j:configuration PUBLIC "-//APACHE//DTD LOG4J 1.2//EN" "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
  <appender name="A1" class="org.apache.log4j.net.SocketAppender">
    <param name="RemoteHost" value="localhost"/>
    <param name="Port" value="5000"/>
    <param name="LocationInfo" value="true"/>
    <layout class="org.apache.log4j.PatternLayout">
      <param name="ConversionPattern" value="%t %-5p %c{2} - %m%n"/>
    </layout>
  </appender>
  <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
    <layout class="org.apache.log4j.PatternLayout">
      <param name="ConversionPattern" value="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
    </layout>
  </appender>
  <category name="org.apache.log4j.xml">
    <priority value="debug"/>
    <appender-ref ref="A1"/>
  </category>
  <root>
    <priority value="debug"/>
    <appender-ref ref="STDOUT"/>
  </Root>
</log4j:configuration>
```

Log4j 2 XML配置
```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration>
  <Appenders>
    <Socket name="A1" host="localHost" port="5000">
      <PatternLayout pattern="%t %-5p %c{2} - %m%n"/>
    </Socket>
    <Console name="STDOUT" target="SYSTEM_OUT">
      <PatternLayout pattern="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
    </Console>
  </Appenders>
  <Loggers>
    <Logger name="org.apache.log4j.xml" level="debug">
      <AppenderRef ref="A1"/>
    </Logger>
    <Root level="debug">
      <AppenderRef ref="STDOUT"/>
    </Root>
  </Loggers>
</Configuration>
```

**示例4 - AsyncAppender和TTCCLayout**

Log4j 1.x使用AsyncAppender进行XML配置。
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE log4j:configuration PUBLIC "-//APACHE//DTD LOG4J 1.2//EN" "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/" configDebug="true">
  <appender name="ASYNC" class="org.apache.log4j.AsyncAppender">
    <appender-ref ref="TEMP"/>
  </appender>
  <appender name="TEMP" class="org.apache.log4j.FileAppender">
    <param name="File" value="temp"/>
    <layout class="org.apache.log4j.TTCCLayout">
      <param name="ThreadPrinting" value="true"/>
      <param name="CategoryPrefixing" value="true"/>
      <param name="ContextPrinting" value="true"/>
    </layout>
  </appender>
  <root>
    <priority value="debug"/>
    <appender-ref ref="ASYNC"/>
  </Root>
</log4j:configuration>
```

Log4j 2 XML配置。
```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="debug">
  <Appenders>
    <File name="TEMP" fileName="temp">
      <PatternLayout pattern="%r [%t] %p %c %notEmpty{%ndc }- %m%n"/>
    </File>
    <Async name="ASYNC">
      <AppenderRef ref="TEMP"/>
    </Async>
  </Appenders>
  <Loggers>
    <Root level="debug">
      <AppenderRef ref="ASYNC"/>
    </Root>
  </Loggers>
</Configuration>
```

**示例5 - 具有控制台和文件的AsyncAppender**

Log4j 1.x使用AsyncAppender进行XML配置。
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE log4j:configuration PUBLIC "-//APACHE//DTD LOG4J 1.2//EN" "log4j.dtd">
<log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/" configDebug="true">
  <appender name="ASYNC" class="org.apache.log4j.AsyncAppender">
    <appender-ref ref="TEMP"/>
    <appender-ref ref="CONSOLE"/>
  </appender>
  <appender name="CONSOLE" class="org.apache.log4j.ConsoleAppender">
    <layout class="org.apache.log4j.PatternLayout">
      <param name="ConversionPattern" value="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
    </layout>
  </appender>
  <appender name="TEMP" class="org.apache.log4j.FileAppender">
    <param name="File" value="temp"/>
    <layout class="org.apache.log4j.PatternLayout">
      <param name="ConversionPattern" value="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
    </layout>
  </appender>
  <root>
    <priority value="debug"/>
    <appender-ref ref="ASYNC"/>
  </Root>
</log4j:configuration>
```

Log4j 2 XML配置。请注意，Async Appender应在其引用之后进行配置。这将允许它正常关机。
```
<?xml version="1.0" encoding="UTF-8"?>
<Configuration status="debug">
  <Appenders>
    <Console name="CONSOLE" target="SYSTEM_OUT">
      <PatternLayout pattern="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
    </Console>
    <File name="TEMP" fileName="temp">
      <PatternLayout pattern="%d %-5p [%t] %C{2} (%F:%L) - %m%n"/>
    </File>
    <Async name="ASYNC">
      <AppenderRef ref="TEMP"/>
      <AppenderRef ref="CONSOLE"/>
    </Async>
  </Appenders>
  <Loggers>
    <Root level="debug">
      <AppenderRef ref="ASYNC"/>
    </Root>
  </Loggers>
</Configuration>
```

http://logging.apache.org/log4j/2.x/manual/migration.html

[1]: http://logging.apache.org/log4j/2.x/log4j-core/apidocs/org/apache/logging/log4j/core/util/ShutdownCallbackRegistry.html
[2]: http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html
[3]: http://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/NDC.html
[4]: http://logging.apache.org/log4j/2.x/manual/thread-context.html
[5]: http://logging.apache.org/log4j/2.x/manual/lookups.html