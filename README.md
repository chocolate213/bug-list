# bug-list

## 28. IDEA 中 MyBatis配置文件 SQL标签报错

 Setting -> Editor-> lanuage injections 

 找到ibatis3:sql|select|insert|update|delete，编辑，删除Name里面" sql| "，以及local name里面的"sql|"，apply->ok。

## 27. MyBatis判断集合是否为Null

MyBatis 使用OGNL表达式来对元素进行操作，所以可以使用collection.size()方式获取集合元素大小

``` xml
<if test="indexIdList != null AND indexIdList.size() > 0 ">
                AND i.id in
                <foreach collection="indexIdList" item="indexId" separator="," open="(" close=")">
                    #{indexId}
                </foreach>
</if>
```

## 26. Idea更新失败 Connection Time Out

File -> Appearance & Behavior -> System Settings -> Updates

Uncheck *Use secure connection*

## 25. GB2313 to UTF-8

使用Intellij IDEA转换即可

## 24. 解决jetty报错：请求的操作无法在使用用户映射区域打开的文件上执行
在web.xml文件中添加如下配置：
``` xml
<context-param>  
    <param-name>org.eclipse.jetty.servlet.Default.useFileMappedBuffer</param-name>  
    <param-value>false</param-value>  
</context-param>  
```

## 23. 解决Maven test操作时乱码问题：添加如下插件

``` xml
<!-- 解决maven test命令时console出现中文乱码乱码 -->
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-surefire-plugin</artifactId>
    <version>2.20.1</version>
    <configuration>
        <forkMode>once</forkMode>
        <argLine>-Dfile.encoding=UTF-8</argLine>
    </configuration>
</plugin>
```

## 22. org.apache.tools.ant.taskdefs.SQLExec 从文件读取数据库脚本并执行出现乱码问题
``` java
exec.setEncoding("UTF-8");  //设置读取文件的编码方式
```
## 21. com.fasterxml.jackson.annotation.@JsonFormat标签时区错误问题
使用@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")来进行字符串转日期，会出现转换时区不正确的问题，使用时应手动指定时区：
``` java
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss", timezone = "GMT+8")
```

## 20. Elasticsearch 5.4.0版本客户端报错：java.lang.NoClassDefFoundError: Could not initialize class org.apache.log4j.LogManager
原因：缺少log4j-api依赖
``` xml
<dependency>
    <groupId>org.apache.logging.log4j</groupId>
    <artifactId>log4j-api</artifactId>
    <version>2.6.2</version>
</dependency>
```


## 19. Integer#parseInt VS Integer#valueOf
方法签名：
``` java
public static Integer valueOf(String s);
public static int parseInt(String s);
```
可以看到valueOf返回的是Integer对象，而parseInt返回的是int基本数据类型，有时候对于int的装箱（boxed）并无意义，所以通常情况下还是选用Integer#parseInt


## 18. 常量为什么要同时使用static final修饰: 会提高性能, 但是只对基本数据类型和String类型起作用

链接：https://developer.android.com/training/articles/perf-tips.html#UseFinal

Consider the following declaration at the top of a class:

``` java
  static int intVal = 42;
  static String strVal = "Hello, world!";
```


The compiler generates a class initializer method, called <clinit>, that is executed when the class is first used. The method stores the value 42 into intVal, and extracts a reference from the classfile string constant table for strVal. When these values are referenced later on, they are accessed with field lookups.

We can improve matters with the "final" keyword:

``` java
static final int intVal = 42;
static final String strVal = "Hello, world!";
```

The class no longer requires a <clinit> method, because the constants go into static field initializers in the dex file. Code that refers to intVal will use the integer value 42 directly, and accesses to strVal will use a relatively inexpensive "string constant" instruction instead of a field lookup.

- Note: This optimization applies only to *primitive types and String constants*, not arbitrary reference types. Still, it's good practice to declare constants static final whenever possible

## 17. kafka启动报错：There is insufficient memory for the Java Runtime Environment to continue.
错误原因：kafka默认内存配置为-Xmx1G -Xms1G，如果机器内存小于该配置，kafka将无法完成初始化，导致报错
解决办法：调整kafk启动参数，将虚拟机内存适当调低，调整kafka-server-start.sh配置文件：
-Xmx256m -Xms128m

## 16. SpringMVC @Value标签无法正确注入properties文件键值对问题
@Value标签可将配置文件中的键值对注入到组件中，但是要注意：如果SpringMVC配置文件只扫描Controller标签，那么在@Controller标签注解的类中，只能使用SpringMVC配置文件所配置的properties配置文件（在哪个配置文件中配置的就只能在哪注入）

## 15. JS中Number类型问题
* JS中能表示的整数的最大位数为15位（保证精度），超过15位将会丢失精度：
* JS中小数的最大位数是 17，但是浮点运算并不总是 100% 准确：
例程：
``` javascript
var num = 999999999999999;  //15位之内可以保证精度
console.log(num);
```

如果传给JS的数据为数字类型，且超过15位，需要将**传递的参数类型转换为String类型**，否则会出现数字过大丢失精度，导致与原值不一致的问题。

