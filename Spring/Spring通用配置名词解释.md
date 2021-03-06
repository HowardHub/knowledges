说明：这是spring配置文件的通用名词解释，来源于 https://docs.spring.io/spring-boot/docs/current/reference/html/application-properties.html#appendix.application-properties



＃================================================= ================== 
＃COMMON SPRING BOOT PROPERTIES 
＃============================================== =====================

＃---------------------------------------- 

#### #CORE PROPERTIES 

＃----- ----------------------------------- 
debug = false ＃启用调试日志。
trace = false ＃启用跟踪日志。

##### #logging

logging.config = ＃日志配置文件的位置。例如，Logback的`classpath：logback.xml`。
logging.exception-conversion-word =％wEx ＃记录异常时使用的转换字。
logging.file = ＃日志文件名（例如，`myapp.log`）。名称可以是精确位置或相对于当前目录。
logging.file.max-history = 0 ＃要保留的归档日志文件的最大值。仅支持默认的logback设置。
logging.file.max-size = 10MB ＃最大日志文件大小。仅支持默认的logback设置。
logging.group。* =＃记录组以同时快速更改多个记录器。例如，`logging.level.db = org.hibernate，org.springframework.jdbc`。
logging.level。* = ＃日志级别严重性映射。例如，`logging.level.org.springframework = DEBUG`。
logging.path = ＃日志文件的位置。例如，`/ var / log`。
logging.pattern.console = ＃用于输出到控制台的Appender模式。仅支持默认的Logback设置。
logging.pattern.dateformat = yyyy-MM-dd HH：mm：ss.SSS ＃日志日期格式的Appender模式。仅支持默认的Logback设置。
logging.pattern.file =＃用于输出到文件的Appender模式。仅支持默认的Logback设置。
logging.pattern.level =％5p ＃日志级别的Appender模式。仅支持默认的Logback设置。
logging.register-shutdown-hook = false ＃在日志记录系统初始化时注册一个关闭钩子。



##### ＃AOP 

spring.aop.auto =真＃添加@EnableAspectJAutoProxy。
spring.aop.proxy-target-class = true ＃是否要创建基于子类的（CGLIB）代理（true），而不是基于标准Java接口的代理（false）。



##### #IDENTITY （ContextIdApplicationContextInitializer）

spring.application.name = #Application name。



##### #DINAND （SpringApplicationAdminJmxAutoConfiguration）

spring.application.admin.enabled = false ＃是否为应用程序启用管理功能。
spring.application.admin.jmx-name = org.springframework.boot：type = Admin，name = SpringApplication #JMX 应用程序管理员MBean的名称。



##### #AUTO-CONFIGURATION 

spring.autoconfigure.exclude = ＃要排除的自动配置类。



##### ＃BANNER 

spring.banner.charset = UTF-8 ＃横幅文件编码。
spring.banner.location = classpath：banner.txt ＃横幅文本资源位置。
spring.banner.image.location = classpath：banner.gif ＃横幅图像文件位置（也可以使用jpg或png）。
spring.banner.image.width = 76 ＃字符中的横幅图像的宽度。
spring.banner.image.height = #crs 中横幅图像的高度（默认基于图像高度）。
spring.banner.image.margin = 2 ＃字符中的左手图像边距。
spring.banner.image.invert = false ＃是否应针对暗终端主题反转图像。



##### #SPRING CORE 

spring.beaninfo.ignore = true ＃是否跳过BeanInfo类的搜索。



##### #SPRING CACHE（CacheProperties）

spring.cache.cache-names = #Cmama 分隔的要创建的缓存名称列表（如果底层缓存管理器支持）。
spring.cache.caffeine.spec = ＃用于创建缓存的规范。有关规格格式的更多详细信息，请参阅CaffeineSpec。
spring.cache.couchbase.expiration = ＃条目到期。默认情况下，条目永不过期。请注意，此值最终会转换为秒。
spring.cache.ehcache.config = ＃用于初始化EhCache的配置文件的位置。
spring.cache.infinispan.config = ＃用于初始化Infinispan的配置文件的位置。
spring.cache.jcache.config = ＃用于初始化缓存管理器的配置文件的位置。
spring.cache.jcache.provider = #CachingProvider实现的完全限定名称，用于检索符合JSR-107的缓存管理器。仅当类路径上有多个JSR-107实现时才需要。
spring.cache.redis.cache-null-values = true ＃允许缓存空值。
spring.cache.redis.key-prefix = ＃键前缀。
spring.cache.redis.time-to-live = ＃条目到期。默认情况下，条目永不过期。
spring.cache.redis.use-key-prefix = true＃写入Redis时是否使用密钥前缀。
spring.cache.type = #Cache 类型。默认情况下，根据环境自动检测。



##### #SPRING CONFIG  - 仅使用环境属性（ConfigFileApplicationListener）

spring.config.additional-location = ＃配置除默认值之外使用的文件位置。
spring.config.location = ＃配置替换默认值的文件位置。
spring.config.name = application ＃配置文件名。



##### #HAZELCAST（HazelcastProperties）

spring.hazelcast.config = ＃用于初始化Hazelcast的配置文件的位置。



##### #PROJECT INFORMATION（ProjectInfoProperties）

spring.info.build.encoding = UTF-8 ＃文件编码。
spring.info.build.location = classpath：META-INF / build-info.properties ＃生成的build-info.properties文件的位置。
spring.info.git.encoding = UTF-8 ＃文件编码。
spring.info.git.location =类路径：git.properties 生成的git.properties文件＃所在。



##### ＃JMX 

spring.jmx.default域 = ＃JMX域名。
spring.jmx.enabled = true ＃将管理bean公开给JMX域。
spring.jmx.server = mbeanServer ＃MBeanServer bean name。
spring.jmx.unique-names = false ＃是否应确保唯一的运行时对象名称。



##### #Email （MailProperties）

spring.mail.default-encoding = UTF-8 ＃默认MimeMessage编码。
spring.mail.host = #SMTP 服务器主机。例如，`smtp.example.com`。
spring.mail.jndi-name = ＃会话JNDI名称。设置时，优先于其他会话设置。
spring.mail.password = #SMTP 服务器的登录密码。
spring.mail.port = #SMTP 服务器端口。
spring.mail.properties。* = ＃其他JavaMail会话属性。
spring.mail.protocol = smtp ＃SMTP服务器使用的协议。
spring.mail.test-connection = false＃是否在启动时测试邮件服务器是否可用。
spring.mail.username = #SMTP 服务器的登录用户。



##### #APICING SETTINGS（SpringApplication）

spring.main.allow-bean-definition-overriding = false ＃是否允许通过注册与现有定义同名的定义来覆盖bean定义。
spring.main.banner-mode = console ＃模式用于在应用程序运行时显示横幅。
spring.main.sources = 要包含在ApplicationContext中的



##### #Sources （类名，包名或XML资源位置）。

spring.main.web-application-type = ＃用于显式请求特定类型的Web应用程序的标志。如果未设置，则基于类路径自动检测。



##### #FILE ENCODING（FileEncodingApplicationListener）

 spring.mandatory-file-encoding = ＃应用程序必须使用的预期字符编码。



##### #INTERINGIZATION （MessageSourceProperties）

spring.messages.always-use-message-format = false ＃是否始终应用MessageFormat规则，甚至解析不带参数的消息。
spring.messages.basename = messages ＃逗号分隔的basenames列表（本质上是一个完全限定的类路径位置），每个都遵循ResourceBundle约定，轻松支持基于斜杠的位置。
spring.messages.cache-duration = ＃加载的资源包文件缓存持续时间。未设置时，捆绑包将永久缓存。如果未指定持续时间后缀，则将使用秒。
spring.messages.encoding = UTF-8 ＃消息包编码。
spring.messages.fallback-to-system-locale = true ＃如果找不到特定区域设置的文件，是否回退到系统区域设置。
spring.messages.use-code-as-default-message = false ＃是否使用消息代码作为默认消息而不是抛出“NoSuchMessageException”。仅在开发期间推荐。



##### ＃OUTPUT 

spring.output.ansi.enabled =检测＃配置的ANSI输出。



##### #PID FILE（ApplicationPidFileWriter）

spring.pid.fail-on-write-error = ＃如果使用ApplicationPidFileWriter但它无法写入PID文件，则失败。
spring.pid.file = ＃要写入的PID文件的位置（如果使用ApplicationPidFileWriter）。



##### ＃PROFILES 

spring.profiles.active = ＃逗号分隔的有源配置文件列表。可以通过命令行开关覆盖。
spring.profiles.include = ＃无条件地激活指定的逗号分隔的配置文件列表（如果使用YAML，则激活配置文件列表）。



##### ＃Quartz调度器（QuartzProperties）

spring.quartz.auto-启动 =真＃是否自动启动初始化后的调度。
spring.quartz.jdbc.comment-prefix =  - #SQL 初始化脚本中单行注释的前缀。
spring.quartz.jdbc.initialize-schema = embedded ＃数据库模式初始化模式。
spring.quartz.jdbc.schema = classpath：org / quartz / impl / jdbcjobstore / tables_ @ @ platform @@ .sql ＃用于初始化数据库模式的SQL文件的路径。
spring.quartz.job-store-type = memory ＃Quartz作业存储类型。
spring.quartz.overwrite-existing-jobs = false ＃配置的作业是否应覆盖现有的作业定义。
spring.quartz.properties。* = ＃其他Quartz Scheduler属性。
spring.quartz.scheduler-name = quartzScheduler ＃调度程序的名称。
spring.quartz.startup-delay = 0s ＃一旦初始化完成，调度程序启动之后的延迟。
spring.quartz.wait-for-jobs-to-complete-on-shutdown = false ＃是否等待在关闭时运行的作业完成。



##### #REACTOR （ReactorCoreProperties）

spring.reactor.stacktrace -mode.enabled = false #Reactor 是否应该在运行时收集堆栈跟踪信息。



##### #SENDGRID（SendGridAutoConfiguration）

 spring.sendgrid.api-key = ＃SendGrid API密钥。
spring.sendgrid.proxy.host = ＃SendGrid代理主机。
spring.sendgrid.proxy.port = ＃SendGrid代理端口。



##### #TASK EXECUTION（TaskExecutionProperties）

 spring.task.execution.pool.allow-core-thread-timeout = true ＃是否允许核心线程超时。这可以实现池的动态增长和收缩。
spring.task.execution.pool.core-size = 8 ＃核心线程数。
spring.task.execution.pool.keep-alive = 60s ＃终止之前线程可能保持空闲的时间限制。
spring.task.execution.pool.max-size = ＃允许的最大线程数。如果任务正在填满队列，则池可以扩展到该大小以适应负载。如果队列无限制，则忽略。
spring.task.execution.pool.queue-capacity =＃队列容量。无限制的容量不会增加池，因此会忽略“max-size”属性。
spring.task.execution.thread-name-prefix = task- ＃用于新创建的线程名称的前缀。



##### #TASK SCHEDULING（TaskSchedulingProperties）

spring.task.scheduling.pool.size = 1 ＃允许的最大线程数。
spring.task.scheduling.thread-name-prefix = scheduling- ＃用于新创建的线程名称的前缀。





＃---------------------------------------- 

#### ＃WEB PROPERTIES 

＃----- -----------------------------------

##### #EmbEDDED SERVER CONFIGURATION（ServerProperties）

server.address = ＃服务器应绑定到的网络地址。
server.compression.enabled = false ＃是否启用了响应压缩。
server.compression.excluded-user-agents = ＃逗号分隔的用户代理列表，不应压缩响应。
server.compression.mime-types = text / html，text / xml，text / plain，text / css，text / javascript，application / javascript，application / json，application / xml ＃逗号分隔的MIME类型列表应该是压缩。
server.compression.min-response-size = 2KB＃执行压缩所需的最小“Content-Length”值。
server.connection-timeout = ＃连接器在关闭连接之前等待另一个HTTP请求的时间。未设置时，将使用连接器的特定于容器的默认值。使用值-1表示没有（即无限）超时。
server.error.include-exception = false ＃包含“exception”属性。
server.error.include-stacktrace = never ＃何时包含“stacktrace”属性。
server.error.path = / error ＃错误控制器的路径。
server.error.whitelabel.enabled = true＃是否在服务器出错时启用浏览器中显示的默认错误页面。
server.http2.enabled = false ＃是否启用HTTP / 2支持，如果当前环境支持它。
server.jetty.acceptors = -1 ＃要使用的接受者线程数。当值为-1（默认值）时，接受器的数量是从操作环境派生的。
server.jetty.accesslog.append = false ＃追加到日志。
server.jetty.accesslog.date-format = dd / MMM / yyyy：HH：mm：ss Z ＃请求日志的时间戳格式。
server.jetty.accesslog.enabled = false ＃启用访问日志。
server.jetty.accesslog.extended-format = false＃启用扩展NCSA格式。
server.jetty.accesslog.file-date-format = ＃日期文件名中的日期格式。
server.jetty.accesslog.filename = ＃日志文件名。如果未指定，则日志重定向到“System.err”。
server.jetty.accesslog.locale = ＃请求日志的区域设置。
server.jetty.accesslog.log-cookies = false ＃启用请求cookie的记录。
server.jetty.accesslog.log-latency = false ＃启用请求处理时间的记录。
server.jetty.accesslog.log-server = false ＃启用请求主机名的日志记录。
server.jetty.accesslog.retention-period = 31＃删除旋转日志文件之前的天数。
server.jetty.accesslog.time-zone = GMT ＃请求日志的时区。
server.jetty.max-http-post-size = 200000B #HTTP post或put内容的最大大小。
server.jetty.selectors = -1 ＃要使用的选择器线程数。当值为-1（默认值）时，选择器的数量是从操作环境派生的。
server.max-http-header-size = 8KB #HTTP 邮件头的最大大小。
server.port = 8080 ＃服务器HTTP端口。
server.server-header = ＃用于Server响应头的值（如果为空，则不发送头）。
server.use-forward-headers = ＃是否应将X-Forwarded- *标头应用于HttpRequest。
server.servlet.context-parameters。* = ＃Servlet context init参数。
server.servlet.context-path = ＃应用程序的上下文路径。
server.servlet.application-display-name = application ＃显示
应用程序的名称。server.servlet.jsp.class-name = org.apache.jasper.servlet.JspServlet ＃用于JSP的servlet的类名。
server.servlet.jsp.init-parameters。* = ＃用于配置JSP servlet的Init参数。
server.servlet.jsp.registered = true＃是否已注册JSP servlet。
server.servlet.session.cookie.comment = ＃会话cookie的评论。
server.servlet.session.cookie.domain = ＃会话cookie的域名。
server.servlet.session.cookie.http-only = ＃是否对会话cookie使用“HttpOnly”cookie。
server.servlet.session.cookie.max-age = ＃会话cookie的最大年龄。如果未指定持续时间后缀，则将使用秒。
server.servlet.session.cookie.name = ＃会话cookie名称。
server.servlet.session.cookie.path = ＃会话cookie的路径。
server.servlet.session.cookie.secure =＃是否始终将会话cookie标记为安全。
server.servlet.session.persistent = false ＃是否在重新启动之间保留会话数据。
server.servlet.session.store-dir = ＃用于存储会话数据的目录。
server.servlet.session.timeout = 30m ＃会话超时。如果未指定持续时间后缀，则将使用秒。
server.servlet.session.tracking-modes = ＃会话跟踪模式。
server.ssl.ciphers = ＃支持的SSL密码。
server.ssl.client-auth = ＃客户端身份验证模式。
server.ssl.enabled = true ＃是否启用SSL支持。
server.ssl.enabled-protocols = ＃启用SSL协议。
server.ssl.key-alias = ＃标识密钥库中密钥的别名。
server.ssl.key-password = ＃用于访问密钥库中密钥的密码。
server.ssl.key-store = ＃保存SSL证书的密钥库的路径（通常是jks文件）。
server.ssl.key-store-password = ＃用于访问密钥库的密码。
server.ssl.key-store-provider = ＃密钥库的提供者。
server.ssl.key-store-type = ＃密钥库的类型。
server.ssl.protocol = TLS ＃要使用的SSL协议。
server.ssl.trust-store = ＃持有SSL证书的信任存储。
server.ssl.trust-store-password = ＃用于访问信任库的密码。
server.ssl.trust-store-provider = ＃信任存储的提供者。
server.ssl.trust-store-type = ＃信任库的类型。
server.tomcat.accept-count = 100 ＃当所有可能的请求处理线程都在使用时，传入连接请求的最大队列长度。
server.tomcat.accesslog.buffered = true ＃是否缓冲输出，使其仅定期刷新。
server.tomcat.accesslog.directory = logs＃创建日志文件的目录。可以绝对或相对于Tomcat基础目录。
server.tomcat.accesslog.enabled = false ＃启用访问日志。
server.tomcat.accesslog.file-date-format = .yyyy-MM-dd ＃要放在日志文件名中的日期格式。
server.tomcat.accesslog.pattern = common ＃访问日志的格式模式。
server.tomcat.accesslog.prefix = access_log ＃日志文件名前缀。
server.tomcat.accesslog.rename-on-rotate = false ＃是否延迟在文件名中包含日期戳，直到旋转时间。
server.tomcat.accesslog.request-attributes-enabled = false＃设置请求的IP地址，主机名，协议和端口的请求属性。
server.tomcat.accesslog.rotate = true ＃是否启用访问日志轮换。
server.tomcat.accesslog.suffix = .log ＃日志文件名后缀。
server.tomcat.additional-tld-skip-patterns = ＃逗号分隔的其他模式列表，这些模式匹配要忽略的TLD扫描的jar。
server.tomcat.background-processor-delay = 10s #backgroundProcess 方法调用之间的延迟。如果未指定持续时间后缀，则将使用秒。
server.tomcat.basedir = #Tomcat 基目录。如果未指定，则使用临时目录。
server.tomcat.internal-proxies = 10 \\。\\ d {1,3} \\。\\ d {1,3} \\。\\ d {1,3} | \\
        。192 \\ 168 \\ d {1,3} \\ d {1,3} | \\
        。169 \\ 254 \\ d {1,3} \\ d {1,3} | \\
        。127 \\ d {1,3} \\ d {1,3} \\ d {1,3} | \\
        172 \\ 1 [6-9] {1} \\ d {1,3} \\ d {1,3} |。。\\
        172 \\ 2 [0-9] {1} \\ d {1,3} \\ d {1,3} |。。\\
        172 \\。3 [0-1] {1} \\。\\ d {1,3} \\。\\ d {1,3} \\
        0：0：0：0：0：0：0：1 \\
         :: 1 ＃正则表达式匹配要信任的代理。
