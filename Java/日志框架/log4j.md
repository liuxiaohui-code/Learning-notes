## log4j

Log4j 是 Apache 的一个开放源代码项目，通过使用 Log4j，我们可以控制日志信息输送的目的地是控制台、文件、数据库等；我们也可以控制每一条日志的输出格式；通过定义每一条日志信息的级别，我们能够更加细致地控制日志的生成过程。

Log4j 有 7 种不同的 log 级别，按照等级从低到高依次为：TRACE、DEBUG、INFO、WARN、ERROR、FATAL、OFF。如果配置为 OFF 级别，表示关闭 log。Log4j 支持两种格式的配置文件：properties 和 xml。包含三个主要的组件：Logger、appender、Layout。