## 14. Map的keySet以及entrySet方法返回元素顺序问题：
测试用例：
``` java
@Test
    public void testEntrySetOrder3(){
        Map<String, String> map1 = new Hashtable<>();
        map1.put("3","3");
        map1.put("1","1");
        map1.put("2","2");
        map1.put("5","5");
        map1.put("4","4");

        Map<String, String> map2 = new HashMap<>();
        map2.put("3","3");
        map2.put("1","1");
        map2.put("2","2");
        map2.put("5","5");
        map2.put("4","4");

        Map<String, String> map3 = new LinkedHashMap<>();
        map3.put("3","3");
        map3.put("1","1");
        map3.put("2","2");
        map3.put("5","5");
        map3.put("4","4");

        Map<String, String> map4 = new TreeMap<>();
        map4.put("3","3");
        map4.put("1","1");
        map4.put("2","2");
        map4.put("5","5");
        map4.put("4","4");

        Map<String, String> map5 = new ConcurrentHashMap<>();
        map5.put("3","3");
        map5.put("1","1");
        map5.put("2","2");
        map5.put("5","5");
        map5.put("4","4");

        System.out.println("HashTable : " + map1.entrySet());
        System.out.println("HashMap : " + map2.entrySet());
        System.out.println("LinkedHashMap : " + map3.entrySet());
        System.out.println("TreeMap : " + map4.entrySet());
        System.out.println("ConcurrentHashMap : " + map5.entrySet());
    }
```

测试结果：

* JDK 1.7 下输出结果（与JDK1.6下输出结果相同）：
``` text
HashTable : [5=5, 4=4, 3=3, 2=2, 1=1]
HashMap : [3=3, 2=2, 1=1, 5=5, 4=4]
LinkedHashMap : [3=3, 1=1, 2=2, 5=5, 4=4]
TreeMap : [1=1, 2=2, 3=3, 4=4, 5=5]
ConcurrentHashMap : [1=1, 5=5, 3=3, 4=4, 2=2]
```

* JDK 1.8 下输出结果：
``` text
HashTable : [5=5, 4=4, 3=3, 2=2, 1=1]
HashMap : [1=1, 2=2, 3=3, 4=4, 5=5]
LinkedHashMap : [3=3, 1=1, 2=2, 5=5, 4=4]
TreeMap : [1=1, 2=2, 3=3, 4=4, 5=5]
ConcurrentHashMap : [1=1, 2=2, 3=3, 4=4, 5=5]
```

结论：
* JDK1.7（1.6）中，keySet方法以及entrySet方法将会按照如下顺序排序：
> 1. HashTable: 按照key降序排序；
> 2. HashMap: 按照key的某种顺序排序（无论put元素的顺序如何，都会按照某种顺序排序），但是既不是升序，也不是降序，也不是原序；
> 3. LinkedHashMap: 按照key原序排序；
> 4. TreeMap：按照key升序排序；
> 5. ConcurrentHashMap：按照key的某种顺序排序（无论put元素的顺序如何，都会按照某种顺序排序），但是与HashMap的排序方式不同。

* JDK1.8中，keySet方法以及entrySet方法将会按照如下顺序排序：
> 1. HashTable: 按照key降序排序；
> 2. HashMap: 按照key升序排序（**与JDK1.7不同**）；
> 3. LinkedHashMap: 按照key原序排序；
> 4. TreeMap：按照key升序排序；
> 5. ConcurrentHashMap：按照key升序排序（**与JDK1.7不同**）。

## 13. Java使用mysql-jdbc连接MySQL出现如下警告：
  > Establishing SSL connection without server's identity verification is not recommended. According to MySQL 5.5.45+, 5.6.26+ and 5.7.6+ requirements SSL connection must be established by default if explicit option isn't set. For compliance with existing applications not using SSL the verifyServerCertificate property is set to 'false'. You need either to explicitly disable SSL by setting useSSL=false, or set useSSL=true and provide truststore for server certificate verification.
  
  原因是MySQL在高版本需要指明是否进行SSL连接。在mysql连接字符串url中加入ssl=true或者false即可:

  ``` url
   url=jdbc:mysql://127.0.0.1:3306/framework?characterEncoding=utf8&useSSL=true 
  ```

## 12. 使用Maven默认构建的web.xml文件将会导致jsp页面中无法读取requestScope中的内容。解决办法：将web.xml的声明替换为如下代码(这种方式可同时处理idea中Servlet等元素报错的问题)：

  ``` xml
  <?xml version="1.0" encoding="UTF-8"?>
  <web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
         id="WebApp_ID" version="2.5">
         ...
  <web-app/>
  ```

## 11. 编译时报错：

 `Error:java: javacTask: source release 8 requires target release 1.8`

 解决办法：在pom.xml文件中做出如下配置：
 
 ``` xml
    <build>
       ...
       <plugins>
            <!-- 指定Maven编译条件 否则会出现：
              Error:java: javacTask: source release 8 requires target release 1.8
             -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>1.8</source>
                    <target>1.8</target>
                </configuration>
            </plugin>
        </plugins>
    </build>
 ```
    