server.tomcat.max-connections = 10000 ＃服务器在任何给定时间接受和处理的最大连接数。
server.tomcat.max-http-post-size = 2MB #HTTP 帖子内容的最大大小。
server.tomcat.max-swallow-size = 2MB ＃要吞咽的请求正文的最大数量。
server.tomcat.max-threads = 200 ＃最大工作线程数。
server.tomcat.min-spare-threads = 10 ＃最小工作线程数。
server.tomcat.port-header = X-Forwarded-Port＃用于覆盖原始端口值的HTTP头的名称。
server.tomcat.protocol-header = ＃包含传入协议的头文件，通常命名为“X-Forwarded-Proto”。
server.tomcat.protocol-header-https-value = https ＃协议标头的值，指示传入请求是否使用SSL。
server.tomcat.redirect-context-root = true ＃是否应通过在路径中附加/来重定向对上下文根的请求。
server.tomcat.remote-ip-header = ＃从中提取远程IP的HTTP头的名称。例如，`X-FORWARDED-FOR`。
server.tomcat.resource.allow-caching = true＃是否允许此Web应用程序使用静态资源缓存。
server.tomcat.resource.cache-ttl = ＃静态资源缓存的生存时间。
server.tomcat.uri-encoding = UTF-8 ＃用于解码URI的字符编码。
server.tomcat.use-relative-redirects = ＃通过调用sendRedirect生成的HTTP 1.1和更高版本的位置标头是使用相对还是绝对重定向。
server.undertow.accesslog.dir = #Undertow 访问日志目录。
server.undertow.accesslog.enabled = false ＃是否启用访问日志。
server.undertow.accesslog.pattern = common ＃访问日志的格式模式。
server.undertow.accesslog.prefix = access_log。＃日志文件名前缀。
server.undertow.accesslog.rotate = true ＃是否启用访问日志轮换。
server.undertow.accesslog.suffix = log ＃日志文件名后缀。
server.undertow.buffer-size = ＃每个缓冲区的大小。
server.undertow.direct-buffers = ＃是否在Java堆外部分配缓冲区。默认值源自JVM可用的最大内存量。
server.undertow.eager-filter-init = true ＃是否应在启动时初始化servlet过滤器。
server.undertow.io-threads =＃为worker创建的I / O线程数。默认值源自可用处理器的数量。
server.undertow.max-http-post-size = -1B #HTTP 帖子内容的最大大小。当值为-1时，默认值为大小无限制。
server.undertow.worker-threads = ＃工作线程数。默认值是I / O线程数的8倍。



##### #FREEMARKER（FreeMarkerProperties）

spring.freemarker.allow-request-override = false ＃是否允许HttpServletRequest属性覆盖（隐藏）控制器生成的同名模型属性。
spring.freemarker.allow-session-override = false ＃是否允许HttpSession属性覆盖（隐藏）控制器生成的同名模型属性。
spring.freemarker.cache = false ＃是否启用模板缓存。
spring.freemarker.charset = UTF-8 ＃模板编码。
spring.freemarker.check-template-location = true ＃是否检查模板位置是否存在。
spring.freemarker.content-type = text / html ＃Content-Type value。
spring.freemarker.enabled = true ＃是否为此技术启用MVC视图分辨率。
spring.freemarker.expose-request-attributes = false ＃是否应在与模板合并之前将所有请求属性添加到模型中。
spring.freemarker.expose-session-attributes = false ＃是否应在与模板合并之前将所有HttpSession属性添加到模型中。
spring.freemarker.expose-spring-macro-helpers = true ＃是否公开一个RequestContext供Spring的宏库使用，名称为“springMacroRequestContext”。
spring.freemarker.prefer-file-system-access = true ＃是否更喜欢文件系统访问以进行模板加载。文件系统访问可以热检测模板更改。
spring.freemarker.prefix = ＃在构建URL时添加前缀以查看名称的前缀。
spring.freemarker.request-context-attribute = ＃所有视图的
RequestContext属性的名称。spring.freemarker.settings。* = ＃众所周知的FreeMarker密钥，传递给FreeMarker的配置。
spring.freemarker.suffix = .ftl ＃在构建URL时附加到视图名称的后缀。
spring.freemarker.template-loader-path = classpath：/ templates /＃逗号分隔的模板路径列表。
spring.freemarker.view-names = ＃可以解析的视图名称的白名单。



##### #GLOVY TEMPLATES（GroovyTemplateProperties）

spring.groovy.template.allow-request-override = false ＃是否允许HttpServletRequest属性覆盖（隐藏）控制器生成的同名模型属性。
spring.groovy.template.allow-session-override = false ＃是否允许HttpSession属性覆盖（隐藏）控制器生成的同名模型属性。
spring.groovy.template.cache = false ＃是否启用模板缓存。
spring.groovy.template.charset = UTF-8 ＃模板编码。
spring.groovy.template.check-template-location = true＃是否检查模板位置是否存在。
spring.groovy.template.configuration。* = ＃请参阅GroovyMarkupConfigurer 
spring.groovy.template.content-type = text / html ＃Content-Type value。
spring.groovy.template.enabled = true ＃是否为此技术启用MVC视图分辨率。
spring.groovy.template.expose-request-attributes = false ＃是否应在与模板合并之前将所有请求属性添加到模型中。
spring.groovy.template.expose-session-attributes = false ＃在与模板合并之前是否应将所有HttpSession属性添加到模型中。
spring.groovy.template.expose-spring-macro-helpers = true ＃是否公开一个RequestContext供Spring的宏库使用，名称为“springMacroRequestContext”。
spring.groovy.template.prefix = ＃在构建URL时添加前缀以查看名称的前缀。
spring.groovy.template.request-context-attribute = ＃所有视图的
RequestContext属性的名称。spring.groovy.template.resource-loader-path = classpath：/ templates / ＃Template path。
spring.groovy.template.suffix = .tpl ＃在构建URL时附加到视图名称的后缀。
spring.groovy.template.view-names =＃可以解析的视图名称的白名单。



##### #SPRING HATEOAS（HateoasProperties）

spring.hateoas.use-hal-as-default-json-media-type = true ＃是否应将application / hal + json响应发送给接受application / json的请求。



##### #HTTP （HttpProperties）

spring.http.converters.preferred-json-mapper = ＃用于HTTP消息转换的首选JSON映射器。默认情况下，根据环境自动检测。
spring.http.encoding.charset = UTF-8 #HTTP 请求和响应的字符集。如果未明确设置，则添加到“Content-Type”标头。
spring.http.encoding.enabled = true ＃是否启用http编码支持。
spring.http.encoding.force = ＃是否在HTTP请求和响应上强制编码到已配置的字符集。
spring.http.encoding.force-request =＃是否在HTTP请求中强制编码到配置的字符集。如果未指定“force”，则默认为true。
spring.http.encoding.force-response = ＃是否在HTTP响应中强制编码到配置的字符集。
spring.http.encoding.mapping = ＃用于编码映射的Locale。
spring.http.log-request-details = false ＃是否允许在DEBUG和TRACE级别记录（可能敏感的）请求详细信息。



##### #MULTIPART （MultipartProperties）

spring.servlet.multipart.enabled = true ＃是否启用分段上传支持。
spring.servlet.multipart.file-size-threshold = 0B ＃将文件写入磁盘后的阈值。
spring.servlet.multipart.location = ＃上传文件的中间位置。
spring.servlet.multipart.max-file-size = 1MB ＃最大文件大小。
spring.servlet.multipart.max-request-size = 10MB ＃最大请求大小。
spring.servlet.multipart.resolve-lazily = false ＃是否在文件或参数访问时懒惰地解析多部分请求。



##### #JACKSON （JacksonProperties）

spring.jackson.date-format = ＃日期格式字符串或完全限定的日期格式类名。例如，`yyyy-MM-dd HH：mm：ss`。
spring.jackson.default-property-inclusion = ＃控制序列化期间包含的属性。配置了Jackson的JsonInclude.Include枚举中的一个值。
spring.jackson.deserialization。* = #Jackson on / off功能会影响Java对象的反序列化方式。
spring.jackson.generator。* = ＃Jackson开/关功能的发电机。
spring.jackson.joda-date-time-format =#Joda日期时间格式字符串。如果未配置，如果使用格式字符串配置，则使用“date-format”作为后备。
spring.jackson.locale = ＃用于格式化的区域设置。
spring.jackson.mapper。* = #Jackson 通用开/关功能。
spring.jackson.parser。* = ＃Jackson开启/关闭解析器的功能。
spring.jackson.property-naming-strategy = #Jackson PropertyNamingStrategy的常数之一。也可以是PropertyNamingStrategy子类的完全限定类名。
spring.jackson.serialization。* = #Jacker on / off功能会影响Java对象的序列化方式。
spring.jackson.time-zone =＃格式化日期时使用的时区。例如，“America / Los_Angeles”或“GMT + 10”。
spring.jackson.visibility。* = #Jackson 可见性阈值，可用于限制自动检测哪些方法（和字段）。



##### #GSON（GsonProperties）

 spring.gson.date-format = ＃序列化Date对象时使用的格式。
spring.gson.disable -html-escaping = ＃是否禁用HTML字符的转义，例如'<'，'>'等
spring.gson.disable-inner-class-serialization = ＃是否在内容类中排除内部类序列化。
spring.gson.enable-complex-map-key-serialization = ＃是否启用复杂映射键（即非基元）的序列化。
spring.gson.exclude-fields-without-expose-annotation = ＃是否排除所有不考虑序列化或反序列化但没有“Expose”注释的字段。
spring.gson.field-naming-policy = ＃在序列化和反序列化期间应该应用于对象字段的命名策略。
spring.gson.generate-non-executable-json = ＃是否通过在输出前添加一些特殊文本来生成不可执行的JSON。
spring.gson.lenient = ＃是否宽容解析不符合RFC 4627的
JSON.chring.gson.long-serialization-policy = ＃长和长类型的序列化策略。
spring.gson.pretty-printing = ＃是否输出适合页面的序列化JSON以进行漂亮的打印。
spring.gson.serialize-nulls = ＃是否序列化空字段。



##### #JERSEY （JerseyProperties）

spring.jersey.application-path = ＃作为应用程序基URI的路径。如果指定，则覆盖“@ApplicationPath”的值。
spring.jersey.filter.order = 0 ＃Jersey过滤链顺序。
spring.jersey.init。* = ＃通过servlet或过滤器传递给Jersey的Init参数。
spring.jersey.servlet.load-on-startup = -1 ＃加载Jersey servlet的启动优先级。
spring.jersey.type = servlet ＃Jersey集成类型。



##### #SPRING LDAP（LdapProperties）

spring.ldap.anonymous-read-only = false ＃只读操作是否应使用匿名环境。
spring.ldap.base = #Base 后缀，所有操作都应该来自该后缀。
spring.ldap.base-environment。* = #LDAP 规范设置。
spring.ldap.password = ＃服务器的登录密码。
spring.ldap.urls = ＃服务器的LDAP URL。
spring.ldap.username = ＃登录服务器的用户名。



##### ＃EMBEDDED LDAP（EmbeddedLdapProperties）

spring.ldap.embedded.base-dn = ＃基本DN列表。
spring.ldap.embedded.credential.username = ＃嵌入式LDAP用户名。
spring.ldap.embedded.credential.password = ＃嵌入式LDAP密码。
spring.ldap.embedded.ldif = classpath：schema.ldif #Schema （LDIF）脚本资源引用。
spring.ldap.embedded.port = 0 ＃嵌入式LDAP端口。
spring.ldap.embedded.validation.enabled = true ＃是否启用LDAP模式验证。
spring.ldap.embedded.validation.schema = ＃自定义架构的路径。



##### #MUSTACHE TEMPLATES（MustacheAutoConfiguration）

spring.mustache.allow-request-override = false ＃是否允许HttpServletRequest属性覆盖（隐藏）控制器生成的同名模型属性。
spring.mustache.allow-session-override = false ＃是否允许HttpSession属性覆盖（隐藏）控制器生成的同名模型属性。
spring.mustache.cache = false ＃是否启用模板缓存。
spring.mustache.charset = UTF-8 ＃模板编码。
spring.mustache.check-template-location = true ＃是否检查模板位置是否存在。
spring.mustache.content-type = text / html ＃Content-Type value。
spring.mustache.enabled = true ＃是否为此技术启用MVC视图分辨率。
spring.mustache.expose-request-attributes = false ＃在与模板合并之前是否应将所有请求属性添加到模型中。
spring.mustache.expose-session-attributes = false ＃在与模板合并之前是否应将所有HttpSession属性添加到模型中。
spring.mustache.expose-spring-macro-helpers = true ＃是否公开一个RequestContext供Spring的宏库使用，名称为“springMacroRequestContext”。
spring.mustache.prefix= classpath：/ templates / ＃适用于模板名称的前缀。
spring.mustache.request-context-attribute = ＃所有视图的
RequestContext属性的名称。spring.mustache.suffix = .mustache ＃后缀应用于模板名称。
spring.mustache.view-names = ＃可以解析的视图名称的白名单。



##### #SPRING MVC（WebMvcProperties）

spring.mvc.async.request-timeout = ＃异步请求处理超时之前的时间。

spring.mvc.contentnegotiation.favor-parameter = false ＃是否应使用请求参数（默认为“format”）来确定请求的媒体类型。
spring.mvc.contentnegotiation.favor-path-extension = false ＃是否应使用URL路径中的路径扩展来确定所请求的媒体类型。
spring.mvc.contentnegotiation.media-types。* = ＃映射内容协商的媒体类型的文件扩展名。例如，yml到text / yaml。
spring.mvc.contentnegotiation.parameter-name =＃启用“favor-parameter”时要使用的查询参数名称。
spring.mvc.date-format = ＃要使用的日期格式。例如，`dd / MM / yyyy`。
spring.mvc.dispatch-trace-request = false ＃是否将TRACE请求分派给FrameworkServlet doService方法。
spring.mvc.dispatch-options-request = true ＃是否将OPTIONS请求分派给FrameworkServlet doService方法。
spring.mvc.favicon.enabled = true ＃是否启用favicon.ico的解析。
spring.mvc.formcontent.filter.enabled = true ＃是否启用Spring的FormContentFilter。
spring.mvc.hiddenmethod.filter.enabled = true＃是否启用Spring的HiddenHttpMethodFilter。
spring.mvc.ignore-default-model-on-redirect = true ＃在重定向场景中是否应忽略“默认”模型的内容。
spring.mvc.locale = ＃要使用的语言环境。默认情况下，“Accept-Language”标头会覆盖此区域设置。
spring.mvc.locale-resolver = accept-header ＃定义应如何解析语言环境。
spring.mvc.log-resolved-exception = false ＃是否启用由“HandlerExceptionResolver”解析的异常的警告日志记录，“DefaultHandlerExceptionResolver”除外。
spring.mvc.message-codes-resolver-format =＃格式化消息代码的策略。例如，`PREFIX_ERROR_CODE`。
spring.mvc.pathmatch.use-registered-suffix-pattern = false ＃后缀模式匹配是否仅适用于使用“spring.mvc.contentnegotiation.media-types。*”注册的扩展。
spring.mvc.pathmatch.use-suffix-pattern = false ＃将模式与请求匹配时是否使用后缀模式匹配（“。*”）。
spring.mvc.servlet.load-on-startup = -1 ＃加载调度程序servlet的启动优先级。
spring.mvc.servlet.path = / ＃调度程序servlet的路径。
spring.mvc.static-path-pattern = / ** ＃用于静态资源的路径模式。
spring.mvc.throw-exception-if-no-handler-found = false ＃如果没有找到Handler来处理请求，是否应该抛出“NoHandlerFoundException”。
spring.mvc.view.prefix = #Spring MVC视图前缀。
spring.mvc.view.suffix = #Spring MVC视图后缀。



##### #SPRING RESOURCES HANDLING（ResourceProperties）

spring.resources.add-mappings = true ＃是否启用默认资源处理。
spring.resources.cache.cachecontrol.cache-private = ＃表示响应消息仅供单个用户使用，不得由共享高速缓存存储。
spring.resources.cache.cachecontrol.cache-public = ＃表示任何缓存都可以存储响应。
spring.resources.cache.cachecontrol.max-age = ＃应该缓存响应的最长时间，如果未指定持续时间后缀，则以秒为单位。
spring.resources.cache.cachecontrol.must-revalidate =＃表示一旦它变得陈旧，缓存一定不能使用响应而不用服务器重新验证它。
spring.resources.cache.cachecontrol.no-cache = ＃表示只有在与服务器重新验证时才能重用缓存的响应。
spring.resources.cache.cachecontrol.no-store = ＃表示在任何情况下都不缓存响应。
spring.resources.cache.cachecontrol.no-transform = ＃表明他们不应该转换响应内容的中介（缓存和其他人）。
spring.resources.cache.cachecontrol.proxy-revalidate = ＃与“must-revalidate”指令的含义相同，但它不适用于私有缓存。
spring.resources.cache.cachecontrol.s-max-age = ＃共享缓存应缓存响应的最长时间，如果未指定持续时间后缀，则以秒为单位。
spring.resources.cache.cachecontrol.stale-if-error = ＃遇到错误时可以使用响应的最长时间，如果未指定持续时间后缀，则以秒为单位。
spring.resources.cache.cachecontrol.stale-while-revalidate = ＃响应变为失效后可以响应的最长时间，如果未指定持续时间后缀，则以秒为单位。spring.resources.cache.period = ＃资源处理程序所服务资源的缓存周期。如果未指定持续时间后缀，则将使用秒。
spring.resources.chain.cache= true ＃是否在资源链中启用缓存。
spring.resources.chain.compressed = false ＃是否启用已压缩资源的解析（gzip，brotli）。
spring.resources.chain.enabled = ＃是否启用Spring资源处理链。默认情况下，禁用，除非至少启用了一个策略。
spring.resources.chain.html-application-cache = false ＃是否启用HTML5应用程序缓存清单重写。
spring.resources.chain.strategy.content.enabled = false ＃是否启用内容版本策略。
spring.resources.chain.strategy.content.paths = / ** ＃逗号分隔的模式列表，应用于内容版本策略。
spring.resources.chain.strategy.fixed.enabled = false ＃是否启用固定版本策略。
spring.resources.chain.strategy.fixed.paths = / ** ＃以逗号分隔的模式列表应用于固定版本策略。
spring.resources.chain.strategy.fixed.version = ＃用于固定版本策略的版本字符串。
spring.resources.static-locations = classpath：/ META-INF / resources /，classpath：/ resources /，classpath：/ static /，classpath：/ public / ＃静态资源的位置。



##### #SPRING SESSION（SessionProperties）

spring.session.store-type = ＃会话存储类型。
spring.session.timeout = ＃会话超时。如果未指定持续时间后缀，则将使用秒。
spring.session.servlet.filter-order = -2147483598 ＃会话存储库过滤顺序。
spring.session.servlet.filter-dispatcher-types = async，error，request ＃会话存储库过滤器调度程序类型。



##### #SPRING SESSION HAZELCAST（HazelcastSessionProperties）

spring.session.hazelcast.flush-mode = on-save #sessions flush mode。
spring.session.hazelcast.map-name = spring：session：sessions ＃用于存储会话的地图的名称。



##### #SPRING SESSION JDBC（JdbcSessionProperties）

spring.session.jdbc.cleanup-cron = 0 * * * * * #cron 表达式用于过期的会话清理作业。
spring.session.jdbc.initialize-schema = embedded ＃数据库模式初始化模式。
spring.session.jdbc.schema = classpath中：组织/ springframework的/会话/ JDBC / schema- @ @ 平台@ @ .SQL ＃的路径SQL文件，以用于初始化数据库架构。
spring.session.jdbc.table-name = SPRING_SESSION ＃用于存储会话的数据库表的名称。



##### #SPRING SESSION MONGODB（MongoSessionProperties）

spring.session.mongodb.collection-name = sessions ＃用于存储会话的集合名称。



##### #SPRING SESSION REDIS（RedisSessionProperties）

spring.session.redis.cleanup-cron = 0 * * * * * #cron 表达式用于过期的会话清理作业。
spring.session.redis.flush-mode = on-save #sessions flush mode。
spring.session.redis.namespace = spring：session ＃用于存储会话的密钥的命名空间。



##### #THYMELEAF（ThymeleafAutoConfiguration）

spring.thymeleaf.cache = true ＃是否启用模板缓存。
spring.thymeleaf.check-template = true ＃是否在呈现模板之前检查模板是否存在。
spring.thymeleaf.check-template-location = true ＃是否检查模板位置是否存在。
spring.thymeleaf.enabled = true ＃是否为Web框架启用Thymeleaf视图解析。
spring.thymeleaf.enable-spring-el-compiler = false ＃在SpringEL表达式中启用SpringEL编译器。
spring.thymeleaf.encoding = UTF-8 ＃模板文件编码。
spring.thymeleaf.excluded-view-names = ＃逗号分隔的视图名称列表（允许的模式）应从分辨率中排除。
spring.thymeleaf.mode = HTML ＃要应用于模板的模板模式。另请参见Thymeleaf的TemplateMode枚举。
spring.thymeleaf.prefix = classpath：/ templates / ＃在构建URL时添加前缀以查看名称的前缀。
spring.thymeleaf.reactive.chunked-mode-view-names = ＃逗号分隔的视图名称列表（允许的模式），当设置了最大块大小时，它应该是在CHUNKED模式下执行的唯一列表。
spring.thymeleaf.reactive.full-mode-view-names =＃逗号分隔的视图名称列表（允许的模式），即使设置了最大块大小，也应该在FULL模式下执行。
spring.thymeleaf.reactive.max-chunk-size = 0B ＃用于写入响应的数据缓冲区的最大大小。
spring.thymeleaf.reactive.media-types = ＃视图技术支持的媒体类型。
spring.thymeleaf.render-hidden-markers-before-checkboxes = false ＃是否应在复选框元素本身之前呈现隐藏的表单输入作为复选框的标记。
spring.thymeleaf.servlet.content-type = text / html ＃Content-Type写入HTTP响应的值。
spring.thymeleaf.servlet.produce-partial-output-while-processing = true#Thymeleaf是否应尽快开始编写部分输出或缓冲直到模板处理完成。
spring.thymeleaf.suffix = .html ＃在构建URL时附加到视图名称的后缀。
spring.thymeleaf.template-resolver-order = ＃链中模板解析器的顺序。
spring.thymeleaf.view-names = ＃逗号分隔的视图名称列表（允许的模式），可以解析。



##### #SPRING WEBFLUX（WebFluxProperties）

spring.webflux.date-format = ＃要使用的日期格式。例如，`dd / MM / yyyy`。
spring.webflux.hiddenmethod.filter.enabled = true ＃是否启用Spring的HiddenHttpMethodFilter。
spring.webflux.static-path-pattern = / ** ＃用于静态资源的路径模式。

##### #SPRING WEB SERVICES（WebServicesProperties）

spring.webservices.path = / services ＃作为服务基URI的路径。
spring.webservices.servlet.init = ＃Servlet init参数传递给Spring Web Services。
spring.webservices.servlet.load-on-startup = -1 ＃加载Spring Web Services servlet的启动优先级。
spring.webservices.wsdl-locations = ＃逗号分隔的WSDL位置列表以及要作为bean公开的随附XSD。



＃---------------------------------------- 

#### ＃SECURITY PROPERTIES 

＃----- ----------------------------------- 

##### ＃SECURITY（SecurityProperties）

spring.security.filter.order = -100 ＃安全过滤器链顺序。
spring.security.filter.dispatcher-types = async，error，request ＃安全过滤器链调度程序类型。
spring.security.user.name = user ＃默认用户名。
spring.security.user.password = ＃默认用户名的密码。
spring.security.user.roles = ＃授予默认用户名的角色。



##### ＃SECURITY OAUTH2 CLIENT（OAuth2ClientProperties）

spring.security.oauth2.client.provider。* = ＃OAuth提供商详细信息。
spring.security.oauth2.client.registration。* = ＃OAuth客户注册。



##### ＃SECURITY OAUTH2 RESOURCE SERVER（OAuth2ResourceServerProperties）

 spring.security.oauth2.resourceserver.jwt.jwk-set-uri = ＃JSON用于验证JWT令牌的Web Key URI。
 spring.security.oauth2.resourceserver.jwt.issuer-uri = OpenID Connect Provider声明为其颁发者标识符的URI。



＃---------------------------------------- 

#### ＃DATA PROPERTIES 

＃----- -----------------------------------

##### #FLYWAY （FlywayProperties）

spring.flyway.baseline-description = << Flyway Baseline >> ＃描述在应用基线时标记现有模式。
spring.flyway.baseline-on-migrate = false ＃是否在迁移非空架构时自动调用基线。
spring.flyway.baseline-version = 1 ＃用于在执行基线时标记现有模式的版本。
spring.flyway.check-location = true ＃是否检查迁移脚本位置是否存在。
spring.flyway.clean-disabled = false ＃是否禁用数据库清理。
spring.flyway.clean-on-validation-error = false＃发生验证错误时是否自动调用clean。
spring.flyway.connect-retries = 0 ＃尝试连接数据库时的最大重试次数。
spring.flyway.enabled = true ＃是否启用flyway。
spring.flyway.encoding = UTF-8 #SQL 迁移的编码。
spring.flyway.group = false ＃是否在应用它们时在同一事务中将所有挂起的迁移组合在一起。
spring.flyway.ignore-future-migrations = true ＃在读取架构历史记录表时是否忽略未来的迁移。
spring.flyway.ignore-ignored-migrations = false＃是否在读取模式历史记录表时忽略忽略的迁移。
spring.flyway.ignore-missing-migrations = false ＃是否在读取模式历史记录表时忽略缺少的迁移。
spring.flyway.ignore-pending-migrations = false ＃在读取架构历史记录表时是否忽略挂起的迁移。
spring.flyway.init-sqls = ＃在获取连接后立即执行以初始化连接的SQL语句。
spring.flyway.installed-by = ＃用户名在架构历史记录表中记录为已应用迁移。
spring.flyway.locations = classpath：db / migration＃迁移脚本的位置。可以包含特殊的“{vendor}”占位符以使用特定于供应商的位置。
spring.flyway.mixed = false ＃是否允许在同一迁移中混合事务和非事务语句。
spring.flyway.out-of-order = false ＃是否允许迁移无序运行。
spring.flyway.password = ＃要迁移的数据库的登录密码。
spring.flyway.placeholder-prefix = $ { ＃迁移脚本中占位符的前缀。
spring.flyway.placeholder-replacement = true ＃在迁移脚本中执行占位符替换。
spring.flyway.placeholder-suffix =}＃迁移脚本中占位符的后缀。
spring.flyway.placeholders = ＃占位符及其替换应用于sql迁移脚本。
spring.flyway.repeatable-sql-migration-prefix = R ＃可重复SQL迁移的文件名前缀。
spring.flyway.schemas = ＃由
Flyway 管理的方案名称（区分大小写）。spring.flyway.skip-default-callbacks = false ＃是否跳过默认回调。如果为true，则仅使用自定义回调。
spring.flyway.skip-default-resolvers = false ＃是否跳过默认的解析器。如果为true，则仅使用自定义解析程序。
spring.flyway.sql-migration-prefix = V.#SQL迁移的文件名前缀。
spring.flyway.sql-migration-separator = __ #SQL迁移的文件名分隔符。
spring.flyway.sql-migration-suffixes = .sql #SQL迁移的文件名后缀。
spring.flyway.table = flyway_schema_history ＃
将由 Flyway 使用的架构架构历史记录表的名称。spring.flyway.target = ＃应考虑迁移的目标版本。
spring.flyway.url = 要迁移的数据库的JDBC url。如果未设置，则使用主要配置的数据源。
spring.flyway.user = ＃要迁移的数据库的登录用户。
spring.flyway.validate-on-migrate = true ＃是否在执行迁移时自动调用validate。



##### #LIQUIBASE（LiquibaseProperties）

spring.liquibase.change-log = classpath：/db/changelog/db.changelog-master.yaml# 更改日志配置路径。
spring.liquibase.check-change-log-location = true ＃是否检查更改日志位置是否存在。
spring.liquibase.contexts = ＃逗号分隔的运行时上下文列表。
spring.liquibase.database-change-log-lock-table = DATABASECHANGELOGLOCK ＃用于跟踪并发Liquibase用法的表的名称。

spring.liquibase.database-change-log-table = DATABASECHANGELOG ＃用于跟踪更改历史记录的表的名称。

spring.liquibase.default-模式= ＃默认数据库架构。
spring.liquibase.drop-first = false ＃是否首先删除数据库模式。
spring.liquibase.enabled = true ＃是否启用Liquibase支持。
spring.liquibase.labels = ＃逗号分隔的运行时标签列表。
spring.liquibase.liquibase-schema = #Schema用于Liquibase对象。
spring.liquibase.liquibase-tablespace = ＃用于Liquibase对象的表空间。
spring.liquibase.parameters。* = ＃更改日志参数。
spring.liquibase.password = ＃要迁移的数据库的登录密码。
spring.liquibase.rollback-file = ＃执行更新时写入回滚SQL的文件。
spring.liquibase.test-rollback-on-update = false ＃是否应在执行更新之前测试回滚。
spring.liquibase.url = ＃JDBC要迁移的数据库的URL。如果未设置，则使用主要配置的数据源。
spring.liquibase.user = ＃要迁移的数据库的登录用户。



##### ＃COUCHBASE（CouchbaseProperties）

spring.couchbase.bootstrap-hosts = #Couchbase 节点（主机或IP地址）来自引导程序。
spring.couchbase.bucket.name = default ＃要连接的存储桶的名称。
spring.couchbase.bucket.password =   ＃桶的密码。
spring.couchbase.env.endpoints.key-value = 1 ＃针对键/值服务的每个节点的套接字数。
spring.couchbase.env.endpoints.queryservice.min-endpoints = 1 ＃每个节点的最小套接字数。
spring.couchbase.env.endpoints.queryservice.max-endpoints = 1 ＃每个节点的最大套接字数。
spring.couchbase.env.endpoints.viewservice.min-endpoints = 1 ＃每个节点的最小套接字数。
spring.couchbase.env.endpoints.viewservice.max-endpoints = 1 ＃每个节点的最大套接字数。
spring.couchbase.env.ssl.enabled = ＃是否启用SSL支持。除非另有说明，否则在提供“keyStore”时自动启用。
spring.couchbase.env.ssl.key-store = ＃保存证书的JVM密钥库的路径。
spring.couchbase.env.ssl.key-store-password = ＃用于访问密钥库的密码。
spring.couchbase.env.timeouts.connect = 5000ms ＃桶连接超时。
spring.couchbase.env.timeouts.key-value = 2500ms ＃对特定密钥超时执行的阻止操作。
spring.couchbase.env.timeouts.query = 7500ms ＃N1QL查询操作超时。
spring.couchbase.env.timeouts.socket-connect = 1000ms #Socket 连接超时。
spring.couchbase.env.timeouts.view = 7500ms ＃常规和地理空间视图操作超时。