## 10. Maven编译war文件时会忽略类路径下的*.xml(mapper.xml)文件导致出现如下异常（原因：没有找到映射文件，编译时在target文件夹中没有mapper.xml文件）：
    
    `Mapped Statements collection does not contain value for ...`
    
解决办法：在pom.xml中配置资源文件：
    
    ``` xml 
    <build>
        <!-- 指定要编译到target目录下的资源文件位置 -->
        <resources>
            <!-- 将src/main/java路径下的所有xml、properties文件添加到target中 -->
            <resource>
                <directory>src/main/java</directory>
                <includes>
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>false</filtering>
            </resource>
            <!-- 将src/main/resources路径下的所有xml、properties文件添加到target中 -->
            <resource>
                <directory>src/main/resources</directory>
                <includes>
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                </includes>
                <filtering>false</filtering>
            </resource>
        </resources>
        ...
    <build/>
    ```
    
## 9. 如何查找依赖包
    
    链接地址：[http://mvnrepository.com/](http://mvnrepository.com/ "mvnrepository")
    
## 8. Spring容器只支持一个<context:property-placeholder/>配置，而且只会读取到第一个配置文件，其他的会被忽略。

解决办法：使用通配符导入配置文件
``` xml
    <context:property-placeholder location="classpath*:conf/conf*.properties"/>  
```

## 7. 使用Gson与服务器交互时间数据时，解决时间数据传递问题：
首先Gson生成时间需要格式化成yyyy-MM-dd HH:mm:ss的格式：

``` java
Gson gson = new GsonBuilder()
                .setDateFormat("yyyy-MM-dd HH:mm:ss")
                .create();
```

然后在服务端采用如下方法转换日期格式：

``` java
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date birthday;
```

如果采用RequestBody来传递参数，就不能使用Spring提供的@DatatimeFormat注解来格式化日期格式，需要使用Jackson提供的
@JsonFormat标签来格式化时间

在JavaBean中,可以同时指定这两个注解，用于分别解决表单提交的Data类型数据和Json方式提交的Date类型数据的格式化问题：
``` java
@DateTimeFormat(pattern = "yyyy/MM/dd HH;mm;ss")
@JsonFormat(pattern = "yyyy-MM-dd HH:mm:ss")
private Date birthday;
```
提交的Json对象中的Date数据：
``` json
{"birthday":"2016-11-05 13:13:13","id":0,"name":"张三","salary":1000.0}
```
表单中的Date数据
``` text
birthday:2016/12/20 13;13;13
```
这样服务器接收到的时间信息就可以被正确解析

## 6. 解决Post请求中的中文乱码问题：
在web.xml文件中配置过滤器：
``` xml
    <!-- 解决post请求参数乱码问题 -->
    <filter>
        <filter-name>characterEncodingFilter</filter-name>
        <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
        <init-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </init-param>
        <init-param>
            <param-name>forceEncoding</param-name>
            <param-value>true</param-value>
        </init-param>
    </filter>
    <filter-mapping>
        <filter-name>characterEncodingFilter</filter-name>
        <url-pattern>/*</url-pattern>
    </filter-mapping>
```

## 5. 解决Get请求中文乱码问题：在Tomcat配置文件的Connector节点配置如下参数

``` xml
<Connector port="8080" protocol="HTTP/1.1"
               connectionTimeout="20000"
               redirectPort="8443"
               URIEncoding="UTF-8" />
```
idea中需要去掉Tomcat配置中的VM Options中的参数：-Dfile.encoding=UTF-8

## 4. 解决@ResponseBody参数响应乱码问题

``` xml
    <!-- 开启注解 @RequestBody、@ResponseBody（） -->
    <mvc:annotation-driven>
        <!-- 解决@ResponseBody返回中文参数乱码问题 -->
        <mvc:message-converters register-defaults="true">
            <bean class="org.springframework.http.converter.StringHttpMessageConverter">
                <property name="supportedMediaTypes" value="text/html;charset=UTF-8"/>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
```

## 3. 引入Log4J2后控制台报错
``` text
    SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
```

解决办法： Placing one (**and only one**) of slf4j-nop.jar, slf4j-simple.jar, slf4j-log4j12.jar, slf4j-jdk14.jar or logback-classic.jar on the class path should solve the problem.

``` xml
    <dependency>
        <groupId>org.slf4j</groupId>
        <artifactId>slf4j-log4j12</artifactId>
        <version>1.7.21</version>
    </dependency>
```

## 2. Servlet Api与JSP支持包由Servlet容器（Tomcat）提供，所以在pom.xml文件中不用再次提供该文件，所以在配置文件中将其编译范围指定为provide（已提供）

``` xml
        <dependency>
            ...
            <scope>provided</scope>
        </dependency>

        <!-- JSP -->
        <dependency>
            ...
            <scope>provided</scope>
        </dependency>
```

## 1. 是否声明HTML文档的doctype将会对HTML文档样式造成影响，所以尽量声明HTML文档的DOCTYPE:
``` html
<!DOCTYPE html>
</html>
...
</html>
```