##### #DAO （PersistenceExceptionTranslationAutoConfiguration）

spring.dao.exceptiontranslation.enabled = true ＃是否启用PersistenceExceptionTranslationPostProcessor。



##### #CASSANDRA （CassandraProperties）

spring.data.cassandra.cluster-name = #Cassandra 集群的名称。
spring.data.cassandra.compression = none ＃Cassandra二进制协议支持的压缩。
spring.data.cassandra.connect-timeout = #Socket 选项：连接超时。
spring.data.cassandra.consistency-level = ＃查询一致性级别。
spring.data.cassandra.contact-points = localhost ＃群集节点地址。
spring.data.cassandra.fetch-size = ＃查询默认提取大小。
spring.data.cassandra.jmx-enabled = false＃是否启用JMX报告。
spring.data.cassandra.keyspace-name = ＃要使用的Keyspace名称。
spring.data.cassandra.port = #Cassandra 服务器的端口。
spring.data.cassandra.password = ＃服务器的登录密码。
spring.data.cassandra.pool.heartbeat-interval = 30s #Heartbeat interval，在此之后，在空闲连接上发送消息以确保它仍然存在。如果未指定持续时间后缀，则将使用秒。
spring.data.cassandra.pool.idle-timeout = 120s ＃删除空闲连接之前的空闲超时。如果未指定持续时间后缀，则将使用秒。
spring.data.cassandra.pool.max队列大小= 256 ＃如果没有可用连接，则排队的最大请求数。
spring.data.cassandra.pool.pool-timeout = 5000ms ＃尝试从主机池获取连接时的池超时。
spring.data.cassandra.read-timeout = #Socket 选项：读取超时。
spring.data.cassandra.repositories.type = auto ＃要启用的Cassandra存储库的类型。
spring.data.cassandra.serial-consistency-level = ＃查询串行一致性级别。
spring.data.cassandra.schema-action = none ＃启动时要采取的架构操作。
spring.data.cassandra.ssl = false ＃启用SSL支持。
spring.data.cassandra.username = ＃服务器的登录用户。



##### #DATA COUCHBASE（CouchbaseDataProperties）

spring.data.couchbase.auto-index = false ＃自动创建视图和索引。
spring.data.couchbase.consistency = read-your-own-writes ＃在生成的查询中默认应用的一致性。
spring.data.couchbase.repositories.type = auto ＃要启用的Couchbase存储库的类型。



##### #ELASTICSEARCH（ElasticsearchProperties）

spring.data.elasticsearch.cluster-name = elasticsearch ＃Elasticsearch集群名称。
spring.data.elasticsearch.cluster-nodes = ＃逗号分隔的集群节点地址列表。
spring.data.elasticsearch.properties。* = ＃用于配置客户端的其他属性。
spring.data.elasticsearch.repositories.enabled = true ＃是否启用Elasticsearch存储库。



##### #DATA JDBC 

spring.data.jdbc.repositories.enabled = true ＃是否启用JDBC存储库。



##### #DATA LDAP

spring.data.ldap.repositories.enabled = true ＃是否启用LDAP存储库。



##### #MONGODB（MongoProperties）

spring.data.mongodb.authentication-database = ＃认证数据库名称。
spring.data.mongodb.database = ＃数据库名称。
spring.data.mongodb.field-naming-strategy = ＃要使用的FieldNamingStrategy的完全限定名称。
spring.data.mongodb.grid-fs-database = ＃GridFS数据库名称。
spring.data.mongodb.host = #Mongo 服务器主机。无法使用URI设置。
spring.data.mongodb.password = #mongo 服务器的登录密码。无法使用URI设置。
spring.data.mongodb.port = #Mongo 服务器端口。无法使用URI设置。
spring.data.mongodb.repositories.type = auto ＃要启用的Mongo存储库的类型。
spring.data.mongodb.uri = mongodb：// localhost / test ＃Mongo数据库URI。无法使用主机，端口和凭据进行设置。
spring.data.mongodb.username = #mongo 服务器的登录用户。无法使用URI设置。



##### #DATA REDIS 

spring.data.redis.repositories.enabled = true ＃是否启用Redis存储库。



##### ＃NEO4J（Neo4jProperties）

spring.data.neo4j.auto-index = none ＃自动索引模式。
spring.data.neo4j.embedded.enabled = true ＃如果嵌入式驱动程序可用，是否启用嵌入模式。
spring.data.neo4j.open-in-view = true ＃注册OpenSessionInViewInterceptor。将Neo4j会话绑定到线程以进行整个请求处理。
spring.data.neo4j.password = ＃服务器的登录密码。
spring.data.neo4j.repositories.enabled = true ＃是否启用Neo4j存储库。
spring.data.neo4j.uri = 驱动程序使用的#URL 。默认情况下自动检测。
spring.data.neo4j.username = ＃服务器的登录用户。



##### #DATA REST（RepositoryRestProperties）

spring.data.rest.base-path = #Spring Data REST用于公开存储库资源的基本路径。
spring.data.rest.default-media-type = ＃未指定时用作默认值的内容类型。
spring.data.rest.default-page-size = ＃默认页面大小。
spring.data.rest.detection-strategy = default ＃用于确定暴露哪些存储库的策略。
spring.data.rest.enable-enum-translation = ＃是否通过Spring Data REST默认资源包启用枚举值转换。
spring.data.rest.limit-param-name =#URL查询字符串参数的名称，指示一次返回多少结果。
spring.data.rest.max-page-size = ＃最大页面大小。
spring.data.rest.page-param-name = #URL 查询字符串参数的名称，指示要返回的页面。
spring.data.rest.return-body-on-create = ＃是否在创建实体后返回响应正文。
spring.data.rest.return-body-on-update = ＃更新实体后是否返回响应正文。
spring.data.rest.sort-param-name = #URL 查询字符串参数的名称，指示对结果进行排序的方向。



##### #SOLR （SolrProperties）

spring.data.solr.host = http：//127.0.0.1：8983 / solr #Solr host。如果设置了“zk-host”，则忽略。
spring.data.solr.repositories.enabled = true ＃是否启用Solr存储库。
spring.data.solr.zk-host = ＃ZooKeeper主机地址，格式为HOST：PORT。



##### ＃DATA WEB（SpringDataWebProperties）

spring.data.web.pageable.default页大小 = 20 ＃缺省页大小。
spring.data.web.pageable.max-page-size = 2000 ＃要接受的最大页面大小。
spring.data.web.pageable.one-indexed-parameters = false ＃是否公开和假设从1开始的页码索引。
spring.data.web.pageable.page-parameter = page ＃页面索引参数名称。
spring.data.web.pageable.prefix = ＃常用前缀，用于页码和页面大小参数。

spring.data.web.pageable.qualifier-delimiter = _＃限定符与实际页码和大小属性之间使用的分隔符。
spring.data.web.pageable.size-parameter = size ＃页面大小参数名称。
spring.data.web.sort.sort-parameter = sort ＃排序参数名称。



##### #DATASOURCE （DataSourceAutoConfiguration＆DataSourceProperties）

spring.datasource.continue-on-error = false ＃初始化数据库时是否发生错误时停止。
spring.datasource.data = #Data （DML）脚本资源引用。
spring.datasource.data-username = ＃用于执行DML脚本的数据库的用户名（如果不同）。

spring.datasource.data-password = ＃执行DML脚本的数据库的密码（如果不同）。
spring.datasource.dbcp2。* = ＃Commons DBCP2特定设置
spring.datasource.driver-class-name =#JDBC驱动程序的完全限定名称。默认情况下，基于URL自动检测。
spring.datasource.generate-unique-name = false ＃是否生成随机数据源名称。
spring.datasource.hikari。* = ＃Hikari特定设置
spring.datasource.initialization-mode = embedded ＃使用可用的DDL和DML脚本初始化数据源。
spring.datasource.jmx-enabled = false ＃是否启用JMX支持（如果由基础池提供）。
spring.datasource.jndi-name = ＃JNDI数据源的位置。设置时将忽略类，URL，用户名和密码。
spring.datasource.name =＃数据源的名称。使用嵌入式数据库时默认为“testdb”。
spring.datasource.password = ＃数据库的登录密码。
spring.datasource.platform = all ＃在DDL或DML脚本中使用的平台（例如架构 -  $ {platform} .sql或data  -  $ {platform} .sql）。
spring.datasource.schema = #Schema （DDL）脚本资源引用。
spring.datasource.schema-username = ＃执行DDL脚本的数据库的用户名（如果不同）。

spring.datasource.schema-password = ＃执行DDL脚本的数据库的密码（如果不同）。
spring.datasource.separator =;#SQL初始化脚本中的语句分隔符。
spring.datasource.sql-script-encoding = #SQL 脚本编码。
spring.datasource.tomcat。* = ＃Tomcat数据源特定设置
spring.datasource.type = ＃要使用的连接池实现的完全限定名称。默认情况下，它是从类路径中自动检测到的。
spring.datasource.url = ＃JDBC数据库的URL。
spring.datasource.username = ＃登录数据库的用户名。
spring.datasource.xa.data-source-class-name = #XA 数据源完全限定名称。
spring.datasource.xa.properties =＃传递给XA数据源的属性。



##### #JEST （Elasticsearch HTTP客户端）（JestProperties）

spring.elasticsearch.jest.connection-timeout = 3s ＃连接超时。
spring.elasticsearch.jest.multi-threaded = true ＃是否从多个执行线程启用连接请求。
spring.elasticsearch.jest.password = ＃登录密码。
spring.elasticsearch.jest.proxy.host = #HTTP 客户端应使用的代理主机。
spring.elasticsearch.jest.proxy.port = #HTTP 客户端应使用的代理端口。
spring.elasticsearch.jest.read-timeout = 3s ＃读取超时。
spring.elasticsearch.jest.uris = http：// localhost：9200＃逗号分隔的Elasticsearch实例列表。
spring.elasticsearch.jest.username = ＃登录用户名。



##### #Elasticsearch REST客户端（RestClientProperties）

spring.elasticsearch.rest.password = ＃凭据密码。
spring.elasticsearch.rest.uris = http：// localhost：9200 ＃要使用的以逗号分隔的Elasticsearch实例列表。
spring.elasticsearch.rest.username = ＃凭据用户名。



##### ＃H2 Web控制台（H2ConsoleProperties）

spring.h2.console.enabled = false ＃是否启用控制台。
spring.h2.console.path = / h2-console ＃控制台可用的路径。
spring.h2.console.settings.trace = false ＃是否启用跟踪输出。
spring.h2.console.settings.web-allow-others = false ＃是否启用远程访问。



##### ＃InfluxDB（InfluxDbProperties）

spring.influx.password = ＃登录密码。
spring.influx.url = 要连接的InfluxDB实例的URL。
spring.influx.user = ＃登录用户。



##### ＃JOOQ （JooqProperties）

spring.jooq.sql-dialect = #SQL 方言使用。默认情况下自动检测。



##### #JDBC （JdbcProperties）

spring.jdbc.template.fetch-size = -1 ＃需要更多行时应从数据库中提取的行数。
spring.jdbc.template.max-rows = -1 ＃最大行数。
spring.jdbc.template.query-timeout = ＃查询超时。默认是使用JDBC驱动程序的默认配置。如果未指定持续时间后缀，则将使用秒。



##### ＃JPA （JpaBaseConfiguration，HibernateJpaAutoConfiguration）

spring.data.jpa.repositories.bootstrap-mode = default #JAPA 存储库的Bootstrap模式。
spring.data.jpa.repositories.enabled = true ＃是否启用JPA存储库。
spring.jpa.database = ＃要操作的目标数据库，默认情况下自动检测。也可以使用“databasePlatform”属性进行设置。
spring.jpa.database-platform = ＃要操作的目标数据库的名称，默认情况下自动检测。也可以使用“Database”枚举来设置。
spring.jpa.generate-ddl = false ＃是否在启动时初始化架构。
spring.jpa.hibernate.ddl-auto = ＃DDL模式。这实际上是“hibernate.hbm2ddl.auto”属性的快捷方式。使用嵌入式数据库时未默认为“create-drop”，并且未检测到架构管理器。否则，默认为“none”。
spring.jpa.hibernate.naming.implicit-strategy = ＃隐式命名策略的完全限定名称。
spring.jpa.hibernate.naming.physical-strategy = ＃物理命名策略的完全限定名称。
spring.jpa.hibernate.use-new-id-generator-mappings = ＃是否将Hibernate的较新的IdentifierGenerator用于AUTO，TABLE和SEQUENCE。
spring.jpa.mapping-resources =＃Mapping资源（相当于persistence.xml中的“mapping-file”条目）。
spring.jpa.open-in-view = true ＃注册OpenEntityManagerInViewInterceptor。将JPA EntityManager绑定到线程以进行整个请求处理。
spring.jpa.properties。* = ＃要在JPA提供程序上设置的其他本机属性。
spring.jpa.show -sql = false ＃是否启用SQL语句的日志记录。



##### #JTA （JtaAutoConfiguration）

spring.jta.enabled = true ＃是否启用JTA支持。
spring.jta.log-dir = #Transaction logs目录。
spring.jta.transaction-manager-id = #Transaction manager唯一标识符。



##### #ATOMIKOS（AtomikosProperties）

spring.jta.atomikos.connectionfactory.borrow-connection-timeout = 30 ＃从池中借用连接的超时（以秒为单位）。
spring.jta.atomikos.connectionfactory.ignore-session-transacted-flag = true ＃是否在创建会话时忽略事务处理标志。
spring.jta.atomikos.connectionfactory.local-transaction-mode = false ＃是否需要本地事务。
spring.jta.atomikos.connectionfactory.maintenance-interval = 60 ＃池维护线程运行之间的时间（以秒为单位）。
spring.jta.atomikos.connectionfactory.max-idle-time = 60＃从池中清除连接的时间（以秒为单位）。
spring.jta.atomikos.connectionfactory.max-lifetime = 0 ＃连接在被销毁之前可以合并的时间（以秒为单位）。0表示没有限制。
spring.jta.atomikos.connectionfactory.max-pool-size = 1 ＃池的最大大小。
spring.jta.atomikos.connectionfactory.min-pool-size = 1 ＃池的最小大小。
spring.jta.atomikos.connectionfactory.reap-timeout = 0 ＃借用连接的reap超时（以秒为单位）。0表示没有限制。
spring.jta.atomikos.connectionfactory.unique-resource-name = jmsConnectionFactory＃用于在恢复期间标识资源的唯一名称。
spring.jta.atomikos.connectionfactory.xa-connection-factory-class-name = #XAConnectionFactory的供应商特定实现。
spring.jta.atomikos.connectionfactory.xa-properties = ＃供应商特定的XA属性。
spring.jta.atomikos.datasource.borrow-connection-timeout = 30 ＃从池中借用连接的超时时间（秒）。
spring.jta.atomikos.datasource.concurrent-connection-validation = ＃是否使用并发连接验证。
spring.jta.atomikos.datasource.default-isolation-level = ＃池提供的连接的默认隔离级别。
spring.jta.atomikos.datasource.login-timeout = ＃用于建立数据库连接的超时（以秒为单位）。
spring.jta.atomikos.datasource.maintenance-interval = 60 ＃池维护线程运行之间的时间（以秒为单位）。
spring.jta.atomikos.datasource.max-idle-time = 60 ＃从池中清除连接的时间（以秒为单位）。
spring.jta.atomikos.datasource.max-lifetime = 0 ＃连接在被销毁之前可以合并的时间（以秒为单位）。0表示没有限制。
spring.jta.atomikos.datasource.max-pool-size = 1 ＃池的最大大小。
spring.jta.atomikos.datasource.min-pool-size = 1＃池的最小大小。
spring.jta.atomikos.datasource.reap-timeout = 0 ＃借用连接的reap超时（以秒为单位）。0表示没有限制。
spring.jta.atomikos.datasource.test-query = ＃用于在返回连接之前验证连接的SQL查询或语句。
spring.jta.atomikos.datasource.unique-resource-name = dataSource ＃用于在恢复期间标识资源的唯一名称。
spring.jta.atomikos.datasource.xa-data-source-class-name = #XAConnectionFactory的供应商特定实现。
spring.jta.atomikos.datasource.xa-properties = ＃供应商特定的XA属性。
spring.jta.atomikos.properties.allow-sub-transactions = true ＃指定是否允许子事务。
spring.jta.atomikos.properties.checkpoint-interval = 500 ＃检查点之间的间隔，表示为两个检查点之间的日志写入次数。
spring.jta.atomikos.properties.default -jta -timeout = 10000ms #JTA 事务的默认超时。
spring.jta.atomikos.properties.default-max-wait-time-on-shutdown = 9223372036854775807 ＃正常关闭（no-force）等待事务完成的时间。
spring.jta.atomikos.properties.enable-logging = true ＃是否启用磁盘日志记录。
spring.jta.atomikos.properties.force-shutdown-on-vm-exit = false ＃虚拟机关闭是否应触发事务核心的强制关闭。
spring.jta.atomikos.properties.log-base-dir = ＃应存储日志文件的目录。
spring.jta.atomikos.properties.log-base-name = tmlog ＃Transactions日志文件基名。
spring.jta.atomikos.properties.max-actives = 50 ＃最大活动事务数。
spring.jta.atomikos.properties.max-timeout = 300000ms ＃事务允许的最大超时时间。
spring.jta.atomikos.properties.recovery.delay = 10000ms ＃两次恢复扫描之间的延迟。
spring.jta.atomikos.properties.recovery.forget- orphaned -log-entries-delay = 86400000ms ＃恢复之后的延迟可以清除挂起（'孤立'）日志条目。
spring.jta.atomikos.properties.recovery.max-retries = 5 ＃在抛出异常之前提交事务的重试次数。
spring.jta.atomikos.properties.recovery.retry-interval = 10000ms ＃重试尝试之间的延迟。
spring.jta.atomikos.properties.serial-jta-transactions = true ＃是否应尽可能加入子事务。
spring.jta.atomikos.properties.service = ＃应该启动的事务管理器实现。
spring.jta.atomikos.properties.threaded-two-phase-commit = false ＃是否对参与资源使用不同（和并发）线程进行两阶段提交。
spring.jta.atomikos.properties.transaction-manager-unique-name = ＃事务管理器的唯一名称。



##### ＃BITRONIX 

spring.jta.bitronix.connectionfactory.acquire-increment = 1 ＃在增长池时创建的连接数。
spring.jta.bitronix.connectionfactory.acquisition-interval = 1 ＃获取无效连接后再次尝试获取连接之前等待的时间（以秒为单位）。
spring.jta.bitronix.connectionfactory.acquisition-timeout = 30 ＃从池中获取连接的超时时间（秒）。
spring.jta.bitronix.connectionfactory.allow-local-transactions = true ＃事务管理器是否应该允许混合XA和非XA事务。
spring.jta.bitronix.connectionfactory.apply-transaction-timeout = false＃是否应在登记时在XAResource上设置事务超时。
spring.jta.bitronix.connectionfactory.automatic-enlisting-enabled = true ＃是否应自动登记和退出资源。
spring.jta.bitronix.connectionfactory.cache-producer-consumers = true ＃是否应该缓存生产者和消费者。
spring.jta.bitronix.connectionfactory.class-name = #XA 资源的底层实现类名。
spring.jta.bitronix.connectionfactory.defer-connection-release = true ＃提供程序是否可以在同一连接上运行多个事务并支持事务交错。
spring.jta.bitronix.connectionfactory.disabled= ＃是否禁用此资源，这意味着暂时禁止从其池中获取连接。
spring.jta.bitronix.connectionfactory.driver-properties = ＃应在底层实现上设置的属性。
spring.jta.bitronix.connectionfactory.failed = ＃将此资源生成器标记为失败。
spring.jta.bitronix.connectionfactory.ignore-recovery-failures = false ＃是否应忽略恢复失败。
spring.jta.bitronix.connectionfactory.max-idle-time = 60 ＃从池中清除连接的时间（以秒为单位）。
spring.jta.bitronix.connectionfactory.max-pool-size = 10＃池的最大大小。0表示没有限制。
spring.jta.bitronix.connectionfactory.min-pool-size = 0 ＃池的最小大小。
spring.jta.bitronix.connectionfactory.password = ＃用于连接JMS提供程序的密码。
spring.jta.bitronix.connectionfactory.share-transaction-connections = false ＃是否可以在事务上下文中共享处于ACCESSIBLE状态的连接。
spring.jta.bitronix.connectionfactory.test-connections = true ＃从池中获取时是否应测试连接。
spring.jta.bitronix.connectionfactory.two-pc-ordering-position = 1＃在两阶段提交期间此资源应采取的位置（始终是第一个是Integer.MIN_VALUE，总是最后一个是Integer.MAX_VALUE）。
spring.jta.bitronix.connectionfactory.unique-name = jmsConnectionFactory ＃用于在恢复期间标识资源的唯一名称。
spring.jta.bitronix.connectionfactory.use -tm -join = true ＃启动XAResources时是否应该使用TMJOIN。

spring.jta.bitronix.connectionfactory.user = ＃用于连接到JMS提供程序的用户。
spring.jta.bitronix.datasource.acquire-increment = 1 ＃在增长池时创建的连接数。
spring.jta.bitronix.datasource.acquisition-interval = 1＃在获取无效连接后再次尝试获取连接之前等待的时间（以秒为单位）。
spring.jta.bitronix.datasource.acquisition-timeout = 30 ＃从池中获取连接的超时时间（秒）。
spring.jta.bitronix.datasource.allow-local-transactions = true ＃事务管理器是否应该允许混合XA和非XA事务。
spring.jta.bitronix.datasource.apply-transaction-timeout = false ＃是否应在XAResource登记时设置事务超时。
spring.jta.bitronix.datasource.automatic-enlisting-enabled = true ＃是否应自动登记和退出资源。
spring.jta.bitronix.datasource.class-name = #XA 资源的底层实现类名。
spring.jta.bitronix.datasource.cursor-holdability = ＃连接的默认光标可保持性。
spring.jta.bitronix.datasource.defer-connection-release = true ＃数据库是否可以在同一连接上运行多个事务并支持事务交错。
spring.jta.bitronix.datasource.disabled = ＃是否禁用此资源，这意味着暂时禁止从其池中获取连接。
spring.jta.bitronix.datasource.driver-properties = ＃应在底层实现上设置的属性。
spring.jta.bitronix.datasource.enable -jdbc4-connection-test = ＃从池中获取连接时是否调用Connection.isValid（）。
spring.jta.bitronix.datasource.failed = ＃将此资源生成器标记为失败。
spring.jta.bitronix.datasource.ignore-recovery-failures = false ＃是否应忽略恢复失败。
spring.jta.bitronix.datasource.isolation-level = ＃连接的默认隔离级别。
spring.jta.bitronix.datasource.local-auto-commit = ＃本地事务的默认自动提交模式。
spring.jta.bitronix.datasource.login-timeout =＃建立数据库连接的超时时间（秒）。
spring.jta.bitronix.datasource.max-idle-time = 60 ＃从池中清除连接的时间（以秒为单位）。
spring.jta.bitronix.datasource.max-pool-size = 10 ＃池的最大大小。0表示没有限制。
spring.jta.bitronix.datasource.min-pool-size = 0 ＃池的最小大小。
spring.jta.bitronix.datasource.prepared-statement-cache-size = 0 ＃预准备语句缓存的目标大小。0禁用缓存。

spring.jta.bitronix.datasource.share-transaction-connections = false＃是否可以在事务上下文中共享处于ACCESSIBLE状态的连接。
spring.jta.bitronix.datasource.test-query = ＃用于在返回连接之前验证连接的SQL查询或语句。
spring.jta.bitronix.datasource.two-pc-ordering-position = 1 ＃此资源在两阶段提交期间应采取的位置（始终首先是Integer.MIN_VALUE，并且始终是最后一个是Integer.MAX_VALUE）。
spring.jta.bitronix.datasource.unique-name = dataSource ＃用于在恢复期间标识资源的唯一名称。
spring.jta.bitronix.datasource.use -tm -join = true ＃启动XAResources时是否应该使用TMJOIN。
spring.jta.bitronix.properties.allow-multiple-lrc = false ＃是否允许多个LRC资源登记到同一事务中。
spring.jta.bitronix.properties.asynchronous2-pc = false ＃是否启用异步执行两阶段提交。
spring.jta.bitronix.properties.background-recovery-interval-seconds = 60 ＃在后台运行恢复过程的时间间隔（以秒为单位）。
spring.jta.bitronix.properties.current-node-only-recovery = true ＃是否仅恢复当前节点。
spring.jta.bitronix.properties.debug-zero-resource-transaction = false＃是否记录创建和提交没有单个登记资源的事务调用堆栈。
spring.jta.bitronix.properties.default-transaction-timeout = 60 ＃默认事务超时，以秒为单位。
spring.jta.bitronix.properties.disable-jmx = false ＃是否启用JMX支持。
spring.jta.bitronix.properties.exception-analyzer = ＃设置要使用的异常分析器实现的完全限定名称。
spring.jta.bitronix.properties.filter-log-status = false ＃是否启用日志过滤，以便只写入强制日志。
spring.jta.bitronix.properties.force-batching-enabled = true＃是否批量磁盘强制。
spring.jta.bitronix.properties.forced-write-enabled = true ＃是否强制将日志记录到磁盘。
spring.jta.bitronix.properties.graceful-shutdown-interval = 60 ＃TM在关闭时中止事务之前等待事务完成的最大秒数。
spring.jta.bitronix.properties.jndi-transaction-synchronization-registry-name = ＃TransactionSynchronizationRegistry的JNDI名称。
spring.jta.bitronix.properties.jndi-user-transaction-name = ＃UserTransaction的JNDI名称。
spring.jta.bitronix.properties.journal = disk ＃期刊的名称。可以是'disk'，'null'或类名。
spring.jta.bitronix.properties.log-part1-filename = btm1.tlog ＃日志的第一个片段的名称。
spring.jta.bitronix.properties.log-part2-filename = btm2.tlog ＃日志的第二个片段的名称。
spring.jta.bitronix.properties.max-log-size-in-mb = 2 ＃日志片段的最大大小（兆字节）。
spring.jta.bitronix.properties.resource-configuration-filename = ＃ResourceLoader配置文件名。
spring.jta.bitronix.properties.server-id = #ASCII ID必须唯一标识此TM实例。默认为机器的IP地址。
spring.jta.bitronix.properties.skip-corrupted-logs = false#Skip损坏的事务日志条目。
spring.jta.bitronix.properties.warn-about-zero-resource-transaction = true ＃是否为没有单个登记资源的事务记录警告。



##### ＃EMBEDDED MONGODB（EmbeddedMongoProperties）

spring.mongodb.embedded.features = sync_delay ＃逗号分隔的要启用的功能列表。
spring.mongodb.embedded.storage.database-dir = ＃用于数据存储的目录。
spring.mongodb.embedded.storage.oplog-size = #oplog的最大大小。
spring.mongodb.embedded.storage.repl-set-name = ＃副本集的名称。
spring.mongodb.embedded.version = 3.5.5 ＃要使用的Mongo版本。



##### #REDIS（RedisProperties）

spring.redis.cluster.max -redirects = ＃在群集中执行命令时要遵循的最大重定向数。
spring.redis.cluster.nodes = ＃逗号分隔的“host：port”对列表引导自。
spring.redis.database = 0 ＃连接工厂使用的数据库索引。
spring.redis.url = ＃连接URL。覆盖主机，端口和密码。用户被忽略。示例：redis：// user：password@example.com ：6379 
spring.redis.host = localhost ＃Redis服务器主机。
spring.redis.jedis.pool.max-active = 8＃池在给定时间可以分配的最大连接数。使用负值无限制。
spring.redis.jedis.pool.max-idle = 8 ＃池中“空闲”连接的最大数量。使用负值表示无限数量的空闲连接。
spring.redis.jedis.pool.max -wait = -1ms ＃在池耗尽时，在抛出异常之前连接分配应该阻塞的最长时间。使用负值无限期阻止。
spring.redis.jedis.pool.min-idle = 0 ＃目标是池中维护的最小空闲连接数。此设置仅在其为正时才有效。
spring.redis.lettuce.pool.max-active = 8＃池在给定时间可以分配的最大连接数。使用负值无限制。
spring.redis.lettuce.pool.max-idle = 8 ＃池中“空闲”连接的最大数量。使用负值表示无限数量的空闲连接。
spring.redis.lettuce.pool.max -wait = -1ms ＃在池耗尽时，在抛出异常之前连接分配应阻塞的最长时间。使用负值无限期阻止。
spring.redis.lettuce.pool.min-idle = 0 ＃目标是池中维护的最小空闲连接数。此设置仅在其为正时才有效。
spring.redis.lettuce.shutdown-timeout = 100ms＃关机超时。
spring.redis.password = ＃redis服务器的登录密码。
spring.redis.port = 6379 #Redis服务器端口。
spring.redis.sentinel.master = #Redis服务器的名称。
spring.redis.sentinel.nodes = ＃逗号分隔的“host：port”对列表。
spring.redis.ssl = false ＃是否启用SSL支持。
spring.redis.timeout = ＃连接超时。



##### #TRANSACTION （TransactionProperties）

spring.transaction.default-timeout = ＃默认事务超时。如果未指定持续时间后缀，则将使用秒。
spring.transaction.rollback-on-commit-failure = ＃是否回滚提交失败。



＃---------------------------------------- 

#### ＃INTEGRATION PROPERTIES 

＃----- -----------------------------------

##### #ACTIVEMQ（ActiveMQProperties）

spring.activemq.broker-url = ActiveMQ代理的URL。默认情况下自动生成。
spring.activemq.close-timeout = 15s ＃在考虑结束完成之前等待的时间。
spring.activemq.in-memory = true ＃默认代理URL是否应该在内存中。如果已指定显式代理，则忽略。
spring.activemq.non-blocking-redelivery = false ＃是否在从回滚事务重新传递消息之前停止消息传递。这意味着启用此消息顺序时不会保留消息顺序。
spring.activemq.password = ＃代理的登录密码。
spring.activemq.send-timeout = 0ms ＃等待消息发送响应的时间。将其设置为0以永远等待。
spring.activemq.user = ＃代理的登录用户。
spring.activemq.packages.trust-all = ＃是否信任所有包。
spring.activemq.packages.trusted = ＃逗号分隔的要信任的特定包列表（不信任所有包时）。
spring.activemq.pool.block-if-full = true ＃是否阻止请求连接并且池已满。将其设置为false以改为抛出“JMSException”。
spring.activemq.pool.block-if-full-timeout = -1ms＃如果池仍然满，则在抛出异常之前阻塞。
spring.activemq.pool.enabled = false ＃是否应创建JmsPoolConnectionFactory，而不是常规ConnectionFactory。
spring.activemq.pool.idle-timeout = 30s ＃连接空闲超时。
spring.activemq.pool.max-connections = 1 ＃最大池化连接数。
spring.activemq.pool.max-sessions-per-connection = 500 ＃池中每个连接的最大池化会话数。
spring.activemq.pool.time-between-expiration-check = -1ms ＃在空闲连接驱逐线程的运行之间休眠的时间。当为负时，没有空闲连接驱逐线程运行。
spring.activemq.pool.use-anonymous-producer = true ＃是否只使用一个匿名“MessageProducer”实例。将其设置为false以在每次需要时创建一个“MessageProducer”。



##### #ARTEMIS （ArtemisProperties）

spring.artemis.embedded.cluster-password = ＃群集密码。默认情况下在启动时随机生成。
spring.artemis.embedded.data-directory = #Journal 文件目录。如果关闭持久性，则没有必要。
spring.artemis.embedded.enabled = true ＃如果Artemis服务器API可用，是否启用嵌入模式。
spring.artemis.embedded.persistent = false ＃是否启用持久存储。
spring.artemis.embedded.queues = ＃逗号分隔的队列，在启动时创建。
spring.artemis.embedded.server-id =＃服务器ID。默认情况下，使用自动递增的计数器。
spring.artemis.embedded.topics = ＃在启动时要创建的以逗号分隔的主题列表。
spring.artemis.host = localhost ＃Artemis broker主机。
spring.artemis.mode = ＃Artemis部署模式，默认情况下自动检测。
spring.artemis.password = ＃代理的登录密码。
spring.artemis.pool.block-if-full = true ＃是否在请求连接且池已满时阻止。将其设置为false以改为抛出“JMSException”。
spring.artemis.pool.block-if-full-timeout = -1ms ＃如果池仍然满，则在抛出异常之前阻塞。
spring.artemis.pool.enabled = false ＃是否应创建JmsPoolConnectionFactory，而不是常规ConnectionFactory。
spring.artemis.pool.idle-timeout = 30s ＃连接空闲超时。
spring.artemis.pool.max-connections = 1 ＃池化连接的最大数量。
spring.artemis.pool.max-sessions-per-connection = 500 ＃池中每个连接的最大池化会话数。
spring.artemis.pool.time-between-expiration-check = -1ms ＃在空闲连接驱逐线程的运行之间休眠的时间。当为负时，没有空闲连接驱逐线程运行。
spring.artemis.pool.use-anonymous-producers = true＃是否只使用一个匿名“MessageProducer”实例。将其设置为false以在每次需要时创建一个“MessageProducer”。
spring.artemis.port = 61616 #Artemis 经纪人端口。
spring.artemis.user = ＃代理的登录用户。



##### #SPRING BATCH（BatchProperties）

spring.batch.initialize-schema = embedded ＃数据库模式初始化模式。
spring.batch.job.enabled = true ＃在启动时执行上下文中的所有Spring Batch作业。
spring.batch.job.names = ＃逗号分隔的要在启动时执行的作业名称列表（例如，`job1，job2`）。默认情况下，将执行上下文中找到的所有作业。
spring.batch.schema = classpath中：组织/ springframework的/批号/核心/ schema- @ @ 平台@ @ .SQL ＃的路径SQL文件，以用于初始化数据库架构。
spring.batch.table-prefix =＃所有批次元数据表的表前缀。



##### #SPRING INTEGRATION（IntegrationProperties）

spring.integration.jdbc.initialize-schema = embedded ＃数据库模式初始化模式。
spring.integration.jdbc.schema = classpath中：组织/ springframework的/集成/ JDBC / schema- @ @ 平台@ @ .SQL ＃的路径SQL文件，以用于初始化数据库架构。



##### #JMS （JmsProperties）

spring.jms.cache.consumers = false ＃是否缓存消息使用者。
spring.jms.cache.enabled = true ＃是否缓存会话。
spring.jms.cache.producers = true ＃是否缓存消息生成器。
spring.jms.cache.session-cache-size = 1 ＃会话缓存的大小（根据JMS会话类型）。
spring.jms.jndi-name = ＃连接工厂JNDI名称。设置时，优先于其他连接工厂自动配置。
spring.jms.listener.acknowledge-mode = ＃容器的确认模式。默认情况下，侦听器使用自动确认进行事务处理。
spring.jms.listener.auto-startup = true ＃启动时自动启动容器。
spring.jms.listener.concurrency = ＃最小并发使用者数。
spring.jms.listener.max-concurrency = ＃最大并发使用者数。
spring.jms.pub-sub-domain = false ＃默认目标类型是否为topic。
spring.jms.template.default-destination = ＃用于没有目标参数的发送和接收操作的默认目标。
spring.jms.template.delivery-delay = ＃用于发送呼叫的传递延迟。
spring.jms.template.delivery-mode =＃交付模式。设置时启用QoS（服务质量）。
spring.jms.template.priority = ＃发送时消息的优先级。设置时启用QoS（服务质量）。
spring.jms.template.qos-enabled = ＃发送消息时是否启用显式QoS（服务质量）。
spring.jms.template.receive-timeout = ＃用于接收呼叫的超时。
spring.jms.template.time-to-live = ＃发送时消息的生存时间。设置时启用QoS（服务质量）。



##### #APACHE KAFKA（KafkaProperties）

spring.kafka.admin.client-id = #ID 在发出请求时传递给服务器。用于服务器端日志记录。
spring.kafka.admin.fail-fast = false ＃如果代理在启动时不可用，是否快速失败。
spring.kafka.admin.properties。* = ＃用于配置客户端的其他特定于管理员的属性。
spring.kafka.admin.ssl.key-password = ＃密钥库文件中私钥的密码。
spring.kafka.admin.ssl.key-store-location = ＃密钥库文件的位置。
spring.kafka.admin.ssl.key-store-password =＃存储密钥库文件的密码。
spring.kafka.admin.ssl.key-store-type = ＃密钥库的类型。
spring.kafka.admin.ssl.protocol = ＃要使用的SSL协议。
spring.kafka.admin.ssl.trust-store-location = ＃信任库文件的位置。
spring.kafka.admin.ssl.trust-store-password = ＃存储信任存储文件的密码。
spring.kafka.admin.ssl.trust-store-type = ＃信任库的类型。
spring.kafka.bootstrap-servers = ＃逗号分隔的主机：端口对列表，用于建立与Kafka集群的初始连接。除非被覆盖，否则适用于所有组件。
spring.kafka.client-id = #ID 在发出请求时传递给服务器。用于服务器端日志记录。
spring.kafka.consumer.auto-commit-interval = ＃如果'enable.auto.commit'设置为true，则将消费者偏移自动提交给Kafka的频率。
spring.kafka.consumer.auto-offset-reset = ＃当Kafka中没有初始偏移量或者服务器上不再存在当前偏移量时该怎么办。
spring.kafka.consumer.bootstrap-servers = ＃逗号分隔的主机：端口对列表，用于建立与Kafka集群的初始连接。为消费者覆盖全球财产。
spring.kafka.consumer.client-id =#ID在发出请求时传递给服务器。用于服务器端日志记录。
spring.kafka.consumer.enable-auto-commit = ＃是否在后台定期提交消费者的偏移量。
spring.kafka.consumer.fetch-max-wait = ＃如果没有足够的数据立即满足“fetch-min-size”给出的要求，服务器在回答获取请求之前会阻塞的最长时间。
spring.kafka.consumer.fetch-min-size = ＃服务器应为获取请求返回的最小数据量。
spring.kafka.consumer.group-id = ＃唯一字符串，用于标识此使用者所属的使用者组。
spring.kafka.consumer.heartbeat间隔= ＃心跳与消费者协调员之间的预期时间。
spring.kafka.consumer.key-deserializer = #Deserializer 类的键。
spring.kafka.consumer.max-poll-records = ＃一次调用poll（）时返回的最大记录数。
spring.kafka.consumer.properties。* = ＃用于配置客户端的其他特定于使用者的属性。
spring.kafka.consumer.ssl.key-password = ＃密钥库文件中私钥的密码。
spring.kafka.consumer.ssl.key-store-location = ＃密钥库文件的位置。
spring.kafka.consumer.ssl.key-store-password =＃存储密钥库文件的密码。
spring.kafka.consumer.ssl.key-store-type = ＃密钥库的类型。
spring.kafka.consumer.ssl.protocol = ＃要使用的SSL协议。
spring.kafka.consumer.ssl.trust-store-location = ＃信任存储文件的位置。
spring.kafka.consumer.ssl.trust-store-password = ＃存储信任存储文件的密码。
spring.kafka.consumer.ssl.trust-store-type = ＃信任库的类型。
spring.kafka.consumer.value-deserializer = #Deserializer 类的值。
spring.kafka.jaas.control-flag = required ＃登录配置的控制标志。
spring.kafka.jaas.enabled = false ＃是否启用JAAS配置。
spring.kafka.jaas.login-module = com.sun.security.auth.module.Krb5LoginModule ＃登录模块。
spring.kafka.jaas.options = ＃其他JAAS选项。
spring.kafka.listener.ack-count = ＃当ackMode为“COUNT”或“COUNT_TIME”时，偏移提交之间的记录数。
spring.kafka.listener.ack-mode = ＃Listener AckMode。请参阅spring-kafka文档。
spring.kafka.listener.ack-time = ＃当ackMode为“TIME”或“COUNT_TIME”时，偏移提交之间的时间。
spring.kafka.listener.client-id =＃侦听器的使用者client.id属性的前缀。
spring.kafka.listener.concurrency = ＃在侦听器容器中运行的线程数。
spring.kafka.listener.idle-event-interval = ＃发布空闲消费者事件（未收到数据）之间的时间。
spring.kafka.listener.log-container-config = ＃是否在初始化期间记录容器配置（INFO级别）。
spring.kafka.listener.monitor-interval = ＃检查无响应的消费者之间的时间。如果未指定持续时间后缀，则将使用秒。
spring.kafka.listener.no-poll-threshold =#Multiplier应用于“pollTimeout”以确定消费者是否无响应。
spring.kafka.listener.poll-timeout = ＃轮询消费者时使用的超时。
spring.kafka.listener.type = single ＃Listener类型。
spring.kafka.producer.acks = ＃生产者要求领导者在考虑完成请求之前收到的确认数。
spring.kafka.producer.batch-size = ＃默认批量大小。
spring.kafka.producer.bootstrap-servers = ＃逗号分隔的主机：端口对列表，用于建立与Kafka集群的初始连接。为生产者覆盖全球财产。
spring.kafka.producer.buffer-memory = ＃生产者可用于缓冲等待发送到服务器的记录的总内存大小。
spring.kafka.producer.client-id = #ID 在发出请求时传递给服务器。用于服务器端日志记录。
spring.kafka.producer.compression-type = ＃生产者生成的所有数据的压缩类型。
spring.kafka.producer.key-serializer = ＃密码的Serializer类。
spring.kafka.producer.properties。* = ＃用于配置客户端的其他特定于生产者的属性。
spring.kafka.producer.retries = ＃大于零时，启用重试失败的发送。
spring.kafka.producer.ssl.key-password = ＃密钥库文件中私钥的密码。
spring.kafka.producer.ssl.key-store-location = ＃密钥库文件的位置。
spring.kafka.producer.ssl.key-store-password = ＃存储密钥库文件的密码。
spring.kafka.producer.ssl.key-store-type = ＃密钥库的类型。
spring.kafka.producer.ssl.protocol = ＃要使用的SSL协议。
spring.kafka.producer.ssl.trust-store-location = ＃信任库文件的位置。
spring.kafka.producer.ssl.trust-store-password = ＃存储信任存储文件的密码。
spring.kafka.producer.ssl.trust-store-type = ＃信任库的类型。
spring.kafka.producer.transaction-id-prefix = ＃非空时，为生产者启用事务支持。
spring.kafka.producer.value-serializer = #Serializer 类的值。
spring.kafka.properties。* = ＃用于配置客户端的生产者和使用者共有的附加属性。

spring.kafka.ssl.key-password = ＃密钥库文件中私钥的密码。
spring.kafka.ssl.key-store-location = ＃密钥库文件的位置。
spring.kafka.ssl.key-store-password =＃存储密钥库文件的密码。
spring.kafka.ssl.key-store-type = ＃密钥库的类型。
spring.kafka.ssl.protocol = ＃要使用的SSL协议。
spring.kafka.ssl.trust-store-location = ＃信任库文件的位置。
spring.kafka.ssl.trust-store-password = ＃存储信任存储文件的密码。
spring.kafka.ssl.trust-store-type = ＃信任库的类型。
spring.kafka.streams.application-id = #Kafka streams application.id property; 默认spring.application.name。
spring.kafka.streams.auto-startup = true ＃是否自动启动流工厂bean。
spring.kafka.streams.bootstrap-servers = ＃逗号分隔的主机：端口对列表，用于建立与Kafka集群的初始连接。覆盖流的全局属性。
spring.kafka.streams.cache-max-size-buffering = ＃用于跨所有线程缓冲的最大内存大小。
spring.kafka.streams.client-id = #ID 在发出请求时传递给服务器。用于服务器端日志记录。
spring.kafka.streams.properties。* = ＃用于配置流的其他Kafka属性。
spring.kafka.streams.replication-factor =＃流处理应用程序创建的更改日志主题和重新分区主题的复制因子。
spring.kafka.streams.ssl.key-password = ＃密钥库文件中私钥的密码。
spring.kafka.streams.ssl.key-store-location = ＃密钥库文件的位置。
spring.kafka.streams.ssl.key-store-password = ＃存储密钥库文件的密码。
spring.kafka.streams.ssl.key-store-type = ＃密钥库的类型。
spring.kafka.streams.ssl.protocol = ＃要使用的SSL协议。
spring.kafka.streams.ssl.trust-store-location = ＃信任库文件的位置。
spring.kafka.streams.ssl.trust-store-password = ＃存储信任存储文件的密码。
spring.kafka.streams.ssl.trust-store-type = ＃信任库的类型。
spring.kafka.streams.state-dir = ＃状态存储的目录位置。
spring.kafka.template.default-topic = ＃发送消息的默认主题。



##### #RABBIT（RabbitProperties）

spring.rabbitmq.addresses = ＃逗号分隔的客户端应连接的地址列表。
spring.rabbitmq.cache.channel.checkout-timeout = ＃达到缓存大小后等待获取通道的持续时间。
spring.rabbitmq.cache.channel.size = ＃要在缓存中保留的通道数。
spring.rabbitmq.cache.connection.mode = channel ＃连接工厂缓存模式。
spring.rabbitmq.cache.connection.size = ＃缓存的连接数。
spring.rabbitmq.connection-timeout = ＃连接超时。将其设置为零以永远等待。
spring.rabbitmq.dynamic = true ＃是否创建AmqpAdmin bean。
spring.rabbitmq.host = localhost ＃RabbitMQ主机。
spring.rabbitmq.listener.direct.acknowledge-mode = ＃容器的确认模式。
spring.rabbitmq.listener.direct.auto-startup = true ＃是否在启动时自动启动容器。
spring.rabbitmq.listener.direct.consumers-per-queue = ＃每个队列的消费者数量。
spring.rabbitmq.listener.direct.default-requeue-rejected = ＃默认情况下，拒绝的交付是否重新排队。
spring.rabbitmq.listener.direct.idle-event-interval =＃应该发布空闲容器事件的频率。
spring.rabbitmq.listener.direct.missing-queues-fatal = false ＃如果容器声明的队列在代理上不可用，则是否失败。
spring.rabbitmq.listener.direct.prefetch = ＃每个消费者可能未完成的未确认消息的最大数量。
spring.rabbitmq.listener.direct.retry.enabled = false ＃是否启用发布重试。
spring.rabbitmq.listener.direct.retry.initial-interval = 1000ms ＃第一次和第二次尝试传递消息之间的持续时间。
spring.rabbitmq.listener.direct.retry.max-attempts = 3＃传递邮件的最大尝试次数。
spring.rabbitmq.listener.direct.retry.max -interval = 10000ms ＃尝试之间的最长持续时间。
spring.rabbitmq.listener.direct.retry.multiplier = 1 ＃乘数应用于先前的重试间隔。
spring.rabbitmq.listener.direct.retry.stateless = true ＃重试是无状态还是有状态。
spring.rabbitmq.listener.simple.acknowledge-mode = ＃容器的确认模式。
spring.rabbitmq.listener.simple.auto-startup = true ＃是否在启动时自动启动容器。
spring.rabbitmq.listener.simple.concurrency =＃侦听器调用者线程的最小数量。
spring.rabbitmq.listener.simple.default-requeue-rejected = ＃默认情况下，拒绝的交付是否重新排队。
spring.rabbitmq.listener.simple.idle-event-interval = ＃应该发布空闲容器事件的频率。
spring.rabbitmq.listener.simple.max-concurrency = ＃侦听器调用者线程的最大数量。
spring.rabbitmq.listener.simple.missing-queues-fatal = true ＃如果容器声明的队列在代理上不可用，则是否失败和/或如果在运行时删除一个或多个队列，是否停止容器。
spring.rabbitmq.listener.simple.prefetch =＃每个消费者可能未完成的未确认消息的最大数量。
spring.rabbitmq.listener.simple.retry.enabled = false ＃是否启用发布重试。
spring.rabbitmq.listener.simple.retry.initial-interval = 1000ms ＃第一次和第二次尝试传递消息之间的持续时间。
spring.rabbitmq.listener.simple.retry.max-attempts = 3 ＃传递邮件的最大尝试次数。
spring.rabbitmq.listener.simple.retry.max -interval = 10000ms ＃尝试之间的最长持续时间。
spring.rabbitmq.listener.simple.retry.multiplier = 1 ＃乘数应用于上一个重试间隔。
spring.rabbitmq.listener.simple.retry.stateless = true ＃重试是无状态还是有状态。
spring.rabbitmq.listener.simple.transaction-size = ＃确认模式为AUTO时要在acks之间处理的消息数。如果大于预取，则预取将增加到此值。
spring.rabbitmq.listener.type = simple ＃Listener容器类型。
spring.rabbitmq.password = guest ＃登录以对代理进行身份验证。
spring.rabbitmq.port = 5672 ＃RabbitMQ端口。
spring.rabbitmq.publisher-confirms = false ＃是否启用发布者确认。
spring.rabbitmq.publisher-returns = false＃是否启用发布者返回。
spring.rabbitmq.requested-heartbeat = ＃请求心跳超时; 零，没有。如果未指定持续时间后缀，则将使用秒。
spring.rabbitmq.ssl.algorithm = #SSL 算法使用。默认情况下，由Rabbit客户端库配置。
spring.rabbitmq.ssl.enabled = false ＃是否启用SSL支持。
spring.rabbitmq.ssl.key-store = ＃保存SSL证书的密钥库的路径。
spring.rabbitmq.ssl.key-store-password = ＃用于访问密钥库的密码。
spring.rabbitmq.ssl.key-store-type = PKCS12 ＃密钥库类型。
spring.rabbitmq.ssl.trust-store = ＃持有SSL证书的信任存储。
spring.rabbitmq.ssl.trust-store-password = ＃用于访问信任库的密码。
spring.rabbitmq.ssl.trust-store-type = JKS #Trust store type。
spring.rabbitmq.ssl.validate-server-certificate = true ＃是否启用服务器端证书验证。
spring.rabbitmq.ssl.verify-hostname = true ＃是否启用主机名验证。
spring.rabbitmq.template.default-receive-queue = ＃从明确指定none时接收消息的默认队列的名称。
spring.rabbitmq.template.exchange =＃用于发送操作的默认交换的名称。
spring.rabbitmq.template.mandatory = ＃是否启用强制消息。
spring.rabbitmq.template.receive-timeout = ＃receive（）`操作的超时。
spring.rabbitmq.template.reply-timeout = #outoutout用于`sendAndReceive（）`操作。
spring.rabbitmq.template.retry.enabled = false ＃是否启用发布重试。
spring.rabbitmq.template.retry.initial-interval = 1000ms ＃第一次和第二次尝试传递消息之间的持续时间。
spring.rabbitmq.template.retry.max-attempts = 3 ＃传递邮件的最大尝试次数。
spring.rabbitmq.template.retry.max -interval = 10000ms ＃尝试之间的最长持续时间。
spring.rabbitmq.template.retry.multiplier = 1 ＃乘数应用于先前的重试间隔。
spring.rabbitmq.template.routing-key = ＃用于发送操作的默认路由密钥的值。
spring.rabbitmq.username = guest ＃登录用户以对代理进行身份验证。
spring.rabbitmq.virtual-host = ＃连接到代理时使用的虚拟主机。



＃---------------------------------------- 

#### ＃ACTUATOR PROPERTIES 

＃----- -----------------------------------

##### #MANAGEMENT HTTP SERVER（ManagementServerProperties）

management.server.add-application-context-header = false ＃在每个响应中添加“X-Application-Context”HTTP标头。
management.server.address = ＃管理端点应绑定到的网络地址。需要自定义management.server.port。
management.server.port = ＃管理端点HTTP端口（默认情况下使用与应用程序相同的端口）。配置其他端口以使用特定于管理的SSL。
management.server.servlet.context-path = ＃管理端点context-path（例如，`/ management`）。需要自定义management.server.port。
management.server.ssl.ciphers= ＃支持的SSL密码。
management.server.ssl.client-auth = ＃客户端身份验证模式。
management.server.ssl.enabled = true ＃是否启用SSL支持。
management.server.ssl.enabled-protocols = ＃启用SSL协议。
management.server.ssl.key-alias = ＃标识密钥库中密钥的别名。
management.server.ssl.key-password = ＃用于访问密钥库中密钥的密码。
management.server.ssl.key-store = ＃保存SSL证书的密钥库的路径（通常是jks文件）。
management.server.ssl.key-store-password =＃用于访问密钥库的密码。
management.server.ssl.key-store-provider = ＃密钥库的提供者。
management.server.ssl.key-store-type = ＃密钥库的类型。
management.server.ssl.protocol = TLS ＃要使用的SSL协议。
management.server.ssl.trust-store = ＃持有SSL证书的信任存储。
management.server.ssl.trust-store-password = ＃用于访问信任库的密码。
management.server.ssl.trust-store-provider = ＃信任存储的提供者。
management.server.ssl.trust-store-type = ＃信任库的类型。



##### #CLOUDFOUNDRY 

management.cloudfoundry.enabled = true ＃是否启用扩展的Cloud Foundry执行器端点。
management.cloudfoundry.skip-ssl-validation = false ＃是否跳过Cloud Foundry执行器端点安全调用的SSL验证。



##### #ENDPOINTS GENERAL CONFIGURATION 

management.endpoints.enabled-by-default = ＃默认情况下是否启用或禁用所有端点。

##### #ENDPOINTS JMX CONFIGURATION（JmxEndpointProperties）

management.endpoints.jmx.domain = org.springframework.boot #Endpoints JMX域名。如果设置，则回退到'spring.jmx.default-domain'。
management.endpoints.jmx.exposure.include = * ＃应包含的端点ID或所有的“*”。
management.endpoints.jmx.exposure.exclude = ＃应排除的端点ID或所有的'*'。
management.endpoints.jmx.static-names = ＃附加的静态属性，附加到表示端点的MBean的所有ObjectName。



##### #ENDPOINTS WEB CONFIGURATION（WebEndpointProperties）

management.endpoints.web.exposure.include = health，info ＃应包含的端点ID或所有的“*”。
management.endpoints.web.exposure.exclude = ＃应排除的端点ID或所有的'*'。
management.endpoints.web.base-path = / actuator #Web端点的基本路径。相对于server.servlet.context-path或management.server.servlet.context-path，如果配置了management.server.port。
management.endpoints.web.path-mapping = ＃端点ID与应公开它们的路径之间的映射。



##### #ENDPOINTS CORS CONFIGURATION（CorsEndpointProperties）

management.endpoints.web.cors.allow-credentials = ＃是否支持凭据。未设置时，不支持凭据。
management.endpoints.web.cors.allowed-headers = ＃逗号分隔的请求中允许的标头列表。'*'允许所有标题。
management.endpoints.web.cors.allowed-methods = ＃逗号分隔的允许方法列表。'*'允许所有方法。未设置时，默认为GET。
management.endpoints.web.cors.allowed-origins = ＃逗号分隔的原始列表允许。'*'允许所有来源。未设置时，将禁用CORS支持。
management.endpoints.web.cors.exposed-headers = ＃逗号分隔的标题列表，包含在响应中。
management.endpoints.web.cors.max-age = 1800s ＃客户端缓存来自飞行前请求的响应的时间。如果未指定持续时间后缀，则将使用秒。



##### #AUDIT EVENTS ENDPOINT（AuditEventsEndpoint）

management.endpoint.auditevents.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.auditevents.enabled = true ＃是否启用auditevents端点。



##### #BEANS ENDPOINT（BeansEndpoint）

management.endpoint.beans.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.beans.enabled = true ＃是否启用beans端点。



##### #CACHES ENDPOINT（CachesEndpoint）

management.endpoint.caches.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.caches.enabled = true ＃是否启用缓存端点。

＃条件报告ENDPOINT（ConditionsReportEndpoint）
management.endpoint.conditions.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.conditions.enabled = true ＃是否启用条件端点。

#CONFIGURATION PROPERTIES REPORT ENDPOINT（ConfigurationPropertiesReportEndpoint，ConfigurationPropertiesReportEndpointProperties）
management.endpoint.configprops.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.configprops.enabled = true ＃是否启用configprops端点。
management.endpoint.configprops.keys-to-sanitize = password，secret，key，token，。* credentials。*，vcap_services，sun.java.command ＃应该清理的密钥。键可以是属性结尾的简单字符串或正则表达式。

#ENVEST ENDPOINT（EnvironmentEndpoint，EnvironmentEndpointProperties）
 management.endpoint.env.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.env.enabled = true ＃是否启用env端点。
management.endpoint.env.keys-to-sanitize = password，secret，key，token，。* credentials。*，vcap_services，sun.java.command ＃应该清理的密钥。键可以是属性结尾的简单字符串或正则表达式。



##### #FLYWAY ENDPOINT（FlywayEndpoint）

management.endpoint.flyway.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.flyway.enabled = true ＃是否启用flyway端点。

#HEEC ENDPOINT（HealthEndpoint，HealthEndpointProperties）
 management.endpoint.health.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.health.enabled = true ＃是否启用运行状况端点。
management.endpoint.health.roles = ＃用于确定是否授权用户显示详细信息的角色。为空时，所有经过身份验证的用户都被授权。
management.endpoint.health.show-details = never ＃何时显示完整的健康详细信息。

#HEAP DUMP ENDPOINT（HeapDumpWebEndpoint）
 management.endpoint.heapdump.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.heapdump.enabled = true ＃是否启用heapdump端点。

#HTTP TRACE ENDPOINT（HttpTraceEndpoint）
 management.endpoint.httptrace.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.httptrace.enabled = true ＃是否启用httptrace端点。

#INFO ENDPOINT（InfoEndpoint）
 info = ＃要添加到信息端点的任意属性。
management.endpoint.info.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.info.enabled = true ＃是否启用信息端点。

#INTEGRATION GRAPH ENDPOINT（IntegrationGraphEndpoint）
 management.endpoint.integrationgraph.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.integrationgraph.enabled = true ＃是否启用集成图终结点。

#JOLOKIA ENDPOINT（JolokiaProperties）
 management.endpoint.jolokia.config。* = ＃Jolokia设置。有关更多详细信息，请参阅Jolokia的文档。
management.endpoint.jolokia.enabled = true ＃是否启用jolokia端点。

#LIQUIBASE ENDPOINT（LiquibaseEndpoint）
 management.endpoint.liquibase.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.liquibase.enabled = true ＃是否启用liquibase端点。

＃LOG FILE ENDPOINT（LogFileWebEndpoint，LogFileWebEndpointProperties）
 management.endpoint.logfile.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.logfile.enabled = true ＃是否启用日志文件端点。
management.endpoint.logfile.external-file = ＃要访问的外部日志文件。如果日志文件由输出重定向而不是日志记录系统本身写入，则可以使用。

＃LOGGERS ENDPOINT（LoggersEndpoint）
 management.endpoint.loggers.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.loggers.enabled = true ＃是否启用记录器端点。

#REQUEST MAPPING ENDPOINT（MappingsEndpoint）
 management.endpoint.mappings.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.mappings.enabled = true ＃是否启用映射端点。

#METRICS ENDPOINT（MetricsEndpoint）
 management.endpoint.metrics.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.metrics.enabled = true ＃是否启用指标端点。

#PROMETHEUS ENDPOINT（PrometheusScrapeEndpoint）
 management.endpoint.prometheus.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.prometheus.enabled = true ＃是否启用prometheus端点。

#STEEDEDED TASKS ENDPOINT（ScheduledTasksEndpoint）
 management.endpoint.scheduledtasks.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.scheduledtasks.enabled = true ＃是否启用scheduledtasks端点。

#SESSIONS ENDPOINT（SessionsEndpoint）
 management.endpoint.sessions.enabled = true ＃是否启用会话端点。

#SHUTDOWN ENDPOINT（ShutdownEndpoint）
 management.endpoint.shutdown.enabled = false ＃是否启用关闭端点。

#THREAD DUMP ENDPOINT（ThreadDumpEndpoint）
 management.endpoint.threaddump.cache.time-to-live = 0ms ＃可以缓存响应的最长时间。
management.endpoint.threaddump.enabled = true ＃是否启用threaddump端点。



##### ＃HEALTH INDICATORS 

management.health.db.enabled = true ＃是否启用数据库运行状况检查。
management.health.cassandra.enabled = true ＃是否启用Cassandra运行状况检查。
management.health.couchbase.enabled = true ＃是否启用Couchbase运行状况检查。
management.health.defaults.enabled = true ＃是否启用默认健康指标。
management.health.diskspace.enabled = true ＃是否启用磁盘空间运行状况检查。
management.health.diskspace.path = ＃用于计算可用磁盘空间的路径。
management.health.diskspace.threshold = 10MB＃应该可用的最小磁盘空间。
management.health.elasticsearch.enabled = true ＃是否启用Elasticsearch运行状况检查。
management.health.elasticsearch.indices = ＃逗号分隔的索引名称。
management.health.elasticsearch.response-timeout = 100ms ＃等待集群响应的时间。
management.health.influxdb.enabled = true ＃是否启用InfluxDB运行状况检查。
management.health.jms.enabled = true ＃是否启用JMS运行状况检查。
management.health.ldap.enabled = true ＃是否启用LDAP运行状况检查。
management.health.mail.enabled = true＃是否启用邮件健康检查。
management.health.mongo.enabled = true ＃是否启用MongoDB运行状况检查。
management.health.neo4j.enabled = true ＃是否启用Neo4j运行状况检查。
management.health.rabbit.enabled = true ＃是否启用RabbitMQ运行状况检查。
management.health.redis.enabled = true ＃是否启用Redis运行状况检查。
management.health.solr.enabled = true ＃是否启用Solr运行状况检查。
management.health.status.http-mapping = ＃将健康状态映射到HTTP状态代码。默认情况下，已注册的运行状况映射到合理的默认值（例如，UP映射到200）。
management.health.status.order = DOWN，OUT_OF_SERVICE，UP，UNKNOWN ＃以逗号分隔的健康状态列表，按严重程度排序。



##### #HTTP TRACING（HttpTraceProperties）

management.trace.http.enabled = true ＃是否启用HTTP请求 - 响应跟踪。
management.trace.http.include =请求标头，响应标头，cookie，错误＃要包含在跟踪中的项目。



##### #INFO CONTRIBUTORS（InfoContributorProperties）

management.info.build.enabled = true ＃是否启用构建信息。
management.info.defaults.enabled = true ＃是否启用默认信息贡献者。
management.info.env.enabled = true ＃是否启用环境信息。
management.info.git.enabled = true ＃是否启用git信息。
management.info.git.mode = simple ＃用于公开git信息的模式。



##### #METRICS 

management.metrics.distribution.maximum-expected-value。* = ＃预计将以指定名称开始计量ID的最大值。
management.metrics.distribution.minimum-expected-value。* = ＃预计将以指定名称开始的仪表ID的最小值。
management.metrics.distribution.percentiles。* = ＃特定计算的非可聚合百分位数，用于以指定名称开始的仪表ID发送到后端。
management.metrics.distribution.percentiles-histogram。* = ＃是否以指定名称开头的米ID应发布百分位直方图。
management.metrics.distribution.sla。* =＃以指定名称开头的仪表ID的特定SLA边界。最长的比赛获胜。
management.metrics.enable。* = ＃应启用是否以指定名称开始的仪表ID。最长的匹配获胜，关键的“all”也可以用于配置所有的米。
management.metrics.export.appoptics.api-token = #AppOptics API令牌。
management.metrics.export.appoptics.batch-size = 500 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.appoptics.connect-timeout = 5s ＃对此后端的请求的连接超时。
management.metrics.export.appoptics.enabled= true ＃是否启用将度量标准导出到此后端。
management.metrics.export.appoptics.host-tag = instance ＃将指标发送到AppOptics时将映射到“@host”的标记。
management.metrics.export.appoptics.num-threads = 2 ＃指标发布计划程序使用的线程数。
management.metrics.export.appoptics.read-timeout = 10s ＃读取此后端请求的超时时间。
management.metrics.export.appoptics.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.appoptics.uri = https://api.appoptics.com/v1/measurements# 将指标发送到的URI。
management.metrics.export.atlas.batch-size = 10000 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.atlas.config-refresh-frequency = 10s ＃从LWC服务刷新配置设置的频率。
management.metrics.export.atlas.config-time-to-live = 150s #LWC服务订阅的生存时间。
management.metrics.export.atlas.config-uri = http：// localhost：7101 / lwc / api / v1 / expressions / local-dev #Atlas LWC端点的URI，用于检索当前订阅。
management.metrics.export.atlas.connect-timeout = 1s＃对此后端的请求的连接超时。
management.metrics.export.atlas.enabled = true ＃是否启用将度量标准导出到此后端。
management.metrics.export.atlas.eval-uri = http：// localhost：7101 / lwc / api / v1 / evaluate ＃用于评估订阅数据的Atlas LWC端点的URI。
management.metrics.export.atlas.lwc-enabled = false ＃是否启用流式传输到Atlas LWC。
management.metrics.export.atlas.meter-time-to-live = 15m ＃没有任何活动的米的生存时间。在此期限之后，仪表将被视为已过期且不会报告。
management.metrics.export.atlas.num-threads = 2＃指标发布计划程序使用的线程数。
management.metrics.export.atlas.read-timeout = 10s ＃读取此后端请求的超时时间。
management.metrics.export.atlas.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.atlas.uri = http：// localhost：7101 / api / v1 / publish #Atlas服务器的URI。
management.metrics.export.datadog.api-key = ＃Datadog API密钥。
management.metrics.export.datadog.application-key = ＃Datadog应用程序密钥。不是严格要求，但通过向Datadog发送仪表描述，类型和基本单位来改进Datadog体验。
management.metrics.export.datadog.batch-size = 10000 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.datadog.connect-timeout = 1s ＃对此后端的请求的连接超时。
management.metrics.export.datadog.descriptions = true ＃是否将描述元数据发布到Datadog。将其关闭以最小化发送的元数据量。
management.metrics.export.datadog.enabled = true ＃是否启用将度量标准导出到此后端。
management.metrics.export.datadog.host-tag = instance＃将指标发送到Datadog时将映射到“主机”的标记。
management.metrics.export.datadog.num-threads = 2 ＃指标发布计划程序使用的线程数。
management.metrics.export.datadog.read-timeout = 10s ＃读取此后端请求的超时时间。
management.metrics.export.datadog.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.datadog.uri = https://app.datadoghq.com# 将指标发送到的URI。如果需要将指标发布到到Datadog的内部代理，则可以使用此方法定义代理的位置。
management.metrics.export.dynatrace.api-token =#Dynatrace身份验证令牌。
management.metrics.export.dynatrace.batch-size = 10000 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.dynatrace.connect-timeout = 1s ＃对此后端的请求的连接超时。
management.metrics.export.dynatrace.device-id = 将度量标准导出到Dynatrace的自定义设备的ID。
management.metrics.export.dynatrace.enabled = true ＃是否启用将度量标准导出到此后端。
management.metrics.export.dynatrace.num-threads = 2＃指标发布计划程序使用的线程数。
management.metrics.export.dynatrace.read-timeout = 10s ＃读取此后端请求的超时时间。
management.metrics.export.dynatrace.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.dynatrace.technology-type = java ＃导出的指标的技术类型。用于在Dynatrace UI中对逻辑技术名称下的度量标准进行分组。
management.metrics.export.dynatrace.uri = 将指标发送到的URI。应该用于SaaS，自我管理的实例或通过内部代理途径。
management.metrics.export.elastic.auto-create-index = true＃是否自动创建索引（如果不存在）。
management.metrics.export.elastic.batch-size = 10000 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.elastic.connect-timeout = 1s ＃对此后端的请求的连接超时。
management.metrics.export.elastic.enabled = true ＃是否启用将度量标准导出到此后端。
management.metrics.export.elastic.host = http：// localhost：9200 ＃将指标导出到的主机。
management.metrics.export.elastic.index = metrics ＃将指标导出到的索引。
management.metrics.export.elastic.index-date-format = yyyy-MM ＃用于滚动索引的索引日期格式。附加到索引名称后面加一个' - '。
management.metrics.export.elastic.num-threads = 2 ＃指标发布计划程序使用的线程数。
management.metrics.export.elastic.password = ＃弹性服务器的登录密码。
management.metrics.export.elastic.read-timeout = 10s ＃读取此后端请求的超时时间。
management.metrics.export.elastic.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.elastic.timestamp-field-name = @ timestamp ＃时间戳字段的名称。 
management.metrics.export.elastic.user-name = ＃弹性服务器的登录用户。
management.metrics.export.ganglia.addressing-mode = multicast ＃UDP寻址模式，单播或多播。
management.metrics.export.ganglia.duration- units =毫秒＃用于报告持续时间的基本时间单位。
management.metrics.export.ganglia.enabled = true ＃是否启用向Ganglia导出指标。
management.metrics.export.ganglia.host = localhost ＃Ganglia服务器的主机，用于接收导出的指标。
management.metrics.export.ganglia.port = 8649 ＃Ganglia服务器端口，用于接收导出的指标。
management.metrics.export.ganglia.protocol-version = 3.1 ＃Ganglia协议版本。必须是3.1或3.0。
management.metrics.export.ganglia.rate- units = seconds ＃用于报告费率的基本时间单位。
management.metrics.export.ganglia.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.ganglia.time-to-live = 1 ＃生活在Ganglia上的指标的时间。将多播生存时间设置为大于主机之间的跳数（路由器）的数量。
management.metrics.export.graphite.duration-units =毫秒＃用于报告持续时间的基本时间单位。
management.metrics.export.graphite.enabled = true＃是否启用将指标导出到Graphite。
management.metrics.export.graphite.host = localhost ＃Graphite服务器的主机，用于接收导出的指标。
management.metrics.export.graphite.port = 2004 ＃Graphite服务器的端口，用于接收导出的指标。
management.metrics.export.graphite.protocol = pickled ＃在将数据发送到Graphite时使用的协议。
management.metrics.export.graphite.rate-units = seconds ＃用于报告费率的基本时间单位。
management.metrics.export.graphite.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.graphite.tags-as-prefix =＃对于默认命名约定，将指定的标记键转换为度量标准前缀的一部分。
management.metrics.export.humio.api-token = ＃Humio API令牌。
management.metrics.export.humio.batch-size = 10000 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.humio.connect-timeout = 5s ＃对此后端的请求的连接超时。
management.metrics.export.humio.enabled = true ＃是否启用将度量标准导出到此后端。
management.metrics.export.humio.num-threads = 2 ＃指标发布计划程序使用的线程数。
management.metrics.export.humio.read-timeout = 10s ＃读取此后端请求的超时时间。
management.metrics.export.humio.repository = sandbox ＃要将指标发布到的存储库的名称。
management.metrics.export.humio.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.humio.tags。* = ＃Humio标签，用于描述将存储指标的数据源。Humio标签是与Micrometer标签不同的概念。千分尺的标签用于沿尺寸边界划分度量。
management.metrics.export.humio.uri = https://cloud.humio.com#idex将指标发送到。如果您需要将指标发布到Humio的内部代理，您可以使用此方法定义代理的位置。
management.metrics.export.influx.auto-create-db = true ＃在尝试向其发布指标之前，是否创建Influx数据库是否存在。
management.metrics.export.influx.batch-size = 10000 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.influx.compressed = true ＃是否对发布到Influx的度量批次启用GZIP压缩。
management.metrics.export.influx.connect-timeout = 1s＃对此后端的请求的连接超时。
management.metrics.export.influx.consistency = one ＃为每个点写一致性。
management.metrics.export.influx.db = mydb ＃将指标发送到Influx时将映射到“主机”的标记。
management.metrics.export.influx.enabled = true ＃是否启用将度量标准导出到此后端。
management.metrics.export.influx.num-threads = 2 ＃指标发布计划程序使用的线程数。
management.metrics.export.influx.password = ＃Influx服务器的登录密码。
management.metrics.export.influx.read-timeout = 10s＃读取此后端请求的超时时间。
management.metrics.export.influx.retention-duration = ＃Influx应在当前数据库中保留数据的时间段。
management.metrics.export.influx.retention-shard-duration = ＃分片组覆盖的时间范围。
management.metrics.export.influx.retention-policy = ＃要使用的保留策略（如果未指定，则Influx写入DEFAULT保留策略）。
management.metrics.export.influx.retention-replication-factor = ＃在群集中存储了多少份数据副本。
management.metrics.export.influx.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.influx.uri = http：// localhost：8086 ＃Influx服务器的URI。
management.metrics.export.influx.user-name = ＃Influx服务器的登录用户。
management.metrics.export.jmx.domain = metrics ＃Metrics JMX域名。
management.metrics.export.jmx.enabled = true ＃是否已启用将度量标准导出到JMX。
management.metrics.export.jmx.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.kairos.batch-size = 10000 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.kairos.connect-timeout = 1s ＃对此后端的请求的连接超时。
management.metrics.export.kairos.enabled = true ＃是否启用将度量标准导出到此后端。
management.metrics.export.kairos.num-threads = 2 ＃指标发布计划程序使用的线程数。
management.metrics.export.kairos.password = #KairosDB服务器的登录密码。
management.metrics.export.kairos.read-timeout = 10s ＃读取此后端请求的超时时间。
management.metrics.export.kairos.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.kairos.uri = localhost：8080 / api / v1 /  datapoints #KairosDB服务器的URI。
management.metrics.export.kairos.user-name = #KairosDB服务器的登录用户。
management.metrics.export.newrelic.account-id = ＃新Relic帐户ID。
management.metrics.export.newrelic.api-key = #New Relic API密钥。
management.metrics.export.newrelic.batch-size = 10000 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.newrelic.connect-timeout = 1s ＃对此后端的请求的连接超时。
management.metrics.export.newrelic.enabled = true ＃是否启用将度量标准导出到此后端。
management.metrics.export.newrelic.num-threads = 2 ＃指标发布计划程序使用的线程数。
management.metrics.export.newrelic.read-timeout = 10s ＃读取此后端请求的超时时间。
management.metrics.export.newrelic.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.newrelic.uri = https：//insights-collector.newrelic.com #idex 将指标发送到。
management.metrics.export.prometheus.descriptions = true＃是否将发布描述作为scrape有效负载的一部分启用到Prometheus。将其关闭以最小化每次刮擦发送的数据量。
management.metrics.export.prometheus.enabled = true ＃是否启用将指标导出到Prometheus。
management.metrics.export.prometheus.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.prometheus.pushgateway.base-url = localhost：9091 ＃Pushgateway的基本URL。
management.metrics.export.prometheus.pushgateway.enabled = false ＃通过Prometheus Pushgateway启用发布。
management.metrics.export.prometheus.pushgateway.grouping-key =＃为推送的指标分组键。
management.metrics.export.prometheus.pushgateway.job = ＃此应用程序实例的作业标识符。
management.metrics.export.prometheus.pushgateway.push-rate = 1m ＃用于推送指标的频率。
management.metrics.export.prometheus.pushgateway.shutdown-operation = ＃应该在关机时执行的操作。
management.metrics.export.signalfx.access-token = #SignalFX访问令牌。
management.metrics.export.signalfx.batch-size = 10000 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.signalfx.connect-timeout = 1s ＃对此后端的请求的连接超时。
management.metrics.export.signalfx.enabled = true ＃是否启用将度量标准导出到此后端。
management.metrics.export.signalfx.num-threads = 2 ＃指标发布计划程序使用的线程数。
management.metrics.export.signalfx.read-timeout = 10s ＃读取此后端请求的超时时间。
management.metrics.export.signalfx.source = ＃唯一标识正在向SignalFx发布指标的应用实例。默认为本地主机名。
management.metrics.export.signalfx.step = 10s＃步骤大小（即报告频率）使用。
management.metrics.export.signalfx.uri = https：//ingest.signalfx.com# 将指标发送到的URI。
management.metrics.export.simple.enabled = true ＃在没有任何其他导出器的情况下，是否启用将指标导出到内存后端。
management.metrics.export.simple.mode =累积＃计数模式。
management.metrics.export.simple.step = 1m ＃要使用的步长（即报告频率）。
management.metrics.export.statsd.enabled = true ＃是否启用将度量标准导出到StatsD。
management.metrics.export.statsd.flavor = datadog#StatsD线路协议使用。
management.metrics.export.statsd.host = localhost ＃StatsD服务器的主机，用于接收导出的指标。
management.metrics.export.statsd.max-packet-length = 1400 ＃单个有效负载的总长度应保持在网络的MTU中。
management.metrics.export.statsd.polling-frequency = 10s ＃测量仪表
的频率。轮询仪表时，会重新计算其值，如果值已更改（或者publishUnchangedMeters为true），则会将其发送到StatsD服务器。management.metrics.export.statsd.port = 8125 ＃StatsD服务器的端口，用于接收导出的指标。
management.metrics.export.statsd.publish-不变米= true ＃是否将未更改的计量表发送到StatsD服务器。
management.metrics.export.wavefront.api-token = ＃将指标直接发布到Wavefront API主机时使用的API令牌。
management.metrics.export.wavefront.batch-size = 10000 ＃每个请求用于此后端的度量数。如果找到更多测量值，则将发出多个请求。
management.metrics.export.wavefront.connect-timeout = 1s ＃对此后端的请求的连接超时。
management.metrics.export.wavefront.enabled = true ＃是否启用将度量标准导出到此后端。
management.metrics.export.wavefront.global-prefix =＃全局前缀用于将源自此应用程序的白盒工具的度量标准与在Wavefront UI中查看时源自其他Wavefront集成的度量标准分开。
management.metrics.export.wavefront.num-threads = 2 ＃指标发布计划程序使用的线程数。
management.metrics.export.wavefront.read-timeout = 10s ＃读取此后端请求的超时时间。
management.metrics.export.wavefront.source = ＃应用程序实例的唯一标识符，该实例是发布到Wavefront的度量标准的来源。默认为本地主机名。
management.metrics.export.wavefront.step = 10s ＃要使用的步长（即报告频率）。
management.metrics.export.wavefront.uri = https://longboard.wavefront.com# 将指标发送到的URI。
management.metrics.use-global-registry = true ＃是否应将自动配置的MeterRegistry实现绑定到Metrics上的全局静态注册表。
management.metrics.tags。* = ＃应用于每个仪表的公共标签。
management.metrics.web.client.max-uri-tags = 100 ＃允许的唯一URI标记值的最大数量。达到最大标记值数后，过滤器将拒绝具有其他标记值的度量标准。
management.metrics.web.client.requests-metric-name = http.client.requests ＃已发送请求的度量标准的名称。
management.metrics.web.server.auto-time-requests = true ＃是否应自动为Spring MVC，WebFlux或Jersey处理的请求定时。
management.metrics.web.server.max-uri-tags = 100 ＃允许的唯一URI标记值的最大数量。达到最大标记值数后，过滤器将拒绝具有其他标记值的度量标准。
management.metrics.web.server.requests-metric-name = http.server.requests ＃已接收请求的度量标准的名称。



＃---------------------------------------- 

#### #DEDTOOLS PROPERTIES 

＃----- -----------------------------------

##### #DESTOOLS（DevToolsProperties）

spring.devtools.add-properties = true ＃是否启用开发属性默认值。
spring.devtools.livereload.enabled = true ＃是否启用livereload.com兼容服务器。
spring.devtools.livereload.port = 35729 ＃服务器端口。
spring.devtools.restart.additional-exclude = ＃应该从触发完全重启中排除的其他模式。
spring.devtools.restart.additional-paths = ＃要监视更改的其他路径。
spring.devtools.restart.enabled = true ＃是否启用自动重启。
spring.devtools.restart.exclude= META-INF /行家/ **，META-INF /资源/ **，资源/ **，静态/ **，公共/ **，模板/ **，** / *的Test.class，** / * Tests.class，git.properties，META-INF / build-info.properties ＃应该从触发完全重启中排除的模式。
spring.devtools.restart.log-condition-evaluation-delta = true ＃是否在重新启动时记录条件评估增量。
spring.devtools.restart.poll-interval = 1s ＃轮询类路径更改之间等待的时间。
spring.devtools.restart.quiet-period = 400ms ＃触发重启之前没有任何类路径更改所需的安静时间量。
spring.devtools.restart.trigger-file =＃特定文件的名称，当更改时，触发重新启动检查。如果未指定，则任何类路径文件更改都会触发重新启动。



##### #remote DEVTOOLS（RemoteDevToolsProperties）

spring.devtools.remote.context-path = /。~~ spring-boot！〜＃用于处理远程连接的上下文路径。
spring.devtools.remote.proxy.host = ＃用于连接远程应用程序的代理主机。
spring.devtools.remote.proxy.port = ＃用于连接远程应用程序的代理端口。
spring.devtools.remote.restart.enabled = true ＃是否启用远程重启。
spring.devtools.remote.secret = ＃建立连接所需的共享密钥（启用远程支持所需）。
spring.devtools.remote.secret头名= X-AUTH-TOKEN ＃用于传输共享密钥的HTTP头。



＃---------------------------------------- 

#### #TESTING PROPERTIES 

＃----- -----------------------------------

spring.test.database.replace = any ＃要替换的现有DataSource的类型。
spring.test.mockmvc.print =默认#MVC 打印选项。